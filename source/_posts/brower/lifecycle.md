---
title: 浏览器周期
tags: 'lifecycle'
categories: 'brower'
top_img: '/img/brower.jpeg'
---

 ## 背景

应用程序生命周期是现代操作系统管理资源的关键方式。在Android、iOS和最新的Windows版本上，操作系统可以随时启动和停止应用程序。这使得这些平台能够将资源优化并重新分配到最有利于用户的地方。

在web端，历史上没有这样的生命周期，应用程序可以一直保持激活。随着大量网页的运行，内存、CPU、电池和网络等关键系统资源可能会被过度订阅，从而导致终端用户体验差。

虽然web平台早就有了与生命周期状态相关的事件，比如 load, unload, and visibilitychange，但这些事件只允许开发人员响应用户行为的生命周期状态更改。为了让web在低功耗设备上可靠地工作（并且在所有平台上都能展示更多资源），浏览器需要一种主动回收和重新分配系统资源的方法。

事实上，如今的浏览器已经采取了积极的措施来节省后台选项卡中页面的资源，许多浏览器（尤其是Chrome浏览器）都想做更多的工作，以减少它们的总体资源占用。

问题是，目前开发者还没法干预这些系统启动，即使知道它们正在启动，这就意味着，浏览器需要采用保守的策略或者冒着页面出错的风险。

页面生命周期API试图通过以下方式解决此问题：

* 在web上引入并标准化生命周期状态的概念。

* 定义新的系统启动状态，允许浏览器限制隐藏或非活动选项卡可以使用的资源。

* 创建新的api和事件，允许web开发人员响应这些新的系统启动状态的转换。

此解决方案给web开发人员构建可适应系统干预的应用程序提供了可能，并允许浏览器更积极地优化系统资源，最终使所有web用户受益。

本文的其余部分将介绍Chrome68中提供的新页面生命周期特性，并探讨它们与所有现有web平台状态和事件的关系。它还将为每个州的开发人员应该（不应该）做的工作类型提供建议和最佳实践。

## 页面生命周期状态和事件概述
所有页面生命周期状态都是离散的，并且相互排斥，这意味着一个页面一次只能处于一种状态。对页面生命周期状态的大多数更改通常可以通过DOM事件观察到（请参阅开发人员对每个状态的建议以了解异常）。

一图胜千言
 ![](/img/lifecycle.png)

### State

下表详细说明了每个状态。它还列出了可能出现在前后的状态，以及开发人员可以用来观察更改的事件。

*  Active

A page is in the active state if it is visible and has input focus.

Possible previous states:

passive (via the focus event)

Possible next states:

passive (via the blur event) |


*  Passive

A page is in the passive state if it is visible and does not have input focus.

Possible previous states:

active (via the blur event)

hidden (via the visibilitychange event)

Possible next states:

active (via the focus event)

hidden (via the visibilitychange event)

*  Hidden

A page is in the hidden state if it is not visible and has not been frozen.

Possible previous states:

passive (via the visibilitychange event)

Possible next states:

passive (via the visibilitychange event)

frozen (via the freeze event)

terminated (via the pagehide event)

*  Frozen

在冻结状态下，浏览器将暂停执行页面任务队列中的可冻结任务，直到页面解冻。这意味着JavaScript计时器和fetch回调之类的东西不会运行。已经在运行的任务可以完成（最重要的是冻结回调），但是它们可能会受限于它们可以做什么以及可以运行多长时间。

浏览器冻结页面作为一种保存CPU/电池/数据使用的方法；它们也这样做作为一种实现更快的向后/向前导航的方法—避免了重新加载整个页面。

Possible previous states:

hidden (via the freeze event)

Possible next states:

active (via the resume event, then the pageshow event)

passive (via the resume event, then the pageshow event)

hidden (via the resume event)

*  Terminated

一旦页面开始被浏览器从内存中卸载和清除，它就处于终止状态。在此状态下不能启动任何新任务，正在进行的任务如果运行太长可能会被终止。

Possible previous states:

hidden (via the pagehide event)

Possible next states:

NONE

*  Discarded

当浏览器为了节省资源而卸载页面时，页面处于丢弃状态。任何任务、事件回调或任何类型的JavaScript都不能在此状态下运行，因为丢弃通常发生在资源受限的情况下，在资源受限的情况下启动新进程是不可能的。

在丢弃状态下，标签本身（包括标签标题和favicon）通常对用户可见，即使页面已经消失。

Possible previous states:

frozen (no events fired)

Possible next states:

NONE

### Events
浏览器发送很多事件，但只有一小部分事件表示页面生命周期状态可能发生变化。下表概述了与生命周期相关的所有事件，并列出了它们可能转换到的状态。

*  focus

