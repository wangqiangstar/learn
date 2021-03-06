###  什么是虚拟DOM

**本质上是js对象，这个对象就是更加轻量级的对 DOM 的描述**，其实就是一个复杂一点的对象而已



### 为什么要有虚拟DOM

做过 JS 应用优化的人可能都知道，DOM 是复杂的，对它的操作（尤其是查询和创建）是非常慢非常耗费资源的。看下面的例子，仅创建一个空白的 div，其实例属性就达到 231 个。

```
// Chrome v63
const div = document.createElement('div');
let m = 0;
for (let k in div) {
  m++;
}
console.log(m); // 231
```

对于 DOM 这么多属性，其实大部分属性对于做 Diff 是没有任何用处的，所以如果用更轻量级的 JS 对象来代替复杂的 DOM 节点，然后把对 DOM 的 diff 操作转移到 JS 对象，就可以避免大量对 DOM 的查询操作。这个更轻量级的 JS 对象就称为 Virtual DOM 。

那么现在的过程就是这样：

1. 维护一个使用 JS 对象表示的 Virtual DOM，与真实 DOM 一一对应
2. 对前后两个 Virtual DOM 做 diff ，生成**变更**（Mutation）
3. 把变更应用于真实 DOM，生成最新的真实 DOM

因为要把变更应用到真实 DOM 上，所以还是避免不了要直接操作 DOM ，但是 React 的 diff 算法会把 DOM 改动次数降到最低。



### Diff

在初期我们可以看到，数据的变动导致整个页面的刷新，这种效率很低，因为可能是局部的数据变化，但是要刷新整个页面，造成了不必要的开销。



所以就有了 Diff 过程，将数据变动前后的 DOM 结构先进行比较，找出两者的不同处，然后再对不同之处进行更新渲染。

但是由于整个 DOM 结构又太大，所以采用了更轻量级的对 DOM 的描述—虚拟 DOM。



不过需要注意的是，虚拟 DOM 和 Diff 算法的出现是为了解决由命令式编程转变为声明式编程、数据驱动后所带来的性能问题的。换句话说，**直接操作 DOM 的性能并不会低于虚拟 DOM 和 Diff 算法，甚至还会优于。**



这么说的原因是因为 Diff 算法的比较过程，比较是为了找出不同从而有的放矢的更新页面。但是比较也是要消耗性能的。而直接操作 DOM 就是有的放矢，我们知道该更新什么不该更新什么，所以不需要有比较的过程。所以直接操作 DOM 效率可能更高。

React 厉害的地方并不是说它比 DOM 快，而是说不管你数据怎么变化，我都可以以最小的代价来进行更新 DOM。 方法就是我在内存里面用新的数据刷新一个虚拟 DOM 树，然后新旧 DOM 进行比较，找出差异，再更新到 DOM 树上。

框架的意义在于为你掩盖底层的 DOM 操作，让你用更声明式的方式来描述你的目的，从而让你的代码更容易维护。没有任何框架可以比纯手动的优化 DOM 操作更快，因为框架的 DOM 操作层需要应对任何上层 API 可能产生的操作，它的实现必须是普适的。

- [别再说虚拟 DOM 快了，要被打脸的](https://mp.weixin.qq.com/s/XR3-3MNCYY2pg6yVwVQohQ)
- [深入理解虚拟 DOM，它真的不快](https://mp.weixin.qq.com/s/cz5DBpqFiadL4IQofiWY3A)
- [网上都说操作真实 DOM 慢，但测试结果却比 React 更快，为什么](https://www.zhihu.com/question/31809713)



> 另外再提一个点，很多人会把 Diff 、数据更新、提升性能等概念绑定起来，但是你想想这个问题：React 由于只触发更新,而不能知道精确变化的数据,所以需要 diff 来找出差异然后 patch 差异队列。Vue 采用数据劫持的手段可以精准拿到变化的数据,为什么还要用虚拟DOM？



### 虚拟DOM的作用

要想回答上面那个问题，真的不要仅仅以为虚拟 DOM 或者 React 是来解决性能问题的，好处可还有很多呢。下面我总结了一些虚拟 DOM 好作用。

- **Virtual DOM 在牺牲(牺牲很关键)部分性能**的前提下，增加了可维护性，这也是很多框架的通性。
- 实现了对 DOM 的集中化操作，在数据改变时先对虚拟 DOM 进行修改，再反映到真实的 DOM中，用最小的代价来更新DOM，提高效率(提升效率要想想是跟哪个阶段比提升了效率，别只记住了这一条)。
- 打开了函数式 UI 编程的大门。
- 可以渲染到 DOM 以外的端，使得框架跨平台，比如 ReactNative，React VR 等。
- 可以更好的实现 SSR，同构渲染等。这条其实是跟上面一条差不多的。
- 组件的高度抽象化。

既然虚拟 DOM 有这么多作用，那么上面的问题，Vue 采用虚拟 DOM 的原因是什么呢？



### 虚拟 DOM 的缺点

- 首次渲染大量 DOM 时，由于多了一层虚拟 DOM 的计算，会比 innerHTML 插入慢。
- 虚拟 DOM 需要在内存中的维护一份 DOM 的副本(更上面一条其实也差不多，上面一条是从速度上，这条是空间上)。
- 如果虚拟 DOM 大量更改，这是合适的。但是单一的，频繁的更新的话，虚拟 DOM 将会花费更多的时间处理计算的工作。所以，如果你有一个DOM 节点相对较少页面，用虚拟 DOM，它实际上有可能会更慢。但对于大多数单页面应用，这应该都会更快。

### 总结

虚拟 DOM 并没有像其他文章一样去解释它的实现以及相关的 Diff 算法，关于 Diff 算法可以看这篇 [虚拟 DOM 到底是什么？](https://mp.weixin.qq.com/s/oAlVmZ4Hbt2VhOwFEkNEhw)文中介绍了很多库的 diff 算法，可见其实 React 的 diff 算法并不算太快。

而是通过历史来得出他的价值体现，从历史怎么看大牛们是怎么一步一步的去解决问题，从历史中看为什么别人能做出这么伟大的东西，而我们不能？

每个伟大的产品都会有非常多的背景支持，都是一步一步发展而来的。

另外洗清了一个错误观念：**很多人认为虚拟 DOM 最大的优势是 diff 算法，减少 JavaScript 操作真实 DOM 的带来的性能消耗。**

虽然这一个虚拟 DOM 带来的一个优势，但并不是全部。虚拟 DOM 最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种 GUI。