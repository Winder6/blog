# 事件绑定
使用传统方式开发网页，我们容易注意到用事件委托来处理多个同级元素绑定的相同事件。但是使用Vue框架的时候就容易忽略这个问题，从而写下如下代码
```html
<ul>
    <li v-for="(item, index) in data" @click="handleClick(index)">
        {{index}
    </li>
</ul>
```
当我们查看页面元素的event listener，是可以看到每个li上面绑定了事件，Vue并没有对此进行优化，而是给每个li元素绑定了事件，当元素多了会影响性能，而react其实是对此进行了优化的，它把所有事件绑定到了document元素（[详情](https://zhuanlan.zhihu.com/p/27132447)），所以遇到此种场景，就视元素数量以及性能要求而定，决定是否使用事件委托。