DOM元素获取焦点。

>  注意：焦点事件不一定表示状态更改。它只在页面以前没有输入焦点时发出状态更改的信号。

Possible previous states:

passive

Possible current states:

active

*  blur
DOM元素失去焦点。

> 注意：blur事件不一定表示状态更改。如果页面不再具有输入焦点（即页面不只是将焦点从一个元素切换到另一个元素），则只会发出状态更改的信号。

Possible previous states:

active

Possible current states:

passive

*  visibilitychange

文档的visibilityState值已更改。当用户导航到新页面、切换选项卡、关闭选项卡、最小化或关闭浏览器或切换移动操作系统上的应用程序时，就会发生这种情况。

Possible previous states:

passive

hidden

Possible current states:

passive

hidden

*  freeze *

页面刚刚被冻结。页面任务队列中的任何可释放任务都不会启动。

Possible previous states:

hidden

Possible current states:

frozen

*  resume *
浏览器已恢复冻结页。

Possible previous states:

frozen

Possible current states:

active (if followed by the pageshow event)

passive (if followed by the pageshow event)

hidden

*  pageshow
正在遍历会话历史记录条目。

这可以是全新的页面加载，也可以是从前向缓存中获取的页面。如果页是从前向缓存中获取的，则事件的持久化属性为true，否则为false。

Possible previous states:

frozen (a resume event would have also fired)

Possible current states:

active

passive

hidden

*  pagehide

正在从中遍历会话历史记录条目。

如果用户正在导航到另一个页面，并且浏览器能够将当前页面添加到后向缓存中以便稍后重用，则事件的persistend属性为true。如果为true，则页将进入冻结状态，否则将进入终止状态。

Possible previous states:

hidden

Possible current states:

frozen (event.persisted is true, freeze event follows)

terminated (event.persisted is false, unload event follows)


*  beforeunload

窗口、文档及其资源即将卸载。文档仍然可见，此时事件仍然可以取消。


> 警告：beforeunload事件只能用于警告用户未保存的更改。保存这些更改后，应删除该事件。决不能无条件地将其添加到页面中，因为这样做在某些情况下会损害性能。有关详细信息，请参阅下面legacy api部分。

*  unload

正在卸载该页。

> 警告：不建议使用unload事件，因为它不可靠，并且在某些情况下会影响性能。有关更多详细信息，请参阅legacy api部分。

Possible previous states:

hidden

Possible current states:

terminated


## New features added in Chrome 68

上面的图表显示了系统启动而不是用户启动的两种状态：冻结和丢弃。如上所述，现在的浏览器已经偶尔冻结和丢弃隐藏的标签（由他们自己决定），但是开发人员无法知道何时会发生这种情况。

在chrome68中，开发人员现在可以通过监听文档上的冻结和恢复事件来观察隐藏选项卡何时被冻结和解冻。

``` javascript

document.addEventListener('freeze', (event) => {
  // The page is now frozen.
});

document.addEventListener('resume', (event) => {
  // The page has been unfrozen.
});
 
```
在chrome68中，document对象现在还包括一个wasdoccarded属性。要确定页是否在隐藏选项卡中被丢弃，可以在页加载时检查此属性的值（注意：丢弃的页必须重新加载才能再次使用）。

``` javascript

if (document.wasDiscarded) {
  // Page was previously discarded by the browser while in a hidden tab.
}
 
```
有关冻结和恢复事件中的重要操作，以及如何处理和准备丢弃的页面的建议，请参阅下面每个状态建议。

接下来的几节将概述这些新特性如何适应现有的web平台状态和事件。

## 监测页面生命周期状态变化

在active, passive, and hidden 状态下，可以运行JavaScript代码，从现有web平台api确定当前页面生命周期状态。

``` javascript
const getState = () => {
  if (document.visibilityState === 'hidden') {
    return 'hidden';
  }
  if (document.hasFocus()) {
    return 'active';
  }
  return 'passive';
};
 
```
另一方面，冻结和终止状态只能在其各自的事件侦听器（冻结和页面隐藏）中检测到，因为状态正在更改。



### 观察状态变化

在上面定义的getState（）函数的基础上，您可以使用以下代码观察所有页生命周期状态的更改。


