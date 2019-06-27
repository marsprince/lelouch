# vue中进行动态渲染的另一种思路

在实际开发中我们经常遇到这样一种情况，就是根据后端返回接口状态的不同，渲染不同的组件。为了性能，一般获取状态和数据都是放在同一个接口里的。这就出现了问题，怎么把数据传给组件。

很容易想到的解决方案是通过不同的路由，把数据作为传参或者放在其他地方传过去。但是对于单一页面的模块就没法这样做了。于是又想到利用动态组件解决这个问题，但是动态组件还是不能够很优雅的解决不同状态下组件的传参和事件问题，往往需要传一个json过去。于是想到利用异步组件动态渲染。



## 异步组件是什么？

所谓的异步组件，就是传一个函数给vue作为h中的tag，大家都知道tag是可以传组件对象，传字符串的，但是它也可以传一个函数进去，参考如下源码

```javascript
 // async component
  var asyncFactory;
  if (isUndef(Ctor.cid)) {
    asyncFactory = Ctor;
    Ctor = resolveAsyncComponent(asyncFactory, baseCtor);
    if (Ctor === undefined) {
      // return a placeholder node for async component, which is rendered
      // as a comment node but preserves all the raw information for the node.
      // the information will be used for async server-rendering and hydration.
      return createAsyncPlaceholder(
        asyncFactory,
        data,
        context,
        children,
        tag
      )
    }
  }
```

如果ctor.cid不存在，代表这个函数不是vue的构造函数，而是我们传进去的函数，就要进入resolveAsyncComponent流程。如果结果不存在，vue就会建一个comment node并保留原始信息。下图为这个vnode：

![5d1481bc70b3177079](https://i.loli.net/2019/06/27/5d1481bc70b3177079.png)

## 如何让vue来接管异步流程？

上面截图我们看到了，如果通过resolveAsyncComponent返回undef，就会成为一个空评论节点。那么如何在异步请求回来让vue重新渲染呢，就得进入resolveAsyncComponent看一下了。关键代码如下：

```javascript
    var res = factory(resolve, reject);
    if (isObject(res)) {
      if (isPromise(res)) {
        // () => Promise
        if (isUndef(factory.resolved)) {
          res.then(resolve, reject);
        }
      } else if (isPromise(res.component)) {
        res.component.then(resolve, reject);

        if (isDef(res.error)) {
          factory.errorComp = ensureCtor(res.error, baseCtor);
        }
        ...
      }
    }
```

首先，会执行传入的函数，返回如果是promise或者返回一个对象并且component是promise，就会在这个promise上追加then方法。这里推荐按官方文档，返回一个对象，可以更好的帮我们处理error、loading等其他情况。

我们看一下resolve里面vue做了什么

```javascript
var resolve = once(function (res) {
      // cache resolved
      factory.resolved = ensureCtor(res, baseCtor);
      // invoke callbacks only if this is not a synchronous resolve
      // (async resolves are shimmed as synchronous during SSR)
      if (!sync) {
        forceRender(true);
      } else {
        owners.length = 0;
      }
    });
```

可以看到，只是单纯的把构造函数绑在resolved上，然后强制触发render流程（分同步和异步）。这样我们就明白了，只要返回一个promise，然后resolve掉就可以触发reRender，既然走到了render流程我们就可以为所欲为了。

## 渲染

这里只简单的写一下render

```javascript
let data;
render(h) {
      const factory = () => {
        return {
          component: new promise((resolve) => {
            setTimeout(()=>{
              resolve({})
              data = {
                status: '2000',
                data: {}
              }
            }, 1000)
          })
        }
      };
      const renderContent = () => {
        if(!data) return [factory]
        if(data.status!=='2000') return [error]
        let _data = data.data;
        if(_data.status === 2) {
          return [组件, {
            props: ...
          }]
        }
      };
      return h(...renderContent());
    }
```

明白了之前的流程，后面就是业务逻辑了，我们终于可以摆脱丑陋的json props了！
