# IntelliJ IDEA配置和使用心得

分类: java
日期: 2015-07-30

原文地址: 

http://blog.chinaunix.net/uid-29632145-id-5140117.html

------

****[IntelliJ IDEA配置和使用心得]() *2015-07-30 09:28:43*

分类： Java

最近尝鲜试用了一下IntelliJ，使用下来还是比较爽的，最后我这个很少花钱买软件的人，也在oschina上买了个人版。IDE毕竟是码农干活的家伙，想想也值了。使用的时候有一些心得，记录下来。

### 调整界面为酷酷的黑色

Preferences=>Appearance=>theme=>Darcula

### 检出项目:

VCS=>Checkout From Version Control，maven项目会被自动识别出来。

### 设置快捷键：

Preferences=>keymaps，有很多套方案，当然即使选择Eclipse也还是有很多和Eclipse不同的地方。

### 自动补全：

Mac下默认是clt+space，可以使用keymaps=>Main menu=>Code=>Competion设置。比Eclipse好的地方是Spring、Maven的xml，乃至freemarker模板以及iBatis的sqlmap都支持高亮和自动补全。

### 去除自动补全的大小写敏感：

不知道多少童鞋和我一样被Eclipse惯坏了，使用自动补全完全不注意大小写的，IntelliJ默认区分大小写，很是让人难过。不过在Editor=>Code Completion里把Case sensitive completion设置为None就可以了。

### 自动展开目录

Eclipse有个打开文件就自动展开目录的功能，在IntelliJ里从Project左边栏的齿轮上选择Autoscroll to Source和Autoscroll from Source都勾选上即可。

### 使用Tomcat运行web项目：

需安装插件：Tomcat and TomEE intergration

选择Run=>Edit Configurations，点+，选tomcat server，Deloyment选择对应artifact。详细文章：<http://my.oschina.net/tsl0922/blog/94621>

### 项目间文件复制

IntelliJ里的工作空间是Project，不同Project之间是没有什么关系的。在一个Project里copy&paste，会弹出对话框，让你选择**目标文件夹**。也就是说，并没有跨Project的复制，而是从源Project把文件复制出去。

### 自动编译

IntelliJ默认是不会自动编译项目的，所以在run之前会有个make的过程，习惯自动编译项目的可以在这里打开：Compiler=>make project automatically。因为IntelliJ项目空间不大，所以开启之后也不会像Eclipse一样出现build workspace很久的情况。

### Debug

debug最好不要使用method breakpoint，会导致启动异常缓慢，博主之前就不小心启动了method breakpoint，然后进入调试要花掉几分钟的时间。IntelliJ断点可以设置Condition，其实Eclipse也可以，只不过没有这么明显，同时IntelliJ可以在Condition进行代码提示。

### 远程Debug

Run=>Edit Configurations，选择Add=>remote，然后你懂的。

### File Template

与Eclipse的Code Template类似，只不过IntelliJ内置变量全部为大写，例如：${NAME}。可以使用#parse(“File Header.java”)这种格式来导入另一个文件，跟jsp include的作用一样，实现复用的一种方式吧。没有导入/导出，有点不太方便。

### Live Template

用惯了Eclipse快捷键的人可能会不习惯，sysout、foreach等快捷方式找不到了，main方法也无法自动补全了，其实这个在IntelliJ中有一个异常强大的模块Live Template来实现。

例如，在class中尝试psvm+tab，则会发现main方法产生了；输入iter+tab，则生成了foreach语句。 
live template还有一个surround的用法，选中某个变量，键入ctl+alt+j两次，则会出现自动补全的菜单。

此外，还可以自定义Live Template。Code Snippet技术应用也挺普遍的，IntelliJ的Live Template优点是内置了一些智能的变量和函数，可以做到一些语义级别的分析和运用。

------

### 几句牢骚

IDE的圣战从来没有停止过，Eclipse还是IntelliJ好？首先，IntelliJ某些更加体贴的功能，让我感叹一分钱一份货。比如选中括号的后面部分，即使滑动到了下一屏，也会将括号开始的部分浮动显示出来。更重要的，我想引用一下《大教堂与集市》中的比喻，Eclipse好比集市，有开放的环境，本身功能并不求全责备，通过插件来提供相应的功能(最基本的maven、VCS都需要第三方插件提供)。相对的，IntelliJ就像大教堂，内部整合了大多数功能，基本上是一体化的使用设计。

庞大的插件机制和依赖也使得Eclipse出现一些混乱和不稳定。插件依赖/兼容性/稳定性都存在一些问题，而且Eclipse一味向可扩展设计的方式也使得使用起来会更复杂。例如，Eclipse的快捷键设置功能，全部是一字平铺，有一个"when"的选项，我理解这是使用快捷键的一个场景。问题是，所有插件都可以向它注册一个场景，当我真要选择when的时候，发现列表有两页之长，我哪知道选哪个？相反，IntelliJ的keymap采用了分类的方式，一级分类就是使用场景，然后再进入相应项设置快捷键，比Eclipse方便的多。再比如，有人说Eclipse慢，其实很可能并不是内核慢，而是一些插件(例如m2e)运行太慢导致的。而IntelliJ基本上都是很迅速的，很少出现失去响应的情况。

这里我想引用一篇文章《有人负责，才有质量：写给在集市中迷失的一代》<http://www.oschina.net/news/32190/a-generation-list-in-the-bazaar>。Eclipse庞大的体系注定了插件管理的松散性，所以使用者就要忍受一些不稳定和不方便的因素。相比IntelliJ，因为是公司开发，大部分插件都在其管理范围之内，所以整体质量更好控制。

说到这里好像就认定IntelliJ好了？其实也未必，因为《大教堂与集市》也提到，开源带来的生产力是教堂式开发远不能比的，所以IntelliJ要收费，而Eclipse可以免费。Eclipse庞大的插件群，功能的全面性，个人觉得也是IntelliJ比不了的。

最后说一句，说到学习成本，其实IntelliJ是要比Eclipse低的，至少省去了很多配置插件、理清依赖、处理问题的功夫，同时设置也比Eclipse要简单不少。没有说越高级的IDE越复杂的说法，只是Eclipse作为最常用的Java IDE，让大家先入为主了罢了。