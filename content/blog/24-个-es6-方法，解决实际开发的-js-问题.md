---
title: 24 个 ES6 方法，解决实际开发的 JS 问题
date: 2024-09-26T03:37:45.927Z
---

# [24 个 ES6 方法，解决实际开发的 JS 问题](http://www.yyyweb.com/5354.html)

本文主要介绍 24 中 es6 方法，这些方法都挺实用的，本本请记好，时不时翻出来看看。

#### 1.如何隐藏所有指定的元素

```js
const hide = (el) => Array.from(el).forEach(e => (e.style.display = 'none'));

// 事例:隐藏页面上所有`<img>`元素?
hide(document.querySelectorAll('img'))
复制代码
```

#### 2.如何检查元素是否具有指定的类？

页面DOM里的每个节点上都有一个`classList`对象，程序员可以使用里面的方法新增、删除、修改节点上的CSS类。使用`classList`，程序员还可以用它来判断某个节点是否被赋予了某个CSS类。

```js
const hasClass = (el, className) => el.classList.contains(className)

// 事例
hasClass(document.querySelector('p.special'), 'special') // true

复制代码
```

#### 3.如何切换一个元素的类?

```js
const toggleClass = (el, className) => el.classList.toggle(className)

// 事例 移除 p 具有类`special`的 special 类
toggleClass(document.querySelector('p.special'), 'special')
复制代码
```

#### 4.如何获取当前页面的滚动位置？

```js
const getScrollPosition = (el = window) => ({
  x: el.pageXOffset !== undefined ? el.pageXOffset : el.scrollLeft,
  y: el.pageYOffset !== undefined ? el.pageYOffset : el.scrollTop
});

// 事例
getScrollPosition(); // {x: 0, y: 200}
复制代码
```

#### 5.如何平滑滚动到页面顶部

```js
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
}

// 事例
scrollToTop()
复制代码
```

`window.requestAnimationFrame()` 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

`requestAnimationFrame`：优势：由系统决定回调函数的执行时机。60Hz的刷新频率，那么每次刷新的间隔中会执行一次回调函数，不会引起丢帧，不会卡顿。

#### 6.如何检查父元素是否包含子元素？

```js
const elementContains = (parent, child) => parent !== child && parent.contains(child);

// 事例
elementContains(document.querySelector('head'), document.querySelector('title')); 
// true
elementContains(document.querySelector('body'), document.querySelector('body')); 
// false
复制代码
```

#### 7.如何检查指定的元素在视口中是否可见？

```js
const elementIsVisibleInViewport = (el, partiallyVisible = false) => {
  const { top, left, bottom, right } = el.getBoundingClientRect();
  const { innerHeight, innerWidth } = window;
  return partiallyVisible
    ? ((top > 0 && top < innerHeight) || (bottom > 0 && bottom < innerHeight)) &&
        ((left > 0 && left < innerWidth) || (right > 0 && right < innerWidth))
    : top >= 0 && left >= 0 && bottom <= innerHeight && right <= innerWidth;
};

// 事例
elementIsVisibleInViewport(el); // 需要左右可见
elementIsVisibleInViewport(el, true); // 需要全屏(上下左右)可以见
复制代码
```

#### 8.如何获取元素中的所有图像？

```js
const getImages = (el, includeDuplicates = false) => {
  const images = [...el.getElementsByTagName('img')].map(img => img.getAttribute('src'));
  return includeDuplicates ? images : [...new Set(images)];
};

// 事例：includeDuplicates 为 true 表示需要排除重复元素
getImages(document, true); // ['image1.jpg', 'image2.png', 'image1.png', '...']
getImages(document, false); // ['image1.jpg', 'image2.png', '...']

复制代码
```

#### 9.如何确定设备是移动设备还是台式机/笔记本电脑？

```js
const detectDeviceType = () =>
  /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)
    ? 'Mobile'
    : 'Desktop';

// 事例
detectDeviceType(); // "Mobile" or "Desktop"

复制代码
```

#### 10.How to get the current URL?

```js
const currentURL = () => window.location.href

// 事例
currentURL() // 'https://google.com'
复制代码
```

#### 11.如何创建一个包含当前URL参数的对象？

