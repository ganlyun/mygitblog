#  localStorage vs sessionStorage



stack overflow上有个千赞答案，原文如下

[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) and [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) both extend [Storage](https://developer.mozilla.org/en-US/docs/Web/API/Storage). There is no difference between them except for the intended "non-persistence" of `sessionStorage`.

That is, the data stored in `localStorage` **persists until explicitly deleted**. Changes made are saved and available for all current and future visits to the site.

For `sessionStorage`, **changes are only available per tab.** Changes made are saved and available for the current page **in that tab** until it is closed. Once it is closed, the stored data is deleted.



主要说了两个区别点：

* 持久化
* 共享范围



## 持久化

localStorage 除非用户删除，否则一直存在；sessionStorage 随会话关闭而销毁。

看表象的话，localStorage 浏览器关闭也不会被销毁，sessionStorage 则会在浏览器关闭以后就被销毁（关闭页签不会，重新打开还是存在）（基于chrome浏览器123测试）

**与会话超时无关**：`sessionStorage` 的生命周期与服务器端会话的超时无关。即使服务器端的会话没有超时，关闭浏览器也会清除 `sessionStorage` 中的数据。

## 共享范围

localStorage 同域共享；sessionStorage页签之间隔离。

sessionStorage 有个特殊场景会共享，就是window.open或a标签打开了新页签，新页签会复制父页签也就是opener的 sessionStorage 进行初始化，但是后续的 change 不会同步

在MDN中是这么描述的

> **在新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文，这点和 session cookie 的运行方式不同。**

顶级浏览会话就是指openner

>  **当window.open或者a标签使用了`noopener`的参数以后，这个行为就会失效**，依此类推，a标签rel="noopener"也有一样的效果

## 参考内容

### 会话的生命周期

1. **会话初始化（创建）**：
   - 当用户首次访问某个Web应用时，服务器通常会创建一个新的会话。这个过程中，服务器会生成一个唯一的会话标识符（Session ID），并将其返回给客户端。
2. **客户端接收Session ID**：
   - 客户端（浏览器）接收到Session ID后，通常将其保存在cookie中，或者在每个后续请求中作为URL参数或请求头的一部分发送。
3. **会话存储**：
   - 服务器使用Session ID来识别和存储与该会话关联的信息，如用户登录状态、购物车内容、用户偏好设置等。
4. **会话超时**：
   - 服务器会为每个会话设置一个超时时间（或称为“过期时间”）。如果在这个时间内没有任何活动，会话将自动结束。
5. **会话活动**：
   - 用户与服务器之间的交互（如点击链接、提交表单等）会被视为会话活动，这些活动会重置会话的超时计时器。
6. **会话结束**：
   - 用户可以显式地结束会话，如通过注销功能。在注销时，服务器会销毁与Session ID关联的所有信息。
   - 如果用户简单地关闭浏览器或导航到其他网站，会话可能不会立即结束，直到超时时间结束。
   - 当服务器基于安全策略发现了异常的请求行为，会强制结束会话
7. **会话销毁**：
   - 一旦会话结束，服务器会停止跟踪Session ID，并可能从内存或数据库中删除会话数据。

### cookie的生命周期

1. **创建**： 当用户访问一个网站时，服务器可以发送一个 Set-Cookie HTTP 响应头来在客户端创建一个 Cookie。
2. **存储**： 浏览器接收到 Cookie 后，会根据 Cookie 的属性将其存储在浏览器的 Cookie 存储中。
3. **属性**： Cookie 有几个关键属性定义其生命周期：
   - `Expires` 或 `Max-Age`：指定 Cookie 的有效期。`Expires` 设置具体日期，而 `Max-Age` 设置从创建时起的秒数。
   - `Domain` 和 `Path`：定义了 Cookie 的作用域，即哪些路径和域名下的请求会携带这个 Cookie 发送给服务器。
   - `Secure`：指示 Cookie 只能在加密（HTTPS）连接中传输。
   - `HttpOnly`：指示 Cookie 不能被 JavaScript 访问，增加了安全性。
4. **会话期 Cookie**： 如果 Cookie 没有设置 `Expires` 或 `Max-Age` 属性，它就是一个会话期 Cookie，这种 Cookie 在用户关闭浏览器时会被自动删除。
5. **持久性 Cookie**： 设置了 `Expires` 或 `Max-Age` 属性的 Cookie 是持久性的，它们会在指定的时间后过期，或者直到用户手动清除它们。
6. **过期和删除**： 当 Cookie 达到 `Expires` 指定的日期或 `Max-Age` 指定的时间长度后，它就会过期并从浏览器的 Cookie 存储中删除。
7. **服务器控制**： 服务器可以通过发送带有 `Expires` 设置为过去日期的 Set-Cookie 头，或调用 `Max-Age` 设置为 0 的 Set-Cookie 头来显式地删除客户端上的 Cookie。
8. **客户端控制**： 用户或浏览器设置也可以影响 Cookie 的生命周期，如使用隐私模式浏览或手动清除浏览器的 Cookie 存储。
9. **第三方 Cookie**： 来自非访问网站的 Cookie 被称为第三方 Cookie，出于隐私和安全考虑，许多现代浏览器限制或完全禁止了第三方 Cookie。
10. **安全性**： 由于 Cookie 可以包含敏感信息，因此它们的安全性非常重要。使用 `Secure` 和 `HttpOnly` 属性可以提高 Cookie 的安全性。

