
## 指令

### v-show 与 v-if 区别


### v-for 绑定的 key 的作用

> key 是为 Vue 中 vnode 的唯一标记，通过这个 key，diff 操作可以更准确、更快速。

> Vue 的 diff 过程为：
>
> 1、分别用两个指针（startIndex, endIndex）表示 oldCh 和 newCh 的头尾节点
>
> 2、对指针所对应的节点做一个两两比较，判断是否属于同一节点
>
> 3、如果4种比较都没有匹配，那么判断是否有 key，有 key 就会用 key 去做一个比较；无 key 则会通过遍历的形式进行比较
>
> 4、比较的过程中，指针往中间靠，当有一个 startIndex > endIndex，则表示有一个已经遍历完了，比较结束

> 源码如下：

```javascript
  /* vue/src/core/vdom/patch.js */
  function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    let oldStartIdx = 0
    let newStartIdx = 0
    let oldEndIdx = oldCh.length - 1
    let oldStartVnode = oldCh[0]
    let oldEndVnode = oldCh[oldEndIdx]
    let newEndIdx = newCh.length - 1
    let newStartVnode = newCh[0]
    let newEndVnode = newCh[newEndIdx]
    let oldKeyToIdx, idxInOld, vnodeToMove, refElm

    // removeOnly is a special flag used only by <transition-group>
    // to ensure removed elements stay in correct relative positions
    // during leaving transitions
    const canMove = !removeOnly

    if (process.env.NODE_ENV !== 'production') {
      checkDuplicateKeys(newCh)
    }

    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
      if (isUndef(oldStartVnode)) {
        oldStartVnode = oldCh[++oldStartIdx] // Vnode has been moved left
      } else if (isUndef(oldEndVnode)) {
        oldEndVnode = oldCh[--oldEndIdx]
      } else if (sameVnode(oldStartVnode, newStartVnode)) {
        patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
        oldStartVnode = oldCh[++oldStartIdx]
        newStartVnode = newCh[++newStartIdx]
      } else if (sameVnode(oldEndVnode, newEndVnode)) {
        patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
        oldEndVnode = oldCh[--oldEndIdx]
        newEndVnode = newCh[--newEndIdx]
      } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
        patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
        canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm))
        oldStartVnode = oldCh[++oldStartIdx]
        newEndVnode = newCh[--newEndIdx]
      } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
        patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
        canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm)
        oldEndVnode = oldCh[--oldEndIdx]
        newStartVnode = newCh[++newStartIdx]
      } else {
        // 以上 4 种均匹配不到，通过 key 生成 key -> index 的 map（生成一次）
        if (isUndef(oldKeyToIdx)) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx)
        /**
         * 有 key 通过 key 比较，时间复杂度 O(n)
         * 无 key 时，每个 vnode 均需要遍历比较，时间复杂度  O(n²)
         */
        idxInOld = isDef(newStartVnode.key)
          ? oldKeyToIdx[newStartVnode.key]
          : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)
        if (isUndef(idxInOld)) { // New element
          createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
        } else {
          vnodeToMove = oldCh[idxInOld]
          if (sameVnode(vnodeToMove, newStartVnode)) {
            patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
            oldCh[idxInOld] = undefined
            canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm)
          } else {
            // same key but different element. treat as new element
            createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
          }
        }
        newStartVnode = newCh[++newStartIdx]
      }
    }
    if (oldStartIdx > oldEndIdx) {
      refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm
      addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue)
    } else if (newStartIdx > newEndIdx) {
      removeVnodes(oldCh, oldStartIdx, oldEndIdx)
    }
  }
```

### 自定义指令


## 生命周期

### 生命周期执行顺序

- 加载渲染过程

> beforeCreate -> created -> beforeMount -> mounted
>
> 父子组件
>
> 父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted

- 更新过程

> beforeUpdate -> updated
>
> 父子组件
>
> 父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated

- 销毁过程

> beforeDestroy -> destroyed
>
> 父子组件
>
> 父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

### 父组件可以监听到子组件的生命周期吗？

- 子组件通过 `emit` 发送事件

- 通过 @hook 监听

```javascript
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
}

//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
}
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...
```

## 双向绑定

### 直接给一个数组项赋值，Vue 能检测到变化吗?

### 双向绑定的原理

## 路由

### vue-router 路由模式有几种？

> hash:  使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器；
>
> history :  依赖 HTML5 History API 和服务器配置。具体可以查看 HTML5 History 模式；
>
> abstract :  支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式.

### 能说下 vue-router 中常用的 hash 和 history 路由模式实现原理吗？

> hash

> URL 中 hash 值只是客户端的一种状态，也就是说当向服务器端发出请求时，hash 部分不会被发送；
>
> hash 值的改变，都会在浏览器的访问历史中增加一个记录。因此我们能通过浏览器的回退、前进按钮控制hash 的切换；
>
> 可以通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 的 hash 值会发生改变；或者使用  JavaScript 来对 loaction.hash 进行赋值，改变 URL 的 hash 值；
>
> 我们可以使用 hashchange 事件来监听 hash 值的变化，从而对页面进行跳转（渲染）。

> history

> pushState 和 repalceState 两个 API 来操作实现 URL 的变化 ；
>
> 我们可以使用 popstate  事件来监听 url 的变化，从而对页面进行跳转（渲染）；
>
> history.pushState() 或 history.replaceState() 不会触发 popstate 事件，这时我们需要手动触发页面跳转（渲染）。

## 其他

### 组件中 data 为什么是一个函数，而 new Vue 实例里，data 可以直接是一个对象？

> 因为组件是来复用的，JS 中对象为引用数据类型，如果不用过函数限定作用域，那么组件中的 data 值会相互影响
>
> 而 new Vue 的实例不存在被复用的问题

### 样式加 scoped 与不加 scoped 的区别? 以及scoped 的实现原理


## 交流讨论

欢迎关注公众号【前端develop】
![前端develop](../img/qrcode.png)

