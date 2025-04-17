---
title: 上周老板让我研究MCP，我做了个动画给他···
tags:
  - llm
date: 2025-04-17
---
来自 轩辕之风O 轩辕的编程宇宙

 _2025年04月17日 08:26_ _四川_

大家好，我是轩辕。

最近我看了很多关于MCP技术的视频，讲得都很懵，不懂的看完还是不懂。

我花了一些时间，用**动画**的形式做了一个视频，由浅入深地把MCP分享给大家。作为时下流行的人工智能工程技术，**我强烈建议程序员们都学习一下**。虽然咱们不做大模型算法开发，但作为一门工程技术，把AI和咱们传统的软件开发领域相结合是大势所趋。

下面是发布在视频号的视频，可以点击查看：

如果你喜欢看文字稿的，可以继续往下看，内容有点长，建议先马后看！

## 一：AI 调用外部工具

这是一个大语言模型，如果你问它截止到今天，轩辕的编程宇宙总共有多少粉丝，他肯定回答不了你，因为它只是一个用一堆过时数据训练出来的东西，没法知道今天我有多少粉丝，但可以肯定的是，看完这个视频，你一定会为这个数字+1。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F24cdb91b7c69a7300a0f0e600cbce256-20250417095136324.png)
现在，我用Python写了一个爬虫程序，这个程序可以去爬我的主页，然后知道我有多少粉丝。 只要把这个程序和大语言模型做一下结合，它就能回答之前的这个问题了。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Ff8a2e2a03307acfee7eb76a811cab3df-20250417095146071.png)
那么问题来了，如何让大语言模型去调用这个工具呢？

首先，大语言模型没法直接调用，因为这个程序它在我本地电脑上啊，我这是个局域网，大模型怎么能访问呢？

既然没法直接访问，那找个中介来帮个忙。

我们再写一个程序，通过API的方式来访问大模型，而不是直接通过Web界面的聊天对话。 这个程序在通过API访问的时候，他告诉大模型说啊，我这里呢有个某某工具，它有什么功能，它如何使用，你要调用的话就给我说一声，我帮你调用，完事儿之后，把调用的结果同步给你。

现在你对这个程序问一句：轩辕的编程宇宙总共有多少粉丝，它就会把这个问题连同爬虫工具的使用说明书一起发给AI了。

AI分析问题之后，发现需要用到这个工具，然后返回消息，里面写到需要用到爬虫工具，并且把给这个工具传什么参数也一起返回了。这个中介程序收到一看，转头就按照AI的要求调用了爬虫工具，并且把爬虫返回的结果又上报给了AI。AI分析工具输出的内容之后，把最终的答复发给你。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fe5c8b4fffd3d61c8a6d5f2a95573418d-20250417095200071.png)
就这样绕了一大圈，实现了AI调用外部工具的功能。

## 二：Function Calling

那么问题来了，这个说明书该怎么写呢？

最简单的办法，那就直接写在提示词里面。你可以自己定义一套格式，把这个工具的名称、功能、参数、返回值都描述清楚，并且约定好如果要调用某个工具，返回的内容应该长什么样。

但几年前的时候AI遵循指令的效果并不好，你把说明书都给它了，白纸黑字的写着让它按照说明书上的方式跟你对接，但它完全可能就是不听话，嘴上说着好好好，实际返回乱七八糟的东西，搞得中介程序并不知道AI想要使用什么工具。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F09fe8f1579925ae363bb32064d2f9228-20250417095214883.png)
于是，OpenAI整了个规范，把这个过程进行了标准化。工具使用说明书该放在哪里，格式是什么，如果要调用，返回给中介程序的格式又是什么等等这些都约定好。

具体来说，它用一个JSON形式描述这些外部工具的信息，并且和用户输入的提示词分离，这样AI就能更清楚的知道这些工具的信息了。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Ff44dc696ede6f5dec30d8cc63e0944c3-20250417095227588.png)
同时，AI返回的结果中，也用专门的字段来明确指示了需要调用哪些工具，用什么样的参数去调用。 这样就清楚明白多了，这就是**Function Calling**技术。

至于这个中介程序，一个典型的例子就是`Dify`、`Coze` 这样的AI智能体开发平台。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fdf4c781ce1c562fdba36af215b0248f5-20250417095245423.png)
所有外部工具，都可以通过HTTP暴露接口出来，然后我们可以在Dify上把外部工具的API schema配置好，Dify就能拿着API schema信息转换为Function Calling所需要的工具使用说明书格式，让AI调用外部的工具了。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F37c430849c60f625bb374b633b17a0d1-20250417095302327.png)
## 三：MCP，模型上下文协议

