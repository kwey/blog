---
title: addEventListener 详解以及应用
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

> 将指定的监听器注册到 EventTarget 上，当该对象触发指定的事件时，指定的回调函数就会被执行。 事件目标可以是一个文档上的元素 Element,Document和Window或者任何其他支持事件的对象 (比如 XMLHttpRequest)


## <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#The_event_listener_callback" target="_blank">MDN</a>

``` javascript
target.addEventListener(type, listener[, options]);
target.addEventListener(type, listener[, useCapture]);
```

``` javascript
//Gecko/Mozilla only
target.addEventListener(type, listener[, useCapture, wantsUntrusted]);
```


<dt><h3>type</h3></dt><dd>表示监听<a href="https://developer.mozilla.org/zh-CN/docs/Web/Events">事件类型</a>的字符串。</dd><dt>listener</dt><dd>当所监听的事件类型触发时，会接收到一个事件通知（实现了 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Event" title="此页面仍未被本地化, 期待您的翻译!">Event</a> 接口的对象）对象。listener 必须是一个实现了 <a href="https://developer.mozilla.org/zh-CN/docs/Web/API/EventListener" title="当EventListener 所注册的事件发生的时候，该方法会被调用。">EventListener</a> 接口的对象，（该对象中有handleEvent方法），或者是一个<a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions">函数</a></dd>

options 可选<dd>一个指定有关 listener 属性的可选参数对象。可用的选项如下：

<ul><li>

capture:  <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean" title="此页面仍未被本地化, 期待您的翻译!">Boolean</a>，表示 listener 会在该类型的事件捕获阶段传播到该 EventTarget 时触发。</li><li>once:  Boolean，表示 listener 在添加之后最多只调用一次。如果是 true， listener 会在其被调用之后自动移除。</li><li>passive: <a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean" title="此页面仍未被本地化, 期待您的翻译!">Boolean</a>，表示 listener 永远不会调用 preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。</li><li>> mozSystemGroup: 只能在 XBL 或者是 Firefox' chrome 使用，这是个Boolean，表示 listener 被添加到 system group。</li></ul></dd>

## useCapture  可选

<dd><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean" title="此页面仍未被本地化, 期待您的翻译!">Boolean</a>，在DOM树中，注册了listener的元素， 是否要先于它下面的EventTarget，调用该listener。 当userCapture(设为true) 时，沿着DOM树向上冒泡的事件，不会触发listener。当一个元素嵌套了另一个元素，并且两个元素都对同一事件注册了一个处理函数时，所发生的事件冒泡和事件捕获是两种不同的事件传播方式。事件传播模式决定了元素以哪个顺序接收事件。进一步的解释可以查看 <a rel="noopener" href="http://www.w3.org/TR/DOM-Level-3-Events/#event-flow" title="http://www.w3.org/TR/DOM-Level-3-Events/#event-flow">事件流</a> 及 <a rel="noopener" href="http://www.quirksmode.org/js/events_order.html#link4">JavaScript Event order</a> 文档。 如果没有指定， useCapture 默认为 false 。</dd>

关于passive：某乎上这样说：

当你触摸滑动页面时，页面应该跟随手指一起滚动。而此时你绑定了一个 touchstart 事件，你的事件大概执行 200 毫秒。这时浏览器就犯迷糊了：如果你在事件绑定函数中调用了 preventDefault，那么页面就不应该滚动，如果你没有调用 preventDefault，页面就需要滚动。但是你到底调用了还是没有调用，浏览器不知道。只能先执行你的函数，等 200 毫秒后，绑定事件执行完了，浏览器才知道，“哦，原来你没有阻止默认行为，好的，我马上滚”。此时，页面开始滚。


## 检测 passive 值
``` javascript
var passiveSupported = false;

try {
    var options = Object.defineProperty({}, "passive", {
        get: function() {
          passiveSupported = true;
    }
  });

  window.addEventListener("test", null, options);
} catch(err) {}

someElement.addEventListener("mouseup", handleMouseUp, passiveSupported
                               ? { passive: true } : false);

```



``` javascript

class name {
    constructor() {
        this.container = document.createElement('div')
    }
    handleEvent(e) {
        switch (e.type) {
          case 'touchstart':
        case 'mousedown':
            // dosomething
            break
        case 'touchmove':
        case 'mousemove':
            // dosomething
            break
        case 'touchend':
        case 'mouseup':
        case 'touchcancel':
        case 'mousecancel':
            // dosomething
        break
    }
  }
  _addDOMEvents() {
        this._handleDOMEvents(addEvent)
  }
  _removeDOMEvents() {
        this._handleDOMEvents(removeEvent)
  }
  _handleDOMEvents(eventOperation) {
        // 调用name上的handleEvent方法
    eventOperation(this.container, 'touchstart', this)
    eventOperation(window, 'touchend', this)
    eventOperation(this.container, 'mousedown', this)
    eventOperation(window, 'mouseup', this)
  }

  addEvent(el, type, fn, capture, passive = false) {
        el.addEventListener(type, fn, {passive, capture: !!capture})
  }
  
  removeEvent(el, type, fn, capture, passive = false) {
        el.removeEventListener(type, fn, {passive, capture: !!capture})
  }
}
```



``` javascript

let vendor = (() =&gt; {
      if (!inBrowser) {
        return false
  }
  // first pick up standard to fix #743
  let transformNames = {
        standard: 'transform',
    webkit: 'webkitTransform',
    Moz: 'MozTransform',
    O: 'OTransform',
    ms: 'msTransform'
  }

  for (let key in transformNames) {
        if (elementStyle[transformNames[key]] !== undefined) {
          return key
    }
  }

  return false
})()
function prefixStyle(style) {
    if (vendor === false) {
        return false
    }

    if (vendor === 'standard') {
        if (style === 'transitionEnd') {
          return 'transitionend'
        }
        return style
    }

  return vendor + style.charAt(0).toUpperCase() + style.substr(1)
}
let transform = prefixStyle('transform')
let transition = prefixStyle('transition')

export function tap(e, eventName) {
    let ev = document.createEvent('Event')
    ev.initEvent(eventName, true, true)
    ev.pageX = e.pageX
    ev.pageY = e.pageY
    e.target.dispatchEvent(ev)
}

function click(e, event = 'click') {
      let eventSource
  if (e.type === 'mouseup' || e.type === 'mousecancel') {
        eventSource = e
  } else if (e.type === 'touchend' || e.type === 'touchcancel') {
        eventSource = e.changedTouches[0]
  }
  let posSrc = {}
  if (eventSource) {
        posSrc.screenX = eventSource.screenX || 0
    posSrc.screenY = eventSource.screenY || 0
    posSrc.clientX = eventSource.clientX || 0
    posSrc.clientY = eventSource.clientY || 0
  }
  let ev
  const bubbles = true
  const cancelable = true
  if (typeof MouseEvent !== 'undefined') {
        try {
        // [在此查看几个参数是意义](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/initMouseEvent)
            ev = new MouseEvent(event, extend({
                bubbles,
                cancelable
            }, posSrc))
        } catch (e) {
            createEvent()
        }
    } else {
        createEvent()
    }
        
    function createEvent() {
    // 过时的方式
    ev = document.createEvent('Event')
        ev.initEvent(event, bubbles, cancelable)
        extend(ev, posSrc)
      }
    
      // forwardedTouchEvent set to true in case of the conflict with fastclick
      ev.forwardedTouchEvent = true
      ev._constructed = true
      e.target.dispatchEvent(ev)
    }
    
    function dblclick(e) {
          click(e, 'dblclick')
    }
```

