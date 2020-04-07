$$PWA$$
简介：
- 拥有原生应用属性的web应用
特点：
- 可靠——即时加载，即使在不确定的网络条件下也不会受到影响。
  - 当用户打开我们站点时（从桌面 icon 或者从浏览器），通过**Service Worker** 能够让用户在网络条件很差的情况下也能瞬间加载并且展现。
- 快速——页面展现之后，用户期望有平滑的体验，过渡动画和快速响应。
- 粘性 - 像设备上的原生应用，具有沉浸式的用户体验，用户可以添加到桌面
  - PWA 是可以安装的，用户点击安装到桌面后，会在桌面创建一个 PWA 应用，并且不需要从应用商店下载
  -  可以借助 Web App Manifest 提供给用户和 Native App 一样的沉浸式体验
  -  可以通过给用户发送离线通知，让用户回流
> Web App Manifest 允许开发者控制 PWA 添加到桌面，允许定制桌面图标、URL等等。

#### Service Worker
背景
- web worker不具有缓存能力

service workergg功能：
- 一个独立的 worker 线程，独立于当前网页进程，有自己独立的 worker context。
- 一旦被 install，就永远存在，除非被手动 unregister
- 用到的时候可以直接唤醒，不用的时候自动睡眠
- 可编程拦截代理请求和返回，缓存文件，缓存的文件可以被网页进程取到（包括网络离线状态）
- 离线内容开发者可控
- 能向客户端推送消息
- **不能直接操作 DOM**
- 必须在 `HTTPS` 环境下才能工作
- 异步实现，内部大都是通过 Promise 实现
使命：让缓存做到优雅和极致，让 Web App 相对于 Native App 的缺点弱化

#### 怎么使用？在什么场景下使用什么 API ？
前提条件
- 要求 HTTPS 的环境
- Service Worker 的缓存机制是依赖 Cache API 实现的
- 依赖 HTML5 fetch API
- 依赖 Promise 实现

注册
- 在 js 主线程（常规的页面里的 js ）注册 Service Worker 来启动安装
    ```js
    if ('serviceWorker' in navigator) {
    window.addEventListener('load', function () {
        navigator.serviceWorker.register('/sw.js', {scope: '/'})
            .then(function (registration) {

                // 注册成功
                console.log('ServiceWorker registration successful with scope: ', registration.scope);
            })
            .catch(function (err) {

                // 注册失败:(
                console.log('ServiceWorker registration failed: ', err);
            });
    });
    }
    ```
- 先判断Service Worker API 的可用情况
- 在页面 onload 的时候注册位于 /sw.js 的 Service Worker。
- 页面加载成功后，就会调用 register() 方法，浏览器将会判断 Service Worker 线程是否已注册并做出相应的处理。
-  scope 参数是可选的，用于指定你想让 Service Worker 控制的内容的子目录
   -  Service Worker 线程将接收 scope 指定网域目录上所有事项的 fetch 事件，如果我们的 Service Worker 的 javaScript 文件在 /a/b/sw.js， 不传 scope 值的情况下, scope 的值就是 /a/b。

#### 查看是否注册成功

#### 注册失败的原因
- Service Worker 文件的地址没有写对，需要相对于 origin。
- 没有service worker的运行环境要求
- Service Worker 文件在不同的 origin 下而不是你的 App 的，这是不被允许的。

#### 自定义请求响应
on install
on fetch

#### service worker生命周期
Service Worker 生命周期
> Service Worker 的使用过程很简单，所处理的事情也相对单一，我们基本上需要做的就是利用这个 API 做好站点的缓存策略在页面脚本中注册 Service Worker 文件所在的 URL。Worker 就可以开始激活了，激活后的 Service Worker 可以监听当前域下的功能性事件，比如资源请求（fetch）、推送通知（push）、后台同步（sync）
**生命周期全览**
- 在 JavaScript 主线程中使用 serviceWorkerContainer.register() 来注册 Service Worker 
  - 在注册的过程中，浏览器会在后台启动尝试 Service Worker 的安装步骤
- 注册成功后，Service Worker 在 ServiceWorkerGlobalScope 环境中运行
  - 这是一个特殊的 worker context，与主脚本的运行线程相独立，同时也没有访问 DOM 的能力。
- 后台开始安装步骤， 通常在安装的过程中需要缓存一些静态资源
  - 如果所有的资源成功缓存则安装成功，如果有任何静态资源缓存失败则安装失败，在这里失败的不要紧，会自动继续安装直到安装成功，
  - 如果安装不成功无法激活 Service Worker。
- 开始激活 Service Worker，必须要在 Service Worker 安装成功之后，才能开始激活步骤，当 Service Worker 安装完成后，会接收到一个激活事件（activate event）
  - 激活事件的处理函数中，主要操作是清理旧版本的 Service Worker 脚本中使用资源。
- 激活成功后 Service Worker 可以控制页面了，但是只针对在成功注册了 Service Worker 后打开的页面
  - *页面打开时有没有 Service Worker，决定了接下来页面的生命周期内受不受 Service Worker 控制。所以，只有当页面刷新后，之前不受 Service Worker 控制的页面才有可能被控制起来。*

install 事件回调中有两个方法：
activate 回调中有两个方法：

