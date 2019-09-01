作者后端经验比较丰富，近期要做跨域跨前端框架的前端页面展示，自然联想到了 IFRAME 方法，细致了解下来发现它可以用来解决很多棘手问题，包括：
* 跨域问题
* Ajax 前进后退问题
* 异步上传问题
* 跨框架问题

# 父页面
## 基础 React 框架
```js
import React, { PureComponent } from 'react';

export default class Iframe extends PureComponent {

    render () {
        return (
            <div>
                <h1>Parent</h1>
                <p>Send Message 
                    <button id="message_button" onClick={this.handleParentClick.bind(this)}>Hi child</button>
                </p>
                <span> Show Message </span>
                <div id='results'></div>
            </div>
        );
    }
}
```  

`import` 和 `export` 外部都是 React 基本框架，内部显示了页面布局：  

![Parent](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190830134026.png)  
`Parent` 提示当前部分是父页面；
`Send Message` 和 `Hi child` 按钮用来向 iframe 传递消息，按钮绑定了点击事件 `handleParentClick`，后文会定义；
`Show Message` 和 `results div` 用来展示 iframe 传来的消息，后文会做处理。

## iframe 相关事件  

``` js
export default class Iframe extends PureComponent {

    receiveMessageFromIframe ( event ) {
        // React will send message started with setImmediate
        if (!event.data.startsWith('setImmediate')){  
          console.log("parent received", event.data);
          const results = document.getElementById('results');
          results.innerHTML = event.data;
        }
    }

    componentDidMount () {
         // "message" name cannot be changed
        window.addEventListener("message", this.receiveMessageFromIframe, false); 
    }

    handleParentClick = () => {
        //必须是iframe加载完成后才可以向子域发送数据
        const childFrameObj = document.getElementById('myFrame');
        childFrameObj.contentWindow.postMessage("This is parent", '*');
        console.log("iframe height", childFrameObj.contentWindow.document.body.offsetHeight);
        childFrameObj.height = childFrameObj.contentWindow.document.body.offsetHeight + 60;  // there will be some margin and stylistic height
        console.log("iframe window", childFrameObj.contentWindow);
        console.log("iframde document", childFrameObj.contentWindow.document);
        console.log("iframe html", childFrameObj.contentWindow.document.documentElement);
    }
    
    render () {
        ...
    }
}
```

* `handleParentClick` 函数是处理 `Hi child` 按钮点击事件的：
    1. 先从 DOM 中获得名字为 `myFrame` 的节点（后文 iframe 部分会展示）。
    2. 通过 `contentWindow.postMessage` 方法向 iframe 传递信息，参数有两个，第一个是传递的信息内容（`This is parent`），另一个是 `targetOrigin`，即目标域，`*` 表示当前域，也可以写成 `http://baidu.com` 等其他域名，但注意 iframe 的域必须和这个域名一致。
    3. 后面 2 行通过 `contentWindow.document.body.offsetHeight` 获得 iframe 页面高度，方便后续做自适应调整，`+60` 因为样式上会产生其他的高度，这里是作为 buffer，实际使用中根据需要调整。
    4. 后面 3 行通过打印的方式展现常用可以获取的 iframe 信息，包括 `window`, `document`, `html` 等。

* `componentDidMount` 函数是在页面加载后执行，React 的典型语法，这里增加了 parent 对 iframe 的事件监听。 
    ``` js
    window.addEventListener("message", this.receiveMessageFromIframe, false); 
    ```
    第一个参数不能修改，事件名称是 `message`，第二个参数是 `eventHandler`，这里使用外面定义的 `receiveMessageFromIframe` 方法，第三个参数是默认值。


* `receiveMessageFromIframe` 函数是事件监听的处理函数，注意 React 也会使用 message 数据驱动，所以这里有过滤 message 以 `setImmediate` 开头的信息，以捕获 iframe 真正传输的数据。获得数据后在展示区 `results div` 添加获得的信息。  