``` javascript
// Stores the initial state using the `getState()` function (defined above).
let state = getState();

// Accepts a next state and, if there's been a state change, logs the
// change to the console. It also updates the `state` value defined above.
const logStateChange = (nextState) => {
  const prevState = state;
  if (nextState !== prevState) {
    console.log(`State change: ${prevState} >>> ${nextState}`);
    state = nextState;
  }
};

// These lifecycle events can all use the same listener to observe state
// changes (they call the `getState()` function to determine the next state).
['pageshow', 'focus', 'blur', 'visibilitychange', 'resume'].forEach((type) => {
  window.addEventListener(type, () => logStateChange(getState()), {capture: true});
});

// The next two listeners, on the other hand, can determine the next
// state from the event itself.
window.addEventListener('freeze', () => {
  // In the freeze event, the next state is always frozen.
  logStateChange('frozen');
}, {capture: true});

window.addEventListener('pagehide', (event) => {
  if (event.persisted) {
    // If the event's persisted property is `true` the page is about
    // to enter the Back-Forward Cache, which is also in the frozen state.
    logStateChange('frozen');
  } else {
    // If the event's persisted property is not `true` the page is
    // about to be unloaded.
    logStateChange('terminated');
  }
}, {capture: true});

上面的代码做了三件事：

 
```
- 使用getState（）函数设置初始状态。

- 定义一个接受下一个状态的函数，如果有更改，则将状态更改记录到控制台。

- 为所有必要的生命周期事件添加捕获事件侦听器，这些事件反过来调用logStateChange（），传递下一个状态。

> 警告！这段代码在不同的浏览器中产生不同的结果，因为事件的顺序和可靠性没有得到一致的实现。要了解如何最好地处理这些不一致，请参阅管理跨浏览器差异。

以上代码需要注意的一点是，所有事件侦听器都被添加到window中，并且它们都传递{capture:true}。有几个原因：


- 并非所有页面生命周期事件都具有相同的目标。pagehide和pageshow在window上启动；visibilitychange、freeze和resume在document上启动，focus和blur在各自的DOM元素上启动。

- 这些事件中的大多数都没有冒泡，这意味着不可能将非捕获的事件侦听器添加到一个公共祖先元素并观察所有这些事件。

- 捕获阶段在目标或冒泡阶段之前执行，因此在那里添加侦听器有助于确保它们在其他代码取消它们之前运行。

## 管理跨浏览器差异

本文开头的图表根据页面生命周期API概述了状态和事件流。但是由于这个API刚刚被引入，新的事件和 DOM API 并没有在所有浏览器中实现。


此外，目前在所有浏览器中实现的事件并不是一致地实现的。例如：


- 某些浏览器在切换选项卡时不会触发模糊事件。这意味着（与上面的图表和表格相反）页面可以从活动状态转到隐藏状态，而无需先经历被动状态。

- 一些浏览器实现了一个前向缓存，页面生命周期API将缓存的页面分类为处于冻结状态。由于这个API是全新的，这些浏览器还没有实现冻结和恢复事件，尽管这个状态仍然可以通过pagehide和pageshow事件观察到。

- 旧版本的Internet Explorer（10及以下）不实现visibilitychange事件。

- pagehide和visibilitychange事件的调度顺序已更改。以前的浏览器会在pagehide之后调度visibilitychange，如果页面的可见性状态在卸载页面时是可见的。新的Chrome版本将在pagehide之前发送visibilitychange，而不管文档在卸载时的可见性状态如何。

- Safari在关闭选项卡时不会可靠地触发pagehide或visibilitychange事件（webkit Bug:151610和151234），因此在Safari中，您可能还需要侦听beforeunload事件以检测隐藏状态的更改。但由于beforeunload事件可以取消，因此需要等到事件传播完成后，才能知道状态是否已更改为hidden。重要提示：只有在Safari中才能使用beforeunload事件，因为在其他浏览器中使用此事件可能会影响性能。有关详细信息，请参阅legacy api部分。


为了让开发人员更容易处理这些跨浏览器的不一致性，并且只关注于遵循生命周期状态的建议和最佳实践 , 有PageLifecycle.js，一个用于观察页面生命周期API状态更改的JavaScript库。



PageLifecycle.js规范化事件触发顺序中的跨浏览器差异，以便状态更改始终完全按照本文中图表和表格中的概述进行（并且在所有浏览器中都是一致的）。