```js
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );

// 事例
getURLParameters('http://url.com/page?n=Adam&s=Smith'); // {n: 'Adam', s: 'Smith'}
getURLParameters('google.com'); // {}
复制代码
```

#### 12.如何将一组表单元素转化为对象？

```js
const formToObject = form =>
  Array.from(new FormData(form)).reduce(
    (acc, [key, value]) => ({
      ...acc,
      [key]: value
    }),
    {}
  );

// 事例
formToObject(document.querySelector('#form')); 
// { email: 'test@email.com', name: 'Test Name' }
复制代码
```

#### 13.如何从对象检索给定选择器指示的一组属性？

```js
const get = (from, ...selectors) =>
  [...selectors].map(s =>
    s
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter(t => t !== '')
      .reduce((prev, cur) => prev && prev[cur], from)
  );
const obj = { selector: { to: { val: 'val to select' } }, target: [1, 2, { a: 'test' }] };

// Example
get(obj, 'selector.to.val', 'target[0]', 'target[2].a'); 
// ['val to select', 1, 'test']
复制代码
```

#### 14.如何在等待指定时间后调用提供的函数？

```js
const delay = (fn, wait, ...args) => setTimeout(fn, wait, ...args);
delay(
  function(text) {
    console.log(text);
  },
  1000,
  'later'
); 

// 1秒后打印 'later'
复制代码
```

#### 15.如何在给定元素上触发特定事件且能选择地传递自定义数据？

```js
const triggerEvent = (el, eventType, detail) =>
  el.dispatchEvent(new CustomEvent(eventType, { detail }));

// 事例
triggerEvent(document.getElementById('myId'), 'click');
triggerEvent(document.getElementById('myId'), 'click', { username: 'bob' });
复制代码
```

自定义事件的函数有 `Event`、`CustomEvent` 和 `dispatchEvent`

```js
// 向 window派发一个resize内置事件
window.dispatchEvent(new Event('resize'))
 

// 直接自定义事件，使用 Event 构造函数：
var event = new Event('build');
var elem = document.querySelector('#id')
// 监听事件
elem.addEventListener('build', function (e) { ... }, false);
// 触发事件.
elem.dispatchEvent(event);
复制代码
```

`CustomEvent` 可以创建一个更高度自定义事件，还可以附带一些数据，具体用法如下：

```js
var myEvent = new CustomEvent(eventname, options);
其中 options 可以是：
{
  detail: {
    ...
  },
  bubbles: true,    //是否冒泡
  cancelable: false //是否取消默认事件
}
复制代码
```

其中 `detail` 可以存放一些初始化的信息，可以在触发的时候调用。其他属性就是定义该事件是否具有冒泡等等功能。

内置的事件会由浏览器根据某些操作进行触发，自定义的事件就需要人工触发。 `dispatchEvent` 函数就是用来触发某个事件：

```js
element.dispatchEvent(customEvent);
复制代码
```

上面代码表示，在 `element` 上面触发 `customEvent` 这个事件。

```js
// add an appropriate event listener
obj.addEventListener("cat", function(e) { process(e.detail) });
 
// create and dispatch the event
var event = new CustomEvent("cat", {"detail":{"hazcheeseburger":true}});
obj.dispatchEvent(event);
使用自定义事件需要注意兼容性问题，而使用 jQuery 就简单多了：

// 绑定自定义事件
$(element).on('myCustomEvent', function(){});
 
// 触发事件
$(element).trigger('myCustomEvent');
// 此外，你还可以在触发自定义事件时传递更多参数信息：
 
$( "p" ).on( "myCustomEvent", function( event, myName ) {
  $( this ).text( myName + ", hi there!" );
});
$( "button" ).click(function () {
  $( "p" ).trigger( "myCustomEvent", [ "John" ] );
});
复制代码
```

#### 16.如何从元素中移除事件监听器?

```js
const off = (el, evt, fn, opts = false) => el.removeEventListener(evt, fn, opts);

const fn = () => console.log('!');
document.body.addEventListener('click', fn);
off(document.body, 'click', fn); 
复制代码
```

#### 17.如何获得给定毫秒数的可读格式？

