# testlernarepo
这是一个测试使用 lerna 来进行多包管理的项目。

### 温馨提示：请直接看最后面的正确操作，如果不懂，再回顾全文。

#### 如何像 babel 那样进行多包管理呢？
babel 项目的 packages 里面存放了 babel 旗下的所有包，比如：babel-core。

我们在安装 babel 的时候一般不是这样装的：`yarn add babel`，而是这样安装的：`yarn add @babel/babel-core`，这是由于 babel 进行了分包管理的缘故，又因为 npm 支持`scope packages`，所以我们可以这样子来安装包。

我们再来看`scope packages`，npm 支持以@符开头的包名称，把它叫做有范围的包，示例：`@somescope/somepackage`。

但是在发布包的时候，npm 会把这种包当成是你的私有包来进行发布，一般我们是发布不了的，因为 npm 会提示需要登录等等东西，这个时候我们一般就加上一个参数，告诉 npm 我们要发布的这个包是一个公共包：`npm publish --access=public`，不出意外这个包就可以发布成功了。

别人要安装你的包：`yarn add @somescope/somepackage`，看起来跟安装 babel-core 一致了。

**但是还差点东西，就是利用 lerna 来进行管理。**

要达到类似于 babel 那样的管理方式，首先，你需要有`scope packages`，正好，我们现在有了一个`@somescope/somepackage`包，现在我们就用 lerna 来管理它。

首先，我们在 github 上面新建一个仓库，就叫做：testlerna 吧（使用已有仓库也行），然后克隆到本地，进入到 testlerna 目录，执行：`lerna init`或者是`lerna init -i`（表示 packages 下面的包单独使用版本号），这个时候，lerna 就为我们生成了这几个东西：
```
testlerna
|-package.json
|-lerna.json
|-packages/
```
packages 目录就是 lerna 要进行管理的各种包的目录，所有的包都会放在这个目录下面。

接着执行：`lerna import ../scopepackage`（假设咱们的这个`@somescope/somepackage`包是放在 scopepackage 目录里面的），这样就会把我们刚才发布的那个包导入到 packages 目录里面，这时候目录看起来像这个样子：
```
testlerna
|-package.json
|-lerna.json
|-packages/
|--scopepackage/
|---package.json
|---...
```
做一些修修改改并执行 git commit 后，我们就可以执行`lerna publish`了，不出意外你的`@somescope/somepackage`也在 npm 上更新了，原来的那个`@somescope/somepackage`仓库可以删除掉了，因为我们把它放在 testlerna 项目中进行管理了，这样就跟 babel 那样的管理方式一样了。

这种方式是有一丢丢麻烦，因为 npm 默认会把 @ 开头的包当成私有包来看待，就导致了如果不在 npm 上提交一次公共版本，执行`lerna publish`时就会发布不了，可能`lerna publish`调用的 npm 命令是`npm publish`它没有加`--access=publish`参数。

如果你不想这么麻烦，那么有一个办法就是，不用`scope package`，也就是你的包名不加 @ 前缀，这样你的包就默认是公共的包了，完全可以不用先去提交一次公共版本，才能使用 lerna 来管理你的包，简单多了。

更新，不用像上面那么麻烦了，还得新建一个仓库，你大可以把前面那种方法当成是学习 npm 的 scope package 吧。

正确的方式是这种：
使用 lerna init 后，packages 里面也有你的包了，这时候我们要先进行第一次发布，直接进入 packages/你的包目录，执行：`npm publish --access=public`，
发布完以后，下次你就可以在主项目里面使用：`lerna publish`来管理了。




