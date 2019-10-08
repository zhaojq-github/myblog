[TOC]



# css 书写规范 css规范-网易NEC

在书写css样式的时候总是无意中就写乱了，无论是命名或者是样式的书写顺序，这里做一个总结，提醒自己在书写css的时候时刻注意，大家可以参考哈。

## 1. 样式属性顺序

单个样式规则下的属性在书写时，应按功能进行分组，组之间需要有一个空行。 同时要以Positioning Model > Box Model > Typographic > Visual 的顺序书写，提高代码的可读性。

1. Positioning Model 布局方式、位置，相关属性包括：position, top, z-index, display, float等
2. Box Model 盒模型，相关属性包括：width, height, padding, margin，border,overflow
3. Typographic 文本排版，相关属性包括：font, line-height, text-align
4. Visual 视觉外观，相关属性包括：color, background, list-style, transform, animation

## 2. CSS选择器命名规则

- 分类式命名法(在前端组件化下尤为重要)
  1. 布局（grid）（.g-）：将页面分割为几个大块，通常有头部、主体、主栏、侧栏、尾部等！
  2. 模块（module）（.m-）：通常是一个语义化的可以重复使用的较大的整体！比如导航、登录、注册等
  3. 元件（unit）（.u-）：通常是一个不可再分的较为小巧的个体，通常被重复用于各种模块中！比如按钮、输 入框、loading等！
  4. 功能（function）（.f-）：为方便一些常用样式的使用，我们将这些使用率较高的样式剥离出来，按需使用，通常这些选择器具有固定样式表现，比如清除浮动等！不可滥用！
  5. 状态（.z-）：为状态类样式加入前缀，统一标识，方便识别，她只能组合使用或作为后代出现（.u-ipt.z-dis{}，.m-list li.z-sel{}）
  6. javascript(.j-)：.j-将被专用于JS获取节点，请勿使用.j-定义样式
- 不要使用 " _ " 下划线来命名css 能良好的区分javascript变量名 输入的时候少按一个shift键 浏览器兼容性问题（比如使用_tips的选择器命名，在IE6是无效的）
- id采用驼峰式命名(不要乱用id)
- scss中的变量、函数、混合、placeholder采用驼峰式命名
- 相同语义的不同类命名方法： 直接加数字或字母区分即可（如：.m-list、.m-list2、.m-list3等，都是列表模块，但是是完全不一样的模块）。其他举例：.f-fw0、.f-fw1、.s-fc0、.s-fc1、.m-logo2、.m-logo3、u-btn、u-btn2等等。
- 命名方式(BEM)：类-体（例：g-head）、类-体-修饰符（例：u-btn-active）
- 后代选择器：体-修饰符即可（例：.m-page .cut{}）注：后代选择器不要在页面布局中使用，因为污染的可能性较大；

## 3. 最佳写法

```
    /* 这是某个模块 */
    .m-nav{}/* 模块容器 */
    .m-nav li,.m-nav a{}/* 先共性  优化组合 */
    .m-nav li{}/* 后个性  语义化标签选择器 */
    .m-nav a{}/* 后个性中的共性 按结构顺序 */
    .m-nav a.a1{}/* 后个性中的个性 */
    .m-nav a.a2{}/* 后个性中的个性 */
    .m-nav .z-crt a{}/* 交互状态变化 */
    .m-nav .z-crt a.a1{}
    .m-nav .z-crt a.a2{}
    .m-nav .btn{}/* 典型后代选择器 */
    .m-nav .btn-1{}/* 典型后代选择器扩展 */
    .m-nav .btn-dis{}/* 典型后代选择器扩展（状态） */
    .m-nav .btn.z-dis{}/* 作用同上，请二选一（如果可以不兼容IE6时使用） */
    .m-nav .m-sch{}/* 控制内部其他模块位置 */
    .m-nav .u-sel{}/* 控制内部其他元件位置 */
    .m-nav-1{}/* 模块扩展 */
    .m-nav-1 li{}
    .m-nav-dis{}/* 模块扩展（状态） */
    .m-nav.z-dis{}/* 作用同上，请二选一（如果可以不兼容IE6时使用） */
```

## 4. 统一语义理解和命名

- 布局(.g-)

| 语义       | 命名     | 简写     |
| ---------- | -------- | -------- |
| 文档       | doc      | doc      |
| 头部       | head     | hd       |
| 主体       | body     | bd       |
| 尾部       | foot     | ft       |
| 主栏       | main     | mn       |
| 主栏子容器 | mainc    | mnc      |
| 侧栏       | side     | sd       |
| 侧栏子容器 | sidec    | sdc      |
| 盒容器     | wrap/box | wrap/box |

- 模块（.m-）、元件（.u-）

| 语义   | 命名         | 简写  |
| ------ | ------------ | ----- |
| 导航   | nav          | nav   |
| 子导航 | subnav       | snav  |
| 面包屑 | crumb        | crm   |
| 菜单   | menu         | menu  |
| 选项卡 | tab          | tab   |
| 标题区 | head/title   | hd/tt |
| 内容区 | body/content | bd/ct |
| 列表   | list         | lst   |
| 表格   | table        | tb    |
| 表单   | form         | fm    |
| 热点   | hot          | hot   |
| 排行   | top          | top   |
| 登录   | login        | log   |
| 标志   | logo         | logo  |
| 广告   | advertise    | ad    |
| 搜索   | search       | sch   |
| 幻灯   | slide        | sld   |
| 提示   | tips         | tips  |
| 帮助   | help         | help  |
| 新闻   | news         | news  |
| 下载   | download     | dld   |
| 注册   | regist       | reg   |
| 投票   | vote         | vote  |
| 版权   | vcopyright   | cprt  |
| 结果   | result       | rst   |
| 标题   | title        | tt    |
| 按钮   | button       | btn   |
| 输入   | input        | ipt   |

- 功能（.f-）

| 语义     | 命名                | 简写 |
| -------- | ------------------- | ---- |
| 清除浮动 | clearboth           | cb   |
| 左浮动   | floatleft           | fl   |
| 内联     | inlineblock         | ib   |
| 文本居中 | textaligncenter     | tac  |
| 垂直居中 | verticalalignmiddle | vam  |
| 溢出隐藏 | overflowhidden      | oh   |
| 完全消失 | displaynone         | dn   |
| 字体大小 | fontsize            | fz   |
| 字体粗细 | fontweight          | fs   |

- 皮肤（.s-）

| 语义     | 命名            | 简写 |
| -------- | --------------- | ---- |
| 字体颜色 | fontcolor       | fc   |
| 背景颜色 | backgroundcolor | bgc  |
| 边框颜色 | bordercolor     | bdc  |

- 状态(.z-)

| 语义   | 命名            | 简写 |
| ------ | --------------- | ---- |
| 选中   | selected        | sel  |
| 当前   | current         | crt  |
| 显示   | show            | show |
| 隐藏   | hide            | hide |
| 打开   | open            | open |
| 关闭   | close	vclose |      |
| 出错   | error           | err  |
| 不可用 | disabled        | dis  |

## 5. 注意事项

1. 一律小写，中划线
2. 尽量不用缩写
3. 不要随便使用id
4. 去掉小数点前面的0： 0.9rem => .9rem
5. 使用简写：margin： 0 1rem 3rem 本文大部分内容参考自网易前端规范：[nec.netease.com/standard/cs…](https://link.juejin.im/?target=http%3A%2F%2Fnec.netease.com%2Fstandard%2Fcss-sort.html)



https://juejin.im/post/5aeff1de6fb9a07a9a10c57a