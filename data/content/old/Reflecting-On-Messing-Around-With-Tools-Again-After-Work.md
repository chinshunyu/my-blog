---
title: 工作后反思折腾工具
publishDate: 2023-11-10
description: 没有设计就是最好的设计，好的工具是理所当然的存在。
---
## 前言

自从工作后逐渐明白生产环境的重要性，作为券商风控居然办公桌上摆放着一台内网机、一个外网机以及一台笔记本电脑，每台设备都装了足以让系统卡死甚至蓝屏的监控“安全软件”（既然如此担心安全却没有系统镜像备份和全盘备份，风控专员居然无法控制自己工作的操作风险），尽管是2018年后购买的新设备在现实中也是无能为力，逐渐发现工作需要的是不断的严谨和重复，以及不定期的微调，凭借不符时流的Excel VBA完成报表的制作，本质上是稳定和一定的可拓展性。

首先千万不要在生产环境折腾测试项目，其技术背景是“失控的微软”，特别是涉及到自动宏、脚本、加载项，我们不知道打开一张Excel表究竟在后台可能发生了什么bug或意外，给我上了一堂印象深刻的课——**代码能跑就不要动，千万不要点击历史文件**，因为Excel没有严格的版本控制，如果操作失误后保存，下一次打开就找不到过去的版本，然而又没有完整的回退路径，对于要留底稿和审计需求而言，又是场灾难片。

## 折腾工具

回顾折腾工具的经历，我曾执迷于新事物然而又学不到真正的技术，还是以前学习基础编程和基础算法来得扎实，让我回心转意的是`next-mdx-remote`这一Next.js生态中重要的mdx组件，其`readme.md`的提示"How Can I Build A Blog With This?"：

>Data has shown that 99% of use cases for all developer tooling are building unnecessarily complex personal blogs. Just kidding. But seriously, if you are trying to build a blog for personal or small business use, consider just using normal html and css. You definitely do not need to be using a heavy full-stack javascript framework to make a simple blog. You'll thank yourself later when you return to make an update in a couple years and there haven't been 10 breaking releases to all of your dependencies.

其实只使用html+css编译这个博客并不是一件难事，唯一的缺陷可能是缺少开发体验，我非常喜欢tailwind的原子化css，可能将一大堆css塞进className会很奇怪，但是长期下来滚依赖项大版本很难不发生错误，不看release notes完全不知道发生什么breaking changes，所以我选择锁版本号，`npm outdated`的wanted和latest永远选择wanted，这是wanted是满足语义化版本控制下软件包的最大版本，但我认为折腾博客和目前的工作有一定的相似，那就是学会足够的技术去维护一套模板并不断修改细节完善。

反观过度的折腾陷入不仅仅是生产力色情，还有副现象的认知偏差，因为大部分人看到有吸引力的事物就想着去模仿，然而**模仿学来的可能是别人成功后的结果**。

## 重来

音乐就在你的指尖流淌  
  
吉他大师说：“音乐就在你的指尖流淌。”就算你买了和艾迪·范·海伦（ Eddie Van Halen ）一样的吉他、效果踏板、扩音器，但是当你用这套装备来演奏时，弹出来的依然是你自己的风格。  
  
同理，给艾迪配一套从当铺倒腾出来的劣质装备，人家一出手，你还是能听出是艾迪·范·海伦的水平。好的装备的确能带来一些帮助，但事实是，你的演奏水平是由你自己的手指决定的。  
  
人们总忍不住要执着于工具，而不关注要用这些工具去做的事情。你见过这类人：能玩转一大堆震撼的艺术字体和漂亮的 Photoshop 滤镜效果的设计师，却不知道该表达什么。业余摄影爱好者总为使用胶片相机还是数码相机而争论不休，却没人关注拍出绝妙照片的决定因素是什么。  
  
很多业余高尔夫球手执着于加入昂贵的俱乐部，但是真正重要的是如何挥杆，而不是加入哪个俱乐部。就算让老虎伍兹加入廉价的俱乐部，他也照样能摆平你。  
  
人们把装备当作取胜的法宝，却不愿意花时间去练习，于是一直泡在专业器材店里。他们想要寻找捷径，然而，最好的工具不是用在普通领域的。而且你在起步阶段肯定用不上它。  
  
在商业领域，太多人纠结于工具的好坏、软件技巧、规模大小、舒适的办公环境、豪华的家具以及其他浮华的东西，而不去关注真正的要点。真正的要点就是怎样赢得客户、如何赢利。  
  
我们还可以看到一些人想要通过博客、播客或拍摄纪录片来宣传他们的业务，却受困于不知选择什么工具。真正要紧的是宣传的内容。你可以花大手笔购买超级棒的设备，但是如果没有什么内容可表达……那么，你还真没什么可说的。  
  
就用你现在手头有的或者能负担得起的，然后开始做吧。工具不重要，就用现有的工具也可以做得一样棒，音乐就在你的指尖流淌。  
  
摘自《重来：更为简单有效的商业思维》

## 系统

许多工具要求用户进行系统性学习，例如Lightroom中的直方图、曝光和色调曲线等功能。然而，这些功能并非LR软件独有，事实上，在胶片和数码相机的年代，相机通常已经配备了直方图显示的功能。重要的是判断曝光是否准确，而并非仅仅依赖显示屏的辅助，概率密度分布函数揭示了不同明度区域内的像素密度，还包括RGB、明度等直方图，这是对图像进行定量分析。然而在过去，眼见为实是主要的判断依据，缺乏可量化辅助工具，并不乏出色的黑白摄影大师。

**工具本身是提供科学分析的方法论，而非直接的创作**。

回到写作本身，除了媒介即认识论和路径依赖性，还有云服务的困境。我是非常反对将自己的笔记全部在云服务上，如gitbook如今要付费才能PDF导出，国产的笔记服务只会看到翻车的新闻，各种开会员勒索，我认为，作为需唾手可得又要结构化数据，使用本地储存+编辑器就是简单的方法，很多人会抱怨没有云同步，难道复制粘贴一份很困难吗？将一件简单的事物复杂化，不如想下自己同步的笔记才几个MB，更有甚者存入数据库，除了展现自己的技术，我想不到对于个人需求有何意义。其实大部分人的博客和NAS也是，**将简单的事物工程化**。

目前写作不需要有纸墨笔砚，只需要“大脑在线”即可，然而折腾工具反而会使得人们掉入工具效率的陷阱，很显然，折腾工具和在某领域获取成功是两回事，但**造轮子销售是个不错的生意**，只要人们陷入某个系统化的技术栈，也许制作报表无法逃离Excel，某些专业软件难以逃离Mac的软件生态，但这就是订阅制商业模式的运作方式，这些选择除了使得“格调”有所不同，不会有任何附加价值，最终是教科书式的市场垄断，消费者又需要在漫长的经济学历史中找到答案。

## 总结

**没有设计就是最好的设计，好的工具是理所当然的存在**。

在设计方面，刻意留空、内容为中心；工具效率方面，好的工具不应被视为“工具”，而是生产劳动过程自然而成的，但刻意折腾必然是低效的，远离了生产环境，最终解决方案是**弄清楚自身的需求，打理自己的生活**。