[PageLifecycle.js](https://github.com/GoogleChromeLabs/page-lifecycle)



## 开发建议


作为开发人员，了解页面生命周期状态并知道如何在代码中观察它们是很重要的，因为应该（和不应该）执行的工作类型在很大程度上取决于页面所处的状态。

例如，如果页面处于隐藏状态，则向用户显示临时通知显然没有意义。虽然这个例子很明显，但还有其他一些不太明显的建议值得列举。


- Active
激活状态是用户最关键的时间，因此也是页面响应用户输入的最重要时间。

任何可能阻塞主线程的非UI任务都应该置为闲置，或交给web worker

- Passive
页面失焦，但是能看到

用户不与页面交互，但仍然可以看到页面。这意味着UI更新和动画应该仍然是平滑的，但是这些更新发生的时间并不那么关键。

当页面从Active更改为Passive时，正是保存未保存的应用程序状态的好时机。

- Hidden
当页面从Passive变为Hidden时，在重新加载之前，用户可能不会再次与之交互。

向hidden的转换通常也是开发人员可以可靠观察到的最后一个状态更改（在移动设备上尤其如此，因为用户可以关闭选项卡或浏览器应用程序本身，在这些情况下不会触发beforeunload、pagehide和unload事件）。

这意味着您应该将隐藏状态视为用户会话的可能结束。换句话说，保存任何未保存的应用程序状态并发送任何未发送的分析数据。

您还应该停止进行UI更新（因为用户看不到它们），并且应该停止用户不希望在后台运行的任何任务。

- Frozen
在冻结状态下，任务队列中可冻结的任务将被挂起，直到页面解除冻结-这可能永远不会发生（例如，如果页面被丢弃）。

这意味着当页面从隐藏变为冻结时，必须停止任何计时器或断开任何连接，如果这些连接冻结，可能会影响同一来源中其他打开的选项卡，或影响浏览器将页面放入前向缓存的能力。

尤其重要的是：

关闭所有打开的IndexedDB连接。

关闭开放频道连接。

关闭活动的WebRTC连接。

停止任何网络轮询或关闭任何打开的Web套接字连接。

释放所有持有的Web锁。

您还应该将任何动态视图状态（例如，无限列表视图中的滚动位置）持久化到sessionStorage（或IndexedDB via commit（）），以便在以后丢弃并重新加载页面时恢复这些状态。

如果页面从冻结变回隐藏，则可以重新打开所有关闭的连接，或重新启动页面最初冻结时停止的任何轮询。

- Terminated

当页转换到终止状态时，通常不需要执行任何操作。

由用户操作而卸载的页面在进入终止状态之前总是经过隐藏状态，隐藏状态是应该执行会话结束逻辑（例如，持久化应用程序状态和向分析报告）的位置。

另外（正如在关于隐藏状态的建议中提到的），开发人员必须认识到，在许多情况下（尤其是在移动设备上）无法可靠地检测到向终止状态的转换，因此依赖终止事件（例如beforeunload、pagehide和unload）的开发人员可能会丢失数据。
- Discarded

在丢弃页面时，开发人员无法观察到丢弃的状态。这是因为页面通常在资源限制下被丢弃，在大多数情况下，仅仅为了允许脚本响应丢弃事件而解冻页面是不可能的。

因此，您应该在从hidden更改为freezed时准备放弃的可能性，然后可以通过检查文档已丢弃.


## 要避免的遗留生命周期API

- The unload event

许多开发人员将unload事件视为有保证的回调，并将其用作会话结束信号来保存状态和发送分析数据，但这样做极不可靠，尤其是在移动设备上！卸载事件在许多典型的卸载情况下都不会触发，包括从移动设备上的选项卡切换器关闭选项卡或从应用切换器关闭浏览器应用程序。

因此，最好依靠visibilitychange事件来确定会话何时结束，并将隐藏状态视为保存应用程序和用户数据的最后可靠时间。

此外，仅仅存在一个注册的卸载事件处理程序（通过onunload或addEventListener（））就可以阻止浏览器将页面放在前向缓存中以实现更快的前向和后向加载。

在所有现代浏览器（包括IE11）中，建议始终使用pagehide事件来检测可能的页面卸载（即终止状态），而不是unload事件。如果您需要支持InternetExplorer版本10及更低版本，您应该检测pagehide事件，并且只有在浏览器不支持pagehide的情况下才使用unload

``` javascript
const terminationEvent = 'onpagehide' in self ? 'pagehide' : 'unload';

addEventListener(terminationEvent, (event) => {
  // Note: if the browser is able to cache the page, `event.persisted`
  // is `true`, and the state is frozen rather than terminated.
}, {capture: true});
 
```


- The beforeunload event

beforeunload事件与unload事件有一个类似的问题，即当存在该事件时，它会阻止浏览器在其前向缓存中缓存页面。

beforeunload和unload的区别在于beforeunload有合法的用法。例如，当您想警告用户他们有未保存的更改时，如果他们继续卸载页面，这些更改将丢失。

由于使用beforeunload有正当理由，但使用它会阻止将页添加到前向缓存，因此建议您仅在用户有未保存的更改时添加beforeunload侦听器，然后在保存未保存的更改后立即将其删除。

换句话说，不要这样做（因为它会无条件地添加beforeunload侦听器）：

参考文档：
<a href="https://developers.google.com/web/updates/2018/07/page-lifecycle-api"> Overview of page lifecycle states</a> 
<a href="https://wicg.github.io/page-lifecycle/"> W3c  Page Lifecycle</a> 