Function Calling非常好用，但是有一点比较明显的缺点，就是这个工具使用说明书写起来是有点复杂的，对应到Dify上，我们要为每一个外部工具都要写一个API schema描述，非常麻烦。实际场景中，一个AI智能体可能有几十上百个外部工具，每一个都要这样操作，而且接口一旦有变化，都要更新维护，非常麻烦。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F1cefdb385d566c226e9747fa5a17bbf9-20250417095319315.png)
另外还有一个问题就是让这些工具都要暴露接口，安全也是一个问题。

如果一个事情很麻烦，但又有商业价值驱动，那这个麻烦一定会被解决。于是一个新的方案出现了，这就是今天的主角：**MCP，模型上下文协议**。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F2685ff4ba332cf8da60551c361a0f35f-20250417095331915.png)
既然每个工具函数都要这么描述一番很麻烦，那就封装一层，毕竟，没有什么事是封装一层解决不了的，如果有，那就再封装一层。

现在你们这些外部工具不要直接这样通过HTTP API的方式暴露出来了，给你们套一层壳，通过这个壳统一对外提供服务，然后中介程序再和这个壳打交道。

这些工具被壳包裹之后，就成了一个个的Server，中介程序里面负责和Server打交道的这部分程序就是Client，它们之间使用的通信协议就是MCP协议，模型上下文协议，这些Server就是MCP Server，Client呢就是MCP Client。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fc347da32612d6c543e1978ac02f42859-20250417095348896.png)
而这个容纳MCP Client的中介程序，就是MCP Host，典型的代表有Claude桌面程序、VSCode中的Cline插件等等。

有了这个壳，现在要编写外部工具变得非常简单了。以Python为例，这里可以使用MCP官方的SDK可以快速开发一个外部工具出来，通过装饰器的这种编程方式，非常简洁的实现了一个工具函数。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F4a087578d037a965c3777e37b614114c-20250417095401970.png)

就这样，我们无需再去编写web server来暴露这个函数，也无需再编写繁琐的API schema文件去描述这个工具函数，这里面的这些细节，MCP的SDK都给我们封装好了。

## 四：MCP协议技术细节

接下来，我们就来看一下MCP底层到底是怎么工作的。

要探究MCP这套方案的工作原理，这里涉及到两块内容，第一个是MCP Client和MCP Server之间的通信。第二个是MCP Client与AI大模型之间的通信，把这两段链路的交互过程都搞清楚之后，就能揭开MCP神秘的面纱啦！

### 1、MCP Client与MCP Server之间的通信

首先来看看MCP Client与MCP Server之间的通信。咱们来看官方文档怎么说，Anthropic的官方文档这里写的很详细，目前MCP支持两种通信方式。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Ff3c952177e356500ba52da88f9ecadb6-20250417095417360.png)
第一种是通过标准输入输出流来交互。在这种方式下，Server进程被Client进程创建出来作为子进程，然后通过标准输入输出交互。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F1784652d77d2ea08029adc270454bd4d-20250417095428725.png)
客户端给服务端发消息，就把数据写入到服务端进程的STDIN标准输入流中。而服务端响应的内容则通过STDOUT标准输出发出去，通过两条通路完成双工通信。

这种方式适用于两者都在同一台计算机上的场景。

如果Server和Client不在同一台计算机上，那就得使用第二种方式，通过HTTP协议使用SSE的方式来进行通信。

SSE是服务器发送事件的简称，我们使用AI，那种一个字一个字往外蹦的效果，就是通过SSE技术来实现的。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fb60587b11a6120ac0595383d05e02559-20250417095444133.png)
使用SSE模式的时候，服务端这边会添加两个路由，一个用来建立持久的连接，让服务端可以随时返回数据给客户端，另一个就是客户端用来给服务端发送消息的。同样通过两条通路完成双工通信。

以上是两种通信方式，具体通信的协议内容，则是基于JSON-RPC2.0来实现的。

接下来我们做个小的实验来抓包看看，它们之间的通信内容。

这是我写的一个简单的MCP Server：xuanyuan-universe，里面有两个工具函数，一个可以用来获取我写过的文章列表，一个可以获取某个主题方面的视频列表。我的文章和视频可远不止这么点哦，这里只是一个示例。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F7259b1df69e4f8554f0be6d22b33e69c-20250417095539217.png)
为了方便查看它们之间的通信，我这里选择SSE的模式来让Client和Server建立连接。

