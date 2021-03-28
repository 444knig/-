fiber尝试解决的问题：

fiber的特性：
核心目标：扩大其适用性，包括动画，布局和手势。分为5个具体目标
- 把可中断的工作拆分成小任务
- 对正在做的工作调整优先次序、重做、复用上次（做了一半的）成果
- 在父子任务之间从容切换（yield back and forth），以支持React执行过程中的布局刷新
-支持render()返回多个元素

关键特性：
- 增量渲染（把渲染任务拆分成块，匀到多帧）
- 更新时能够暂停，终止，复用渲染任务
- 给不同类型的更新赋予优先级
- 并发方面新的基础能力


更好地支持error boundary

fiber和fiber tree
React运行时有三种实例
- DOM 真实DOM
- Instance react维护的v-dom tree node
- Elements 描述UI长什么样子（type, props） React。Element

fiber后：
- DOM：真实DOM
- effect：每个workInProgress tree节点上都有一个effect list，用来存放diff结果，当前节点更新完毕会向上merge effect list（queue收集diff结果）
- workInProgress： workInProgress tree是reconcile过程中从fiber tree建立的当前进度快照，用于断点恢复  这个数据结构的构造实际上就是diff的过程  通过`requestIdleCallback`来调度
- fiber：fiber tree和dom tree 类似，用来描述增量更新所需的上下文信息
- Elements：描述UI长什么样子

实际上 workInprogress tree就是新的fiber tree，双缓冲，能复用内部fiber对象、节省内存和GC的开销

fiber tree 单链表
reconcile过程分为2个阶段
- （可中断）render/reconciliation 通过构造workInProgress tree得出change
- （不可中断）commit 应用这些DOM change

算法流程：
1. 如果当前节点不需要更新，直接把子节点clone过来，跳到5；要更新的话打个tag
2. 更新当前节点状态（`props, state, context`等）
3. 调用`shouldComponentUpdate()`，`false`的话，跳到5
4. 调用`render()`获得新的子节点，并为子节点创建fiber（创建过程会尽量复用现有fiber，子节点增删也发生在这里）
5. 如果没有产生child fiber，该工作单元结束，把effect list归并到return，并把当前节点的sibling作为下一个工作单元；否则把child作为下一个工作单元
6. 如果没有剩余可用时间了，等到下一次主线程空闲时才开始下一个工作单元；否则，立即开始做
7. 如果没有下一个工作单元了（回到了workInProgress tree的根节点），第1阶段结束，进入pendingCommit状态  出口


高优先级的使用：requestAnimationFrame来调度
低优先级的使用：requestIdleCallback回调执行

3. 如何调度
工作循环中每次处理一个任务：
- 判断：看一帧的时间用完没，没得话继续处理，否则还给主线程，用`requestIdleCallback`回调再接着做


1. 如何中断/断点恢复？
中断：检查当前正在处理的工作单元，保存当前成果（workInProgress firstEffect, lastEffect），修改tag标记一下，迅速收尾并再开一个requestIdleCallback，下次有机会再做