```js
const formatDuration = ms => {
  if (ms < 0) ms = -ms;
  const time = {
    day: Math.floor(ms / 86400000),
    hour: Math.floor(ms / 3600000) % 24,
    minute: Math.floor(ms / 60000) % 60,
    second: Math.floor(ms / 1000) % 60,
    millisecond: Math.floor(ms) % 1000
  };
  return Object.entries(time)
    .filter(val => val[1] !== 0)
    .map(([key, val]) => `${val} ${key}${val !== 1 ? 's' : ''}`)
    .join(', ');
};

// 事例
formatDuration(1001); // '1 second, 1 millisecond'
formatDuration(34325055574); 
// '397 days, 6 hours, 44 minutes, 15 seconds, 574 milliseconds'
复制代码
```

#### 18.如何获得两个日期之间的差异（以天为单位）？

```js
const getDaysDiffBetweenDates = (dateInitial, dateFinal) =>
  (dateFinal - dateInitial) / (1000 * 3600 * 24);

// 事例
getDaysDiffBetweenDates(new Date('2017-12-13'), new Date('2017-12-22')); // 9
复制代码
```

#### 19.如何向传递的URL发出GET请求？

```js
const httpGet = (url, callback, err = console.error) => {
  const request = new XMLHttpRequest();
  request.open('GET', url, true);
  request.onload = () => callback(request.responseText);
  request.onerror = () => err(request);
  request.send();
};

httpGet(
  'https://jsonplaceholder.typicode.com/posts/1',
  console.log
); 

// {"userId": 1, "id": 1, "title": "sample title", "body": "my text"}
复制代码
```

#### 20.如何对传递的URL发出POST请求？

```js
const httpPost = (url, data, callback, err = console.error) => {
  const request = new XMLHttpRequest();
  request.open('POST', url, true);
  request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
  request.onload = () => callback(request.responseText);
  request.onerror = () => err(request);
  request.send(data);
};

const newPost = {
  userId: 1,
  id: 1337,
  title: 'Foo',
  body: 'bar bar bar'
};
const data = JSON.stringify(newPost);
httpPost(
  'https://jsonplaceholder.typicode.com/posts',
  data,
  console.log
); 

// {"userId": 1, "id": 1337, "title": "Foo", "body": "bar bar bar"}
复制代码
```

#### 21.如何为指定选择器创建具有指定范围，步长和持续时间的计数器？

```js
const counter = (selector, start, end, step = 1, duration = 2000) => {
  let current = start,
    _step = (end - start) * step < 0 ? -step : step,
    timer = setInterval(() => {
      current += _step;
      document.querySelector(selector).innerHTML = current;
      if (current >= end) document.querySelector(selector).innerHTML = end;
      if (current >= end) clearInterval(timer);
    }, Math.abs(Math.floor(duration / (end - start))));
  return timer;
};

// 事例
counter('#my-id', 1, 1000, 5, 2000); 
// 让 `id=“my-id”`的元素创建一个2秒计时器

复制代码
```

#### 22.如何将字符串复制到剪贴板？

```js
  const el = document.createElement('textarea');
  el.value = str;
  el.setAttribute('readonly', '');
  el.style.position = 'absolute';
  el.style.left = '-9999px';
  document.body.appendChild(el);
  const selected =
    document.getSelection().rangeCount > 0 ? document.getSelection().getRangeAt(0) : false;
  el.select();
  document.execCommand('copy');
  document.body.removeChild(el);
  if (selected) {
    document.getSelection().removeAllRanges();
    document.getSelection().addRange(selected);
  }
};

// 事例
copyToClipboard('Lorem ipsum'); 
// 'Lorem ipsum' copied to clipboard

复制代码
```

#### 23.如何确定页面的浏览器选项卡是否聚焦？

```js
const isBrowserTabFocused = () => !document.hidden;

// 事例
isBrowserTabFocused(); // true
复制代码
```

#### 24.如何创建目录（如果不存在）？

```js
const fs = require('fs');
const createDirIfNotExists = dir => (!fs.existsSync(dir) ? fs.mkdirSync(dir) : undefined);

// 事例
createDirIfNotExists('test'); 
复制代码
```

这里面的方法大都挺实用，可以解决很多开发过程问题，大家就好好利用起来吧。

作者：前端小智
链接：https://juejin.im/post/5e5ef2f9f265da57685dc9c1
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。