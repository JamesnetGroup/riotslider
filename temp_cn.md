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


## 1.WPF Tutorial Series

> 目前为止，我们在YouTube和BiliBili平台上共发布了四个中英文（YouTube上配有韩文字幕）教程系列。我们希望通过视频的形式将复杂的源代码更加整体化具像化地详细讲解，以便于能够增强大家对WPF的理解。

- [x] Theme Switch: [Youtube](https://youtu.be/rGox76Bm6VY?si=jTNutyRFctAfTTeS), [BiliBili](https://www.bilibili.com/video/BV1ez4y1N7B8/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5372268/Use-WPF-to-Create-a-Day-and-Night-Theme-Switch-But), [GitHub](https://github.com/vickyqu115/themeswitch)

- [x] Riot PlayButton: [Youtube](https://youtu.be/xgUqDavCJGg?si=Ok8qZjNQUKUEbgfT), [BiliBili](https://www.bilibili.com/video/BV1Tu4y1j7Ei/?spm_id_from=333.788.recommend_more_video.-1&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5373396/Creating-a-League-of-Legends-Inspired-Play-Button), [GitHub](https://github.com/vickyqu115/riotplaybutton)

- [x] Magic Navigation Bar: [Youtube](https://youtu.be/dxuLWlukthg?si=CpvQ-TJ2tQu9sY3F), [BiliBili](https://www.bilibili.com/video/BV1Ui4y1a717/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5375482/Customizing-ListBox-for-a-Smooth-Animated-Navigati), [GitHub](https://github.com/vickyqu115/navigationbar)

- [x] Riot Slider: [Youtube](https://www.youtube.com/watch?v=jyv2fP9TUtY) [BiliBili](https://www.bilibili.com/video/BV1uy421a7yM/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5377407/Analyzing-and-Customizing-the-Detailed-Mechanisms), [GitHub](https://github.com/vickyqu115/riotslider)

## 2.Specifications：
如果你不熟悉WPF平台，可能会会不太适应这样的开发环境。该项目基于.NET 8.0，但由于使用的是WPF，框架目标仅限于Windows，所以只能在Windows环境下开发和运行。IDE可以选择Visual Studio或JetBrains公司提供的Rider。使用Visual Studio时，版本必须要在VS2022以上才可运行.NET 8.0。因此，如果你使用的是VS2022以下版本，需要将.NET版本迁移到与你的IDE版本相匹配。

- [x] OS: Microsoft Windows 11
- [x] IDE: Microsoft Visual Studio 2022
- [x] Version: C# / NET 8.0 / WPF / windows target only
- [x] NuGet: Jamesnet.Wpf

我们推荐使用最新版本的Windows操作系统。不过，如果考虑到将平台扩展到Avalonia UI、Uno Platform、MAUI等，也完全可以考虑将MacOS作为一个次要设备。我们也同时在使用Thinkpad和Macbook。但是请注意，在MacOS或基于Linux的系统上不能使用Visual Studio，因此Rider是唯一的选择。~~vscode~~

## 3. 创建应用程序项目

首先，需要创建一个WPF应用程序项目。

- [x] 项目类型：WPF应用程序

- [x] 项目名称：DemoApp

- [x] 项目版本：.NET 8.0

## 4.分析Slider的主要功能

与Button等简单控件不同，WPF的Slider控件存在许多不同的属性。特别是，这些属性在控件的功能性方面扮演着重要的角色，因此需要仔细研究，其中一些特别行为的主要属性如下：

**Orientation:** 

WPF提供的控件通常具有通用性质。Slider控件中的Orientation属性就是一个例子。通过这个属性，可以将方向指定为水平或垂直方向。

Orientation属性也可以在StackPanel控件中找到。StackPanel的默认Orientation值是Vertical，但Slider的默认Orientation值是Horizontal。因此，通常情况下，Slider是被默认为水平状态，所以大多数人可能会忽略这个Orientation的功能。

下面我们通过一个简化的Slider部分代码来帮助理解Orientation功能。

```xaml
<Style TargetType="{x:Type Slider}">
    <Setter Property="Template" Value="{StaticResource SliderHorizontal}"/>
    <Style.Triggers>
    	<Trigger Property="Orientation" Value="Vertical">
    	    <Setter Property="Template" Value="{StaticResource SliderVertical}"/>
    	</Trigger>
    </Style.Triggers>
</Style>
```

根据Orientation属性，可以看到触发器（ControlTemplate）中模板的切换。因此，稍微研究一下这个控件的详细配置，就可以很容易地理解Orientation这个属性充当了一个相当重要的角色。

> 这是一个比较有意思的部分。在看到原始代码之前，你能想象到通过Orientation来切换模板的应用吗？其实开源项目就是这样给我们带来各种灵感。并且，通过这段源代码，我们也可以检查到"Style.Trigger"正是切换模板的最佳时机。

在本教程视频中，我们只计划实现水平（Horizontal）方向，因此不会实现通过Orientation进行分支切换的操作。但是我们鼓励大家进行垂直（Vertical）方向的尝试，并通过Fork提交Pull Request请求。这是一个小任务哦～

那么下面我们就来看一下水平/垂直属性各自应用的样子吧！
- [x] Orientation: **Horizontal**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300" style="float:left"/>

> 下面也会涉及到选择范围（SelectionRange，蓝色）区域。
