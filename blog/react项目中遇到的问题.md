## less打包输出后生成很多重复的样式
如果在一个less中用@import的方式引入另一个less文件，会重复输出样式

解决方式：使用@import (reference) "xxx.less"方式引入

相关讨论：[讨论1](https://axiu.me/coding/prevent-less-and-sass-common-part-generate-duplicate-css-code/)，[讨论2](https://github.com/vuejs/vue-loader/issues/110)，[讨论3](https://www.zhihu.com/question/58716465)，[附加，打包样式文件加快](https://juejin.im/post/5aed8ab5f265da0b8f626fa7)

## react 路由状态保存问题
想找到如同vue-router keep-alive的功能，有个库，react-keeper,讨论见[解决方案](https://blog.csdn.net/yuzhongzi81/article/details/79089122)，[react-router解决方案](https://blog.csdn.net/cen_cs/article/details/77977385)，[官方github讨论](https://github.com/facebook/react/issues/12039)，[react-keeper说明](https://zhuanlan.zhihu.com/p/26308250)

## 在组件的 render() 方法中顶层必须包裹为单节点，容易层级加深
[render返回数组；用fragment](https://juejin.im/post/5b2236016fb9a00e9c47cb6b?utm_source=gold_browser_extension)