有几个重要的功能性的的事件
- fetch (请求)：当浏览器在当前指定的 scope 下发起请求时，会触发 fetch 事件，并得到传有 response 参数的回调函数，回调中就可以做各种代理缓存的事情了。
- push (推送)：push 事件是为推送准备的。不过首先需要了解一下 Notification API 和 PUSH API。通过 PUSH API，当订阅了推送服务后，可以使用推送方式唤醒 Service Worker 以响应来自系统消息传递服务的消息，即使用户已经关闭了页面。
- sync (后台同步)：sync 事件由 background sync (后台同步)发出。background sync 配合 Service Worker 推出的 API，用于为 Service Worker 提供一个可以实现注册和监听同步处理的方法。
  - 但它还不在 W3C Web API 标准中。在 Chrome 中这也只是一个实验性功能，需要访问 chrome://flags/#enable-experimental-web-platform-features ，开启该功能，然后重启生效。


- caches：
  - 为缓存的 Request / Response  对象对提供存储机制
  - 作为ServiceWorker 生命周期的一部分。请注意，Cache 接口像 workers 一样，是暴露在 window 作用域下的
  - 一个域可以有多个命名 Cache 对象
  - 你需要在你的脚本 中处理缓存更新的方式。（过期）
  - 需要定期地清理缓存条目，因为每个浏览器都硬性限制了一个域下缓存数据的大小。
- API：
  - Cache.match(request, options)
    - 返回一个 Promise对象，resolve的结果是跟 Cache 对象匹配的第一个已经缓存的请求。
  - Cache.matchAll(request, options)
    - 返回一个Promise 对象，resolve的结果是跟Cache对象匹配的所有请求组成的数组。
  - Cache.add(request)
    - 抓取这个URL, 检索并把返回的response对象添加到给定的Cache对象.这在功能上等同于调用 fetch(), 然后使用 Cache.put() 将response添加到cache中.
  - Cache.addAll(requests)
    - 抓取一个URL数组，检索并把返回的response对象添加到给定的Cache对象。
  - Cache.put(request, response)
    - 同时抓取一个请求及其响应，并将其添加到给定的cache。
  - Cache.delete(request, options)
    - 搜索key值为request的Cache 条目。如果找到，则删除该Cache 条目，并且返回一个resolve为true的Promise对象；如果未找到，则返回一个resolve为false的Promise对象。
  - Cache.keys(request, options)

*浏览器在后台独立于网页运行的脚本，它打开了通向不需要网页或用户交互的功能的大门。*
- 一种 JavaScript Worker，无法直接访问 DOM。Service Worker 通过响应 postMessage 接口发送的消息来与其控制的页面通信，页面可在必要时对 DOM 执行操作
- 一种可编程网络代理，让您能够控制页面所发送网络请求的处理方式
- 在不用时会被中止，并在下次有需要时重启，因此，您不能依赖 Service Worker onfetch 和 onmessage 处理程序中的全局状态。
- 

核心功能：
- Web App Manifest
- Service Worker
- Cache API 缓存
- Push&Notification 推送与通知
- Background Sync 后台同步
- 响应式设计


应用架构：
- 渲染网站主要有两种方法 - 在服务器上或在客户端上
- 服务器端渲染（SSR）
  - 意味着网站在服务器上渲染，因此它提供了更快的首次加载，但在页面之间导航需要每次都下载所有内容
  - 但加载速度不行，每个页面都需要新的服务器往返
- 客户端渲染（CSR）：
  - 允许在导航到不同页面时几乎立即在浏览器中更新网站，但在开始时需要更多的初始下载和客户端上的额外渲染
  - 首次访问时网站速度较慢，但后续访问速度要快得多。
>将SSR与CSR混合可以获得最佳结果 - 您可以在服务器上渲染网站，缓存其内容，然后在客户端需要时更新渲染。 由于SSR，第一页加载很快，并且页面之间的导航是平滑的，因为客户端可以仅使用已更改的部分重新渲染页面。

- App shell:
  - 意图尽快加载最小的用户界面，然后缓存它，以便在后续访问时可以离线使用，然后加载应用程序的所有内容。
  - 下次有人从设备访问应用程序时，UI立即从缓存加载，并从服务器请求新内容
  - 该网站感觉就像一个原生应用，具有即时交互和可靠的性能，同时保留了网络的所有好处。
   
- app shell 方法允许网站:
  - 可链接：即使它的行为类似于原生应用，它仍然是一个网站 - 您可以单击页面中的链接和当您想共享它时向某人发送URL。
  - 渐进式：从“好用的旧的基本网站”开始，逐步添加新功能，同时记住检测它们是否在浏览器中可用，并优雅地处理在没有支持的情况下出现的任何错误。
    - *如，离线模式下 service workers 的帮助只是一个额外的特性使网站体验更好，但没有它网站应仍然是完全可用的。*
  - 响应式网页设计也适用于渐进式网络应用程序，因为它们主要用于移动设备。 有许多不同的设备与浏览器 - 使您的网站适用于不同的屏幕尺寸，视口或像素密度，使用 viewport meta tag，CSS media queries，Flexbox，和 CSS Grid 等技术是很重要的。





#### Service Worker
*浏览器在后台独立于网页运行的脚本，它打开了通向不需要网页或用户交互的功能的大门。*
- 一种 JavaScript Worker，无法直接访问 DOM。Service Worker 通过响应 postMessage 接口发送的消息来与其控制的页面通信，页面可在必要时对 DOM 执行操作
- 一种可编程网络代理，让您能够控制页面所发送网络请求的处理方式
- 在不用时会被中止，并在下次有需要时重启，因此，您不能依赖 Service Worker onfetch 和 onmessage 处理程序中的全局状态。
- 