# iframe 页面
```js
export default class Iframe extends PureComponent {
    render () {
        return (
             <div>
                ...
                <iframe
                  id="myFrame"
                  width="100%"
                  height={0}
                  frameBorder={1}
                  scrolling="no"
                  srcDoc="
                  <h1>myFrame</h1>
                  <p>Send Message: <button id='message_button'}>Hi parent</button></p>
                  <span>Show Message</span>
                  <div id='results'></div>
                  <script>
                      function receiveMessageFromParent ( event ) {
                        let results = document.getElementById('results');
                        results.innerHTML += event.data + '<br>';
                      };
                      window.addEventListener('message', receiveMessageFromParent, false);
        
                      let messageButton = document.getElementById('message_button');
                      /* console.log('msg button', messageButton); annoataion cannot use \/\/  */
                      messageButton.addEventListener('click', function (e) {
                        console.log('iframe send msg');
                        console.log('parent', window.parent);
                        console.log('top container', window.top);
                        window.parent.postMessage('This is child', '*');
                      }, false);
                  </script>
                  "
                />
             </div>
        );
    }
}
```  
## 原生 html 页面
`id` 是 DOM 上节点名称，上文有使用这里的 `myFrame` 来获得节点进行操作；
`width="100%"` 是 iframe 宽度，可以是百分比或者数值，百分比可以自适应页面；
`frameBorder={1}` 是 iframe 边框是否展示，1 代表展示，0 代表不展示；
`scrolling="no"` 是滚动设置，yes 代表展示，no 代表不展示，auto 代表自动识别；
`height={0}` 是 iframe 高度，可以是百分比或者数值，这里设置为 0 代表无内容不展示，有内容后上文根据内容高度自适应展示；
`srcDoc=<string>` 是 iframe 的 DOM，可以使用原生 HTML 编写；
`src` 是 iframe 的信息源。  
`srcDoc` 内设置了 iframe 页面：
1. `myFrame` 提示当前是 iframe 页面；
2. `Send Message` 和 `Hi parent` 是信息传输部分；
3. `Show Message` 和 `results` 用来展示从 parent 接受到的信息。

## js 控制脚本
* `receiveMessageFromParent` 函数用来控制从 parent 接受到信息后展示在展示区；
* `window.addEventListener` 用来初始化事件监听，和 parent 的事件监听机制一样，不赘述；
* `messageButton` 获取了当前组件的点击按钮；
* `messageButton.addEventListener` 在点击按钮上绑定了点击事件，注意点击事件名称不可修改，是 `click`，向 parent 传递消息使用 `window.parent.postMessage`，和 parent 的 postMessage 参数一致，不赘述。
* 注意：iframe 使用的原生 html 页面注释不能使用 `//` 方法，只能使用 `/* */` 完成。  

# 整体代码和效果
```js
import React, { PureComponent } from 'react';

export default class Iframe extends PureComponent {

  receiveMessageFromIframe ( event ) {
    if (!event.data.startsWith('setImmediate')){  // React will send message started with setImmediate
      console.log("parent received", event.data);
      const results = document.getElementById('results');
      results.innerHTML = event.data;
    }
  }

  componentDidMount () {
    window.addEventListener("message", this.receiveMessageFromIframe, false);  // "message" name cannot be changed
  }

  handleParentClick = () => {
    //必须是iframe加载完成后才可以向子域发送数据
    const childFrameObj = document.getElementById('myFrame');
    childFrameObj.contentWindow.postMessage("This is parent", '*');
    console.log("iframe height", childFrameObj.contentWindow.document.body.offsetHeight);
    childFrameObj.height = childFrameObj.contentWindow.document.body.offsetHeight + 60;  // there will be some margin and stylistic height
    console.log("iframe window", childFrameObj.contentWindow);
    console.log("iframde document", childFrameObj.contentWindow.document);
    console.log("iframe html", childFrameObj.contentWindow.document.documentElement);
  }

  render () {
    

    return (
      <div>
        <h1>Parent</h1>
        <p>Send Message <button id="message_button" onClick={this.handleParentClick.bind(this)}>Hi child</button></p>
        <span>Show Message </span>
        <div id='results'></div>
        <iframe
          id="myFrame"
          width="100%"
          height={0}
          frameBorder={1}
          scrolling="no"
          srcDoc="
          <h1>myFrame</h1>
          <p>Send Message: <button id='message_button'}>Hi parent</button></p>
          <span>Show Message</span>
          <div id='results'></div>
          <script>
              function receiveMessageFromParent ( event ) {
                let results = document.getElementById('results');
                results.innerHTML += event.data + '<br>';
              };
              window.addEventListener('message', receiveMessageFromParent, false);

              let messageButton = document.getElementById('message_button');
              /* console.log('msg button', messageButton); annoataion cannot use \/\/  */
              messageButton.addEventListener('click', function (e) {
                console.log('iframe send msg');
                console.log('parent', window.parent);
                console.log('top container', window.top);
                window.parent.postMessage('This is child', '*');
              }, false);
          </script>
          "
        />
      </div>
    );
  }
}

```  
* 初始效果，iframe 不展示：
![Parent](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190830134026.png)  
* 点击 `Hi child` 从 parent 传递信息给 iframe，iframe 自适应加载：
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190830142210.png)
* 多次点击 `Hi child`，iframe 自适应加载：
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190830142258.png)
* 点击 `Hi parent` 从 iframe 传递信息给 parent，展示信息：
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190830142350.png)