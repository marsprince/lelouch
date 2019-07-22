## dependency
required to run：程序跑起来的必须库

## peerDependencies
3.0之后：可以理解为宿主，常规的dependency会产生多个包，而peer不会，如果版本冲突，会报错

添加peer，意味着：

My code is compatible with this version of the package.

If this package already exists in node_modules, do nothing.

If this package doesn’t already exist in the node_modules directory or it is the wrong version, don’t add it. But, show a warning to the user that it wasn’t found.

## 版本冲突

如果在dependency中有冲突，最后安装出来是这样的

```markdown
node_modules
├── lodash 4.17.11
├── todd-a 1.0.0
├── todd-b 1.0.0
│   └── node_modules
│       └── todd-child 2.0.0
└── todd-child 1.0.0
```
多个版本有的时候很好，但是有的时候会引发冲突

如果我们不想有多个版本，就需要将依赖加入peerDependencies，提示用户需要解决

## yarn resolution
指定安装版本，核心是让用户自己决定是怎么使用冲突版本

参考： https://github.com/yarnpkg/rfcs/blob/master/implemented/0000-selective-versions-resolutions.md

