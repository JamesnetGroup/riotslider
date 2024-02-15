![WechatIMG12](https://github.com/vickyqu115/riotslider/assets/101777355/b686d831-2de6-410b-949d-52b6b9993873)
## Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control

在WPF中，像Button和Toggle Button等这一类的基本控件的Template在结构和逻辑上都设计得非常简单和直观，（无需单独的代码处理）仅通过XAML就可以轻松实现。
而像TextBox、ComboBox、Slider这一类更加结构化的控件（不仅仅是XAML）则就必须通过C#代码进行复杂处理之后才可以实现。

因此，当我们处理复合控件时，如果能够很好地理解WPF控件的详细结构，并能够应用这些知识，就可以更加灵活地设计和开发CustomControl了。
此外，能够熟练掌握控件的基本组成部分，自然也就可以从新的角度来弥补MVVM开发模式中的不足，从而实现更加高质量的WPF应用程序。

因此这个项目中，我们将通过查看WPF提供的Slider控件，来深入了解WPF控件的内部机制设计，并对其进行详细的分析。
当然，想要一一查看和了解WPF所有控件的内部详情是比较困难的，当下我们也没有这样的必要。
因为WPF的所有源代码都通过GitHub仓库以开源形式公开和管理。因此我们可以根据需要随时访问GitHub仓库来查找和分析需要了解的控件。
不仅如此，未来我们还计划分析和解剖比Slider控件更加复杂和多样的控件，所以希望大家能够多多支持、关注和鼓励我们在在GitHub、BiliBili等其它平台上提供的教程内容。

![20240201163433798](https://github.com/vickyqu115/riotslider/assets/101777355/f30773e5-9ad3-4b19-b104-5c026af2577e)

## 目录

1.WPF系列教程

2.Specification

3.创建应用程序

4.分析Slider主要功能

5.原始样式提取过程

6.分析内部源代码

7.确认Code behind（GitHub开源）

8.跨平台中的 OnApplyTemplate

9.结束Slider分析

10.创建Riot风格的Slider控件（CustomControl）

11.创建项目与准备

12.TextBlock（Hi Slider）

13.添加引用并测试

14.设置Riot Slider大小

15.PART_Track

16.添加滑动条

17.调整滑动条和Track之间的误差间隙

18.PART_SelectionRange

19.添加Riot风格的设计元素

20.实现Riot风格的Thumb

21.声明Thumb资源

22.RiotSlider Template完成（总结）

23.结束语