首先，我通过Python把这个程序给跑起来，在MCP SDK这个壳的包装下，默认就会启动一个Web Server了，默认使用的端口是8000。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F7eb19184e006987cc78010bbb9753137-20250417095556040.png)
接下来，我在VSCode里使用Cline这个插件，添加MCP Server的配置，通过url参数指定连接本地的8000端口的MCP服务。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fec8ca4227fd32d0d1cc905e3d4084bbb-20250417095606638.png)
提前使用抓包软件来开始抓包，我这里使用的是我的知识星球里带大家开发的EasyTshark这款抓包软件。选择网卡记得选择本地回环网卡，因为服务运行在localhost上。

接下来，我来提个问题：**xuanyuan的视频里面，网络安全主题相关的播放最高的是哪个。**

可以看到，AI分析之后发现，需要用到xuanyuan-universe这个MCP服务里面的`get_relate_video_list`工具，并且传递的参数是：**网络安全**。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F333a422998c629cb742c425b4993bf64-20250417095618801.png)
在调用这个工具之后告诉了我最终的答案。

来抓包软件里面看一下，在TCP会话这里筛选一下8000端口的通信，可以看到这里有两个会话，这个HTTP会话代表的就是客户端发送消息给服务端。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F138850c31b4fc1a7b4310b3c6ecab882-20250417095631171.png)
查看一下通信的数据流，可以看到这里Client发送一个JSON过来，里面指定了调用的工具名称和参数。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2Fb6c8738175dac0f96f055dcc9f6c4cd8-20250417095642114.png)
再看一下服务器返回的内容，在另一个会话中。可以看到MCP server返回的正是我的这个函数中的内容。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F4b611b146db26eca6a94b8c94b0e6828-20250417095652017.png)
上面这段过程只是在使用过程中的一些通信，除此之外，双方建立连接初始化的阶段也有一些通信，比如服务端会告诉客户端自己拥有哪些工具函数。这个不是咱们今天的重点，大家感兴趣的话可以在Anthropic的官网学习。

### 2、MCP Client与AI大模型之间的通信

接下来来看一下MCP Client和AI大模型之间的通信。这一段通信使用的是HTTPS，是加了密的，为了看到内容，我使用Fiddler这个抓包软件做了代理。

同样还是提之前的问题，再来抓一下与大模型之间的通信。没想到这一抓让我发现了不得了的事情。 前面讲Function Calling技术的时候提到过，中介程序把外部工具的使用说明书通过JSON的形式上传了上去。

然而我在抓到的Cline插件与AI的通信数据中发现，Cline插件并没有使用Function Calling技术，而是用最开始的那种最笨的办法，把所有外部工具的详细信息直接写在了提示词里面，而且这里面还有很多cline内置的工具，甚至还有详细的使用案例教学，整个加起来长达六万多个字符，一股脑的发给了AI，这实在是太烧token了。这时候耳边响起了那段经典的旁白：高端的技术往往只需要最朴素的实现方式。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F536985c59783930ec6dc8e8768fe56c1-20250417095704994.png)
我发现这里面有一个章节讲的是MCP服务的使用。Cline告诉AI，如果你要调用MCP服务，请使用use_mcp_tool标签告诉我，并且按照下面这样的格式来指定具体调用的工具名称和参数。![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F8bb1505285620d1651603e8e21c2511f-20250417095717285.png)
这段六万多字的内容作为系统提示词发送给AI，至于我发给AI的问题，则在JSON中通过另一项来指定，角色设定为用户user。

然后看看AI返回的内容，同样是用的SSE格式，每蹦一个字就是一条消息，我也把它合并处理了一下，可以看到，AI确实按照Cline上面约定的那样，使用use_mcp_tool标签指定了要调用的外部工具，包括名称、参数都写的清清楚楚。
![image.png](https://cdn.jsdelivr.net/gh/Tipriest/blog-images@main/imgs/my_blog/2025%2F04%2F17%2F0ff0d5d04fc472f679b4485c24e9cd46-20250417095729722.png)
我查阅了Anthropic的官网，并没有发现MCP Client或者MCP Host如何与AI大模型之间进行通信的规定。目前来说，MCP协议只是规定了Client和Server之间的通信。

至于和AI大模型之间的通信，则是更加开放灵活，直接像Cline这样塞到提示词里面也可以，还有的用Function Calling那样也可以。

用第一种方式的好处是理论上来说任何指令遵循能力好的大模型都可以使用MCP技术，而第二种方式则对大模型有要求，必须是支持Function Calling技术的大模型才行。

好了，以上就是今天的全部内容了，如果有学到一点点东西的话，别忘了点赞收藏一下哦。

如果对上面的抓包软件感兴趣，想加入我们一起来开发的，欢迎扫下面二维码了解一下：