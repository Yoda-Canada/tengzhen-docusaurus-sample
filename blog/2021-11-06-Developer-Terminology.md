---
slug: Developer-Terminology-post
title: Developer must know terminology
authors: Collective
tags: [node, yarn, nmp,]
---

1. Node.js是用来做什么的？
2. npm 是干什么的？（非教程）
3. Yarn是什么？
4. Yarn yarn.lock文件
5. npm和yarn的区别，我们该如何选择？
6.
7.
8.
9.
10.
11. 
12. 
13. 
14. 
15. Babel 是什么？
16. cname记录是什么？他存在的意义是什么？

## Node.js是用来做什么的？

** 回答 1：**

一种javascript的运行环境，能够使得javascript脱离浏览器运行。

** 回答 2：**

国外有一篇非常好的Node.js 介绍文章，从原理入手讲解，在这里给大家翻译一下（本人非翻译出身，一些地方结合了点个人理解，有错误欢迎指出）。原文地址 [Node.js is the New Black](https://www.sitepoint.com/node-js-is-the-new-black/)
译文如下：
如果你去年注意过技术方面的新闻，我敢说你至少看到node.js不下一两次。那么问题来了“node.js是什么？”。有些人没准会告诉你“这是一种通过JavaScript语言开发web服务端的东西”。如果这种晦涩解释还没把你搞晕，你没准会接着问：“为什么我们要用node.js？”，别人一般会告诉你：node.js有非阻塞，事件驱动I/O等特性，从而让高并发（high concurrency）在的轮询（Polling）和comet构建的应用中成为可能。当你看完这些解释觉得跟看天书一样的时候，你估计也懒得继续问了。不过没事。我这篇文章就是在避开高端术语的同时，帮助你你理解node.js的。浏览器给网站发请求的过程一直没怎么变过。当浏览器给网站发了请求。服务器收到了请求，然后开始搜寻被请求的资源。如果有需要，服务器还会查询一下数据库，最后把响应结果传回浏览器。不过，在传统的web服务器中（比如Apache），每一个请求都会让服务器创建一个新的进程来处理这个请求。后来有了Ajax。有了Ajax，我们就不用每次都请求一个完整的新页面了，取而代之的是，每次只请求需要的部分页面信息就可以了。这显然是一个进步。但是比如你要建一个FriendFeed这样的社交网站（类似人人网那样的刷朋友新鲜事的网站），你的好友会随时的推送新的状态，然后你的新鲜事会实时自动刷新。要达成这个需求，我们需要让用户一直与服务器保持一个有效连接。目前最简单的实现方法，就是让用户和服务器之间保持长轮询（long polling）。HTTP请求不是持续的连接，你请求一次，服务器响应一次，然后就完了。长轮训是一种利用HTTP模拟持续连接的技巧。具体来说，只要页面载入了，不管你需不需要服务器给你响应信息，你都会给服务器发一个Ajax请求。这个请求不同于一般的Ajax请求，服务器不会直接给你返回信息，而是它要等着，直到服务器觉得该给你发信息了，它才会响应。比如，你的好友发了一条新鲜事，服务器就会把这个新鲜事当做响应发给你的浏览器，然后你的浏览器就刷新页面了。浏览器收到响应刷新完之后，再发送一条新的请求给服务器，这个请求依然不会立即被响应。于是就开始重复以上步骤。利用这个方法，可以让浏览器始终保持等待响应的状态。虽然以上过程依然只有非持续的Http参与，但是我们模拟出了一个看似持续的连接状态我们再看传统的服务器（比如Apache）。每次一个新用户连到你的网站上，你的服务器就得开一个连接。每个连接都需要占一个进程，这些进程大部分时间都是闲着的（比如等着你好友发新鲜事，等好友发完才给用户响应信息。或者等着数据库返回查询结果什么的）。虽然这些进程闲着，但是照样占用内存。这意味着，如果用户连接数的增长到一定规模，你服务器没准就要耗光内存直接瘫了。这种情况怎么解决？解决方法就是刚才上边说的：非阻塞和事件驱动。这些概念在我们谈的这个情景里面其实没那么难理解。你把非阻塞的服务器想象成一个loop循环，这个loop会一直跑下去。一个新请求来了，这个loop就接了这个请求，把这个请求传给其他的进程（比如传给一个搞数据库查询的进程），然后响应一个回调（callback）。完事了这loop就接着跑，接其他的请求。这样下来。服务器就不会像之前那样傻等着数据库返回结果了。如果数据库把结果返回来了，loop就把结果传回用户的浏览器，接着继续跑。在这种方式下，你的服务器的进程就不会闲着等着。从而在理论上说，同一时刻的数据库查询数量，以及用户的请求数量就没有限制了。服务器只在用户那边有事件发生的时候才响应，这就是事件驱动。FriendFeed是用基于Python的非阻塞框架Tornado (知乎也用了这个框架) 来实现上面说的新鲜事功能的。不过，Node.js就比前者更妙了。Node.js的应用是通过javascript开发的，然后直接在Google的变态V8引擎上跑。用了Node.js，你就不用担心用户端的请求会在服务器里跑了一段能够造成阻塞的代码了。因为javascript本身就是事件驱动的脚本语言。你回想一下，在给前端写javascript的时候，更多时候你都是在搞事件处理和回调函数。javascript本身就是给事件处理量身定制的语言。Node.js还是处于初期阶段。如果你想开发一个基于Node.js的应用，你应该会需要写一些很底层代码。但是下一代浏览器很快就要采用WebSocket技术了，从而长轮询也会消失。在Web开发里，Node.js这种类型的技术只会变得越来越重要。

** 回答 3： **
作者：张戎
链接：https://www.zhihu.com/question/33578075/answer/252611915
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

阅读本帖需要先复习小学语文课文，华罗庚的《统筹方法》。比如，想泡壶茶喝。当时的情况是：开水没有；水壶要洗，茶壶茶杯要洗；火生了，茶叶也有了。怎么办？ 办法甲：洗好水壶，灌上凉水，放在火上；在等待水开的时间里，洗茶壶、洗茶杯、拿茶叶；等水开了，泡茶喝。 办法乙：先做好一些准备工作，洗水壶，洗茶壶茶杯，拿茶叶；一切就绪，灌水烧水；坐待水开了泡茶喝。 办法丙：洗净水壶，灌上凉水，放在火上，坐待水开；水开了之后，急急忙忙找茶叶，洗茶壶茶杯，泡茶喝。 哪一种办法省时间？我们能一眼看出第一种办法好，后两种办法都窝了工。... 从这个图上可以一眼看出，办法甲总共要16分钟（而办法乙、丙需要20分钟）。...看来这是“小题大做”，但在工作环节太多的时候，这样做就非常有必要了。...来源：华罗庚 统筹方法_百度文库Node.js就是跟华罗庚一伙的，帮助我们更快地沏茶，而且更快地给一帮人沏茶。我们在用浏览器访问服务器的时候，就好像去茶叶铺买茶喝。我们可以买回来自己沏，也可以在店里自带的茶座儿坐下来喝现成的。如果我们买回来喝，参考华老师的课文，他自己都说了这是「小题大作」，因为16分钟和20分钟差不了太多，就我们自己和家人，慢生活4分钟也没什么毛病。但是如果我们在店里喝，那可不是咱一家儿等着。比起16分钟的最少等候时间，第一桌多等4分钟，第二桌要同时来的话得多等24分钟，第三桌站起来走了，20多张桌子都闲着。忙活了半天，茶客不耐烦，茶铺不赚钱。所以，茶叶铺一定要学好小学课文，不仅对于同一桌的不同请求要统筹安排，别认死理一件事不干完绝不开始下一件，甚至对于不同桌的不同请求也要尽量并行处理，比如等开水时可以帮好几桌点单、拿茶叶、洗茶杯茶壶，水开之前别闲着，水一开就给各桌上茶，让每桌客人都感觉嗖快嗖快的。

在这个故事里，茶叶铺就是网络服务器。我们自己就是浏览器。我们要是不想浏览器事必躬亲，那就把活扔给服务器干；当服务器一下子服务很多浏览器时就不能认死理非要串行操作，要灵活统筹，同时开始几件事，哪件完事关闭哪件。

这三个特征用江湖切口说就叫：
* 服务器端JavaScript处理：` server-side JavaScript execution `
* 非阻断/异步I/O：` non-blocking or asynchronous I/O `
* 事件驱动：` Event-driven `
Node.js 就是这样一个服务器端的、非阻断式I/O的、事件驱动的JavaScript运行环境。所以说，Node.js是华罗庚的路数，帮茶叶铺更快更多地伺候茶客。这麻利的店小二谁家养的？大家。因为Node.js是开源的。


原始链接：https://www.zhihu.com/question/33578075

## npm 是干什么的？（非教程）
社区
程序员自古以来就有社区文化：

社区的意思是：拥有共同职业或兴趣的人们，自发组织在一起，通过分享信息和资源进行合作。虚拟社区的参与者经常会在线讨论相关话题，或访问某些网站。
前端程序员也有社区，世界上最大的前端社区应该就是 GitHub 了。前端通过 GitHub 来

分享源代码（线上代码仓库）
讨论问题（Issue 列表）
收集学习资源和常去的网站（比如我收集的优质中文前端博客）


加入社区最大的好处之一是，你可以使用别人贡献的代码，你也可以贡献代码给别人用。

麻烦
当一个网站依赖的代码越来越多，程序员发现这是一件很麻烦的事情：

去 jQuery 官网下载 jQuery
去 BootStrap 官网下载 BootStrap
去 Underscore 官网下载 Underscore
……

有些程序员就受不鸟了，一个拥有三大美德的程序员 Isaac Z. Schlueter （以下简称 Isaaz）给出一个解决方案：用一个工具把这些代码集中到一起来管理吧！

这个工具就是他用 JavaScript （运行在 Node.js 上）写的 npm，全称是 Node Package Manager

具体步骤
NPM 的思路大概是这样的：

1. 买个服务器作为代码仓库（registry），在里面放所有需要被共享的代码

2. 发邮件通知 jQuery、Bootstrap、Underscore 作者使用 npm publish 把代码提交到 registry 上，分别取名 jquery、bootstrap 和 underscore（注意大小写）

3. 社区里的其他人如果想使用这些代码，就把 jquery、bootstrap 和 underscore 写到 package.json 里，然后运行 npm install ，npm 就会帮他们下载代码

4. 下载完的代码出现在 node_modules 目录里，可以随意使用了。

这些可以被使用的代码被叫做「包」（package），这就是 NPM 名字的由来：Node Package(包) Manager(管理器)。

发展
Isaaz 通知 jQuery 作者 John Resig，他会答应吗？这事儿不一定啊，对不对。

只有社区里的人都觉得 「npm 是个宝」的时候，John Resig 才会考虑使用 npm。

那么 npm 是怎么火的呢？

npm 的发展是跟 Node.js 的发展相辅相成的。

Node.js 是由一个在德国工作的美国程序员 Ryan Dahl 写的。他写了 Node.js，但是 Node.js 缺少一个包管理器，于是他和 npm 的作者一拍即合、抱团取暖，最终 Node.js 内置了 npm。

后来的事情大家都知道，Node.js 火了。

随着 Node.js 的火爆，大家开始用 npm 来共享 JS 代码了，于是 jQuery 作者也将 jQuery 发布到 npm 了。

所以现在，你可以使用 npm install jquery 来下载 jQuery 代码。

现在用 npm 来分享代码已经成了前端的标配。

后续
Node.js 目前由 Ryan Dahl 当时所在的公司 joyent 继续开发。Ryan Dahl 现在已经去研究 AI 和机器学习了，并且他把 Node.js 的维护权交给了 Isaaz。（我是不是也应该去研究 AI 和机器学习啊教练）

而 Isaaz 维护了一段时间后，辞职了，成立了一个公司专门维护 npm 的 registry，公司名叫做 npm 股份有限公司……谁说开源不能赚钱的~

原始链接：https://zhuanlan.zhihu.com/p/24357770

## Yarn是什么？

姓名：岳沁  学号：17101223458

转载自：https://yarnpkg.com/zh-Hans/docs/usage

Yarn 对你的代码来说是一个包管理器， 你可以通过它使用全世界开发者的代码，或者分享自己的代码。 Yarn 做这些快捷、安全、可靠，所以你不用担心什么。

通过Yarn你可以使用其他开发者针对不同问题的解决方案，使自己的开发过程更简单。 使用过程中遇到问题，你可以将其上报或者贡献解决方案。一旦问题被修复，Yarn会更新保持同步。

代码通过包（package）（或者称为模块（module））的方式来共享。 一个包里包含所有需要共享的代码，以及描述包信息的文件，称为package.json。

作者：丘之心
原始链接：https://www.jianshu.com/p/1e8510826703


## Yarn yarn.lock文件

为了在多台机器之间获得一致的安装结果，Yarn 可能会需要比 package.json 文件中配置的依赖项更多的信息。它需要准确存储每一个依赖项的安装版本。因此在 Yarn 项目的根目录我们需要一个 yarn.lock 文件，这个 yarn.lock 文件是自动生成的。

当我们执行 yarn 命令或者添加依赖包命令后，Yarn 都会在项目根目录下自动生成一个 yarn.lock 文件。在使用 Yarn 安装、升级、删除依赖项目时，会自动更新到 yarn.lock 文件中。一般我们不会去手动编辑这个文件，因为很容易破坏这个文件。

示例：
例如我们安装了一些依赖包，那么 yarn.lock 文件内容类似所示格式：

copy-descriptor@^0.1.0: 
  version "0.1.1" 
  resolved "https://registry.yarnpkg.com/copy-descriptor/-/copy-descriptor-0.1.1.tgz#676f6eb3c39997c2ee1ac3a924fd6124748f578d"
  integrity sha1-Z29us8OZl8LuGsOpJP1hJHSPV40= 
core-util-is@~1.0.0:
  version "1.0.2"
  resolved "https://registry.yarnpkg.com/core-util-is/-/core-util-is-1.0.2.tgz#b5fd54220aa2bc5ab57aab7140c940754503c1a7"
  integrity sha1-tf1UIgqivFq1eqtxQMlAdUUDwac=

create-ecdh@^4.0.0:
  version "4.0.3"
  resolved "https://registry.yarnpkg.com/create-ecdh/-/create-ecdh-4.0.3.tgz#c9111b6f33045c4697f144787f9254cdc77c45ff"
  integrity sha512-GbEHQPMOswGpKXM9kCWVrremUcBmjteUaQ01T9rkKCPDXfUHX0IoP9LpHYo2NPFampa4e+/pFDc3jQdxrxQLaw==
  dependencies:
    bn.js "^4.1.0"
    elliptic "^6.0.0" 

可以明显看到 yarn.lock 文件中的信息比 package.json 文件中详细了很多。

在实际项目中，yarn.lock 文件也很有用处，我们可以将 yarn.lock 提交到版本库中，其他成员就可以通过 yarn install 获取所有依赖包，这个可以保证大家安装的依赖是完全一致的，避免产生 bug。

原文链接：https://segmentfault.com/a/1190000039820351


## npm和yarn的区别，我们该如何选择？

Yarn是什么？
“Yarn是由Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具 ，正如官方文档中写的，Yarn 是为了弥补 npm 的一些缺陷而出现的。”这句话让我想起了使用npm时的坑了：
- npm install的时候巨慢。特别是新的项目拉下来要等半天，删除node_modules，重新install的时候依旧如此。
- 同一个项目，安装的时候无法保持一致性。由于package.json文件中版本号的特点，下面三个版本号在安装的时候代表不同的含义。

"5.0.3",
"~5.0.3",
"^5.0.3"
“5.0.3”表示安装指定的5.0.3版本，“～5.0.3”表示安装5.0.X中最新的版本，“^5.0.3”表示安装5.X.X中最新的版本。这就麻烦了，常常会出现同一个项目，有的同事是OK的，有的同事会由于安装的版本不一致出现bug。
- 安装的时候，包会在同一时间下载和安装，中途某个时候，一个包抛出了一个错误，但是npm会继续下载和安装包。因为npm会把所有的日志输出到终端，有关错误包的错误信息就会在一大堆npm打印的警告中丢失掉，并且你甚至永远不会注意到实际发生的错误。

带着这些坑，我开始了解Yarn的优势及其解决的问题。

Yarn的优点？
速度快 。速度快主要来自以下两个方面：
并行安装：无论 npm 还是 Yarn 在执行包的安装时，都会执行一系列任务。npm 是按照队列执行每个 package，也就是说必须要等到当前 package 安装完成之后，才能继续后面的安装。而 Yarn 是同步执行所有任务，提高了性能。
离线模式：如果之前已经安装过一个软件包，用Yarn再次安装时之间从缓存中获取，就不用像npm那样再从网络下载了。
安装版本统一：为了防止拉取到不同的版本，Yarn 有一个锁定文件 (lock file) 记录了被确切安装上的模块的版本号。每次只要新增了一个模块，Yarn 就会创建（或更新）yarn.lock 这个文件。这么做就保证了，每一次拉取同一个项目依赖时，使用的都是一样的模块版本。npm 其实也有办法实现处处使用相同版本的 packages，但需要开发者执行 npm shrinkwrap 命令。这个命令将会生成一个锁定文件，在执行 npm install 的时候，该锁定文件会先被读取，和 Yarn 读取 yarn.lock 文件一个道理。npm 和 Yarn 两者的不同之处在于，Yarn 默认会生成这样的锁定文件，而 npm 要通过 shrinkwrap 命令生成 npm-shrinkwrap.json 文件，只有当这个文件存在的时候，packages 版本信息才会被记录和更新。
更简洁的输出：npm 的输出信息比较冗长。在执行 npm install 的时候，命令行里会不断地打印出所有被安装上的依赖。相比之下，Yarn 简洁太多：默认情况下，结合了 emoji直观且直接地打印出必要的信息，也提供了一些命令供开发者查询额外的安装信息。
多注册来源处理：所有的依赖包，不管他被不同的库间接关联引用多少次，安装这个包时，只会从一个注册来源去装，要么是 npm 要么是 bower, 防止出现混乱不一致。
更好的语义化： yarn改变了一些npm命令的名称，比如 yarn add/remove，感觉上比 npm 原本的 install/uninstall 要更清晰。
Yarn和npm命令对比
npm install === yarn 
npm install taco --save === yarn add taco
npm uninstall taco --save === yarn remove taco
npm install taco --save-dev === yarn add taco --dev
npm update --save === yarn upgrade
npm的未来：npm5.0
有了yarn的压力之后，npm做了一些类似的改进。
1. 默认新增了类似yarn.lock的 package-lock.json；
2. git 依赖支持优化：这个特性在需要安装大量内部项目（例如在没有自建源的内网开发），或需要使用某些依赖的未发布版本时很有用。在这之前可能需要使用指定 commitid 的方式来控制版本。
3. 文件依赖优化：在之前的版本，如果将本地目录作为依赖来安装，将会把文件目录作为副本拷贝到 nodemodules 中。而在 npm5 中，将改为使用创建 symlinks 的方式来实现（使用本地 tarball 包除外），而不再执行文件拷贝。这将会提升安装速度。目前yarn还不支持。

总结

在npm5.0之前，yarn的优势特别明显。但是在npm之后，通过以上一系列对比，我们可以看到 npm5 在速度和使用上确实有了很大提升，值得尝试，不过还没有超过yarn。

综上我个人的建议是如果你已经在个人项目上使用 yarn，并且没有遇到更多问题，目前完全可以继续使用。但如果有兼容 npm 的场景，或者身处在使用 npm，cnpm，tnpm 的团队，以及还没有切到 yarn 的项目，那现在就可以试一试 npm5 了。

原文链接：https://zhuanlan.zhihu.com/p/27449990



## Babel 是什么？
Babel 是一个 JavaScript 编译器
Babel 是一个工具链，主要用于将采用 ECMAScript 2015+ 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。下面列出的是 Babel 能为你做的事情：

语法转换
通过 Polyfill 方式在目标环境中添加缺失的特性 （通过引入第三方 polyfill 模块，例如 core-js）
源码转换（codemods）
更多参考资料！（请查看这些 [视频](https://www.babeljs.cn/videos.html) 以获得启发）
原始链接：https://www.babeljs.cn/docs/

## cname记录是什么？他存在的意义是什么？

使用A记录和CNAME进行域名解析的区别A记录就是把一个域名解析到一个IP地址（Address，特制数字IP地址），而CNAME记录就是把域名解析到另外一个域名。其功能是差不多，CNAME将几个主机名指向一个别名，其实跟指向IP地址是一样的，因为这个别名也要做一个A记录的。但是使用CNAME记录可以很方便地变更IP地址。如果一台服务器有100个网站，他们都做了别名，该台服务器变更IP时，只需要变更别名的A记录就可以了。


原始链接：https://www.zhihu.com/question/22916306/answer/26650717



