## webpack构建速度优化

1.减少需要构建的东西

按需引入（按路由拆分、项目按需引入、tree-shaking）

不改变的文件做成dll，webpack不会打包已经编译的文件

2.让Webpack更快的找到文件

alias（文件别名，不查找）

modules（node_modules）位置

设置loader的include和exclude，定位入口，排除不需要的文件

3.使用缓存和多进程构建

uglify并行

happypack

webpack:cache

loader:cacheDirectory

## webpack对加载时间的优化




