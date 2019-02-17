## vue2.6更新研究之nextTick总是使用microTask

vue2.6的更新日志里，有这么一条，叫做 [Internal Change: Reverting`nextTick`to Always Use Microtask](https://gist.github.com/yyx990803/d1a0eaac052654f93a1ccaab072076dd)。

当时看到这一条，顿觉

![5c6926d21f145](https://i.loli.net/2019/02/17/5c6926d21f145.jpg)

难道nextTick之前使用的不是microTask吗，老子面试的时候可是这么答的，怪不得阿里把我pass了。然后仔细进去看了一下，事情其实是这样的：

之前的版本里，yyx是这么说的

```javascript
// In < 2.4 we used micro tasks everywhere, but there are some scenarios where
// micro tasks have too high a priority and fires in between supposedly
// sequential events (e.g. #4521, #6690) or even between bubbling of the same
// event (#6566). However, using macro tasks everywhere also has subtle problems
// when state is changed right before repaint (e.g. #6813, out-in transitions).
// Here we use micro task by default, but expose a way to force macro task when
// needed (e.g. in event handlers attached by v-on).
let microTimerFunc
let macroTimerFunc
let useMacroTask = false
```

因为某些情况，我们不得不暴露一个使用macro task的方法，比如：v-on，然后写了一个函数，这样想用啥就可以用啥啦：

```javascript
/**
 * Wrap a function so that if any code inside triggers state change,
 * the changes are queued using a Task instead of a MicroTask.
 */
export function withMacroTask (fn: Function): Function {
  return fn._withTask || (fn._withTask = function () {
    useMacroTask = true
    const res = fn.apply(null, arguments)
    useMacroTask = false
    return res
  })
}
```

![5c692a6ca6d25](https://i.loli.net/2019/02/17/5c692a6ca6d25.jpg)

然鹅

However, the change has actually led to[more problems of its own](https://github.com/vuejs/vue/issues?q=label%3A%22nextTick+related%22+is%3Aclosed), outweighing the benefit of the fix.

![5c692b38ac438](https://i.loli.net/2019/02/17/5c692b38ac438.jpg)

那我们来先看看是什么情况下必须用macrotask代替microtask呢，我简化了vue源码的逻辑，写了一个简单的[demo](https://codepen.io/mars0prince/pen/XOEoXY)：

```javascript
let useMicroTask = false;

function add(target, name, handler) {
  if (useMicroTask) {
    let attachedTimestamp = Date.now();
    if (attachedTimestamp > document.createEvent("Event").timeStamp) {
      attachedTimestamp = performance.now();
    }

    const original = handler;
    handler = function(e) {
      if (e.timeStamp >= attachedTimestamp) {
        console.log("fire");
        return original.apply(this, arguments);
      } else {
        console.log("can not fire");
      }
    };
  }
  return target.addEventListener(name, handler);
}

let added = false;
add(document.getElementById("inner"), "click", function() {
  console.log("inner");
  if (!added) {
    Promise.resolve().then(function() {
      useMicroTask = true;
      add(document.getElementById("outer"), "click", function() {
        console.log("outer");
      });
    });
    added = true;
  }
});
```

```
<div id="outer">
  outer
  <div id="inner">
    inner
  </div>
</div>
```

核心就是当我们在子click的回调里给父div添加了一个click事件，按vue的nextTick的本意，应该是先完成事件冒泡，然后更新dom，但是呢micro task在冒泡的间隙就执行完了，因此第一次点击子div的时候，父div的回调也直接执行了，用yyx的话讲，就是

microtasks have too high a priority and fire in between supposedly  
sequential events (e.g. #4521, #6690, which have workarounds)  
or even between bubbling of the same event (#6566).

大家可以把我的代码修改一下试试。



新解决方案也很简单，就是为这种情况添加一个逻辑：时间判断。劫持原来的handler，进行时间判断，只有在事件触发时间晚于绑定时间时，才会执行回调，这样就避免了上面的情况。

不过这样还有一个问题，参考https://github.com/vuejs/vue/blob/0bad7e2a3508b55abaa8aec2a1bd9c1127305cb4/src/core/observer/scheduler.js

```javascript
// Determine what event timestamp the browser is using. Annoyingly, the
// timestamp can either be hi-res (relative to page load) or low-res
// (relative to UNIX epoch), so in order to compare time we have to use the
// same timestamp type when saving the flush timestamp.
```

e.timeStamp和date.now有的时候并不是同一个相对起始时间（relative to page load or relative to UNIX epoch，因此需要比较一下。

![5c6930cf52ea6](https://i.loli.net/2019/02/17/5c6930cf52ea6.jpg)

然鹅，并不是每个浏览器都这么听话的，在我写这篇文章的时候，我又看了一下vue的代码，发现yyx又添加了若干fix：

![5c6931b4df26f](https://i.loli.net/2019/02/17/5c6931b4df26f.png)



然后现在的代码变成了这个[样子](https://github.com/vuejs/vue/blob/0bad7e2a3508b55abaa8aec2a1bd9c1127305cb4/src/platforms/web/runtime/modules/events.js#L45)，我也没心情研究具体逻辑了，总之就是：

![5c69322bc9a7b](https://i.loli.net/2019/02/17/5c69322bc9a7b.jpg)

大家有时间可以看看逻辑，反正我是看着就头疼，辛苦尤大大年初三还在改bug了。








