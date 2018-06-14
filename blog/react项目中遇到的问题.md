## less打包输出后生成很多重复的样式
如果在一个less中用@import的方式引入另一个less文件，会重复输出样式

解决方式：使用@import (reference) "xxx.less"方式引入

相关讨论：[讨论1](https://axiu.me/coding/prevent-less-and-sass-common-part-generate-duplicate-css-code/)，[讨论2](https://github.com/vuejs/vue-loader/issues/110)，[讨论3](https://www.zhihu.com/question/58716465)，[附加，打包样式文件加快](https://juejin.im/post/5aed8ab5f265da0b8f626fa7)