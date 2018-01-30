# 第4节 怎么开发并发布一个可用的 JavaScript 模块 ？

Javascript 那么流行，作为一个前端开发者，或者前端入门者，发布一个正式可用的 Javascript 模块，对于自己来说应该成长很大。下面就以一个简单的 Javascript 模块 [filesize.js](https://github.com/hustcc/filesize.js) 来介绍 github、travis-ci、npm 这些内容的组合使用。

## 一、目标概览

本文将使用 [filesize.js](https://github.com/hustcc/filesize.js) 这个作为介绍，这个是一个非常简单的 js 库，总共代码也不到 20 行，但是功能完善，可以生产使用。事实上 npm 上有不少简单到几行代码搞定的模块。

1. 首先发布一个 js 模块，肯定需要时代码开源，所以必须使用到 Github 作为代码管理。发布到 npm 能不能不开源呢，当然可以，但npm install 得到之后就是你的源码。
2. 发布一个 js 模块，需要让别人使用，良好的测试必不可少（很多项目有下图的绿色小图标），所以需要给你的项目写完善的测试用例（testcase），然后利用 Github 上第三方的 ci 工具来执行，travis-ci 就是其中一个用的比较多的。
3. 代码测试完毕，没有任何问题之后，可以 npm 发布模块了。

我们 filesize.js 达到的效果就是这样的：

![](/assets/import_filesize.js.png)

并且可以直接 npm install filesize.js 安装下载。下面分别介绍说明。

## 二、Github 项目初始化

首先在 Github 上创建项目，注意想好项目的名字，名字最好能够和之后 npm 发布的模块名字相同（npm 模块名字不能和其他人已经发布的名字重复）。后面关于 Github 如果提交代码这些请自行找其他文章学习。

需要强调的是：

1. 注意完善的 README.md 文档，可以文档先行，这样可以在写代码之前想好你这个模块的 API 方法等。
2. 可以尝试先写 testcase（测试先行），当然不强制，提前写 case，无法运行测试其实也挺耗时的。
3. 注意整理代码结构，对于 js 模块代码，一般是:
   ![](/assets/import_self_module.png)
   1. package.json 是 npm 模块相关的配置；
   2. glupfile.js 是 glup 的配置文件，我这里主要用来压缩代码；
   3. README.md 文档说明；
   4. .gitignore 忽略 git 版本管理的文件和目录（一些无关紧要的全部不要上传 git）；
   5. .travis.yml travis-ci配置文件，后面会说。

## 三、Javascript 模块 & npm

对于一个 js 模块，都有 package.json 文件，这个是一个严格要求的 json 格式，任何不符合要求的都会在执行 npm 命令的时候报错。比如 filesize.js 的该文件内容如下：

![](/assets/import_filesize.js_1.png)

这其中比较重要的就是 name/officialName、keywords、devDependencies、dependencies、scripts、main、version；

1. name：npm 发布之后的名字，一般和前述 github 项目名字保持一致，且不能和已有的 npm 模块重名；
2. keywords：npm 模块的关键字，用于在 npmjs.com 上检索；
3. devDependencies：开发依赖，比如会用到 glup 压缩混淆，tape 进行单元测试，jshint 进行静态代码检查；这些都是开发过程中需要的依赖，非开发环境可以不用安装；
4. dependencies：模块的依赖库，用户在使用你的这个模块的时候，也会附带进行安装下载；filesize.js模块很简单，并没有任何依赖。
5. scripts：配置一些指令，比如：npm run lint，npm run test，npm run build：分别是代码检查，执行测试用例，build 压缩库
6. main：这个是 package.json 中非常重要的配置，标志引用这个模块的入口在哪里；
7. version：模块的版本，每次发布的时候，比如保证版本不一样。

除了这些配置，package.json 还有其他的更多配置，大家可以去搜索学习一下，一般有了这些配置项，就够了，就可以进行 npm publish 来发布模块。

但是，我们发布之前，需要做一些持续集成和单元测试，用来保证代码的正确性，稳定性。这个就是使用到 travis-ci 了。

## 四、接入 travis-ci 持续集成

持续集成是什么？简单来说就是尽快，尽可能早的进行代码的正确性验证（也就是测试），那么我们每次在 github push代码的时候就运行一下测试用例，这个是不是很快，很早，也就是持续集成，对于一些简单的 js 模块，我们做一些简单的单元测试就够够的了。

在 github 创建项目之后，可以打开 https://travis-ci.org/，并使用 Github 授权登陆，然后自己的 Account 菜单中勾上需要接入 travis-ci 的项目，如下图所示：

![](/assets/import_travis-ci.png)

如果看不到新创建的项目，可以右上角刷新一下。开启之后，travis-ci 会 hook 住你 git push 命令，然后根据你项目中的 .travis.yml 配置文件来执行 npm test（npm run test 的简写）进行测试，并捕获检测结果来判断 测试用例 执行成功与否。例如 filesize.js的 .travis.yml 配置如下所示：

![](/assets/import_travis-ci.yml.png)

表示执行在 nodejs 环境，两个不同的大版本分别执行以下，一般选择 4.x 版本即可。那么每次 push 代码之后，都会进行自动触发 ci 任务，如下图所示：

![](/assets/import_travis-ci_2.png)

然后你就可以把这个小绿色图表放到你的 README.md 文件中了。

## 五、发布 publish

项目创建了，package.json 配置好了，代码写完了，文档完善了，ci 执行没有错误了，那么就可以发布出去了。怎么发布，非常简单，如果你没有 https://www.npmjs.com/ 的账号，注册一个就好了，和一般的网站注册并没有什么区别。

有了账号之后，在项目代码根目录，执行：

```bash
npm publish
```

控制台会要求你输入 npmjs 网站的邮箱和密码，输入即可，然后等着出现 发布成功即可，一般出现：filesize.js@1.0.1 这样格式的字符串即可，然后去 npmjs 网站刷一下试试看。

## 六、其他

如果你做了一个很好用的有创意的模块，可以给你的模块做一个简单炫酷的主页来显示用法和 API 接口吧。本文中作为示例的项目 filesize.js 是一个超级大轮子，就是为了写这篇文章而做了，当然也可以用户开发生成环境。

最后安利一下，我的 Github 主页：[https://github.com/hustcc](https://github.com/hustcc)，ID是 hustcc，因为我是一个huster，正好之前注册了hust.cc域名，所以有了这个 github id 。

---

原文：[https://my.oschina.net/wzwahl36/blog/750756](https://my.oschina.net/wzwahl36/blog/750756)

