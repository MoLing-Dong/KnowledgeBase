# [油猴开发指南]关于脚本如何处理 iframe 的碎碎念

## 这篇文章详细探讨了如何在 Tampermonkey（油猴）脚本中处理 iframe 与主页面之间的通信问题，主要分为同源和跨域两种情况

### 1. 同源 iframe

在同源情况下，主页面和 iframe 之间的交互比较简单。你可以直接通过 `iframe.contentWindow` 获取 iframe 的 `window` 对象，通过 `iframe.contentDocument` 获取 `document` 对象，进而操作 iframe 内部的元素。关键点在于：iframe 和主页面的加载顺序可能不同步，所以通常需要监听 iframe 的 `load` 事件，确保在 iframe 完全加载后再进行 DOM 操作。文中还提供了一个封装的 `getIframeDocument` 函数，简化了处理逻辑。

### 2. 跨域 iframe

跨域的情况更加复杂，无法直接通过 `contentWindow` 或 `contentDocument` 操作 iframe 内的元素。这是由于浏览器的安全限制，阻止了跨域之间的直接 DOM 操作。为了实现跨域通信，文中提供了两种主要方法：`postMessage` 和 `GM_addValueChangeListener`。

- **postMessage**:
  `postMessage` 允许不同源的页面通过消息传递进行通信。它需要两个参数：目标窗口引用（即 `contentWindow` 或 `window.top`）和消息内容。`targetOrigin` 用于指定消息接收方的域名，可以使用通配符 `'*'` 来放宽限制。通过在主页面和 iframe 中监听 `message` 事件，可以实现信息的传递与处理。文章还特别提到，iframe 的脚本执行时机与主页面不同步，通常需要 iframe 首先向主页面发送消息，告诉主页面它已经准备好接收消息。

- **GM_addValueChangeListener**:
  这是油猴脚本提供的一个 API，它通过监听全局存储（`GM_setValue`）的变化来实现不同脚本实例之间的通信。虽然理论上它也可以用于主页面与 iframe 之间的通信，但其局限性较大，尤其是在多 iframe 或跨标签页的场景下，很难准确区分消息的来源和目标。因此，作者并不推荐在复杂场景下使用这种方法。

### 3. 框架嵌套的问题

文章还提到，`window.frames` 只能获取页面的第一级子 iframe，而对于多级嵌套 iframe，无法递归获取。解决这个问题的思路是由 iframe 主动向主页面发送消息，主页面收到后逐个回应。这种双向通信机制利用了 `postMessage` 的 `e.source` 来标识消息来源窗口。

### 4. 特殊情况：跨标签页的通信

`postMessage` 也可以用于跨标签页的通信，但前提是新标签页是通过 `window.open` 打开的。在这种情况下，`window.open` 返回的新标签页的 `window` 对象可以作为目标窗口，通过 `postMessage` 实现通信。这个方案同样需要注意加载时机的问题，建议由新标签页向主页面先发消息，确保通信过程不丢失。

### 总结

- **同源 iframe**：可以直接通过 `contentWindow` 和 `contentDocument` 操作 iframe 内部元素。
- **跨域 iframe**：使用 `postMessage` 进行消息传递，确保跨域通信的安全性与可靠性。`GM_addValueChangeListener` 虽然可以用作替代，但其局限性使得在复杂场景中不适用。
- **加载时机**：无论是 `iframe` 还是跨标签页通信，都需要注意加载时机，通常由后加载的一方先发出消息。

通过以上方法，我们可以灵活处理 iframe 与主页面之间的交互，既能应对简单的同源情况，也能解决复杂的跨域通信问题。
