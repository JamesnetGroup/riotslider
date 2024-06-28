## 分析和自定义 WPF Slider 控件的详细机制

![WechatIMG12](https://jamesnetdev.blob.core.windows.net/articleimages/eed43f5b-4f62-465c-acf0-299a41b57337.gif)

## 分析和自定义 WPF Slider 控件的详细机制

在 WPF 中，像Button和ToggleButton这样的基本控件结构和逻辑简单，可以完全用 XAML 实现而无需后台代码。相比之下，更复杂的控件如TextBox、ComboBox和Slider则需要复杂的 C# 代码配合 XAML 才能实现它的功能。

理解和应用 WPF 控件的复杂配置，可以让自定义控件的设计和开发更为优雅和灵活。熟练掌握这些基本组件，我们就可以解决 MVVM 开发模式中的一些问题，从而构建出更加高质量的 WPF 应用程序。

这次我们对 WPF Slider 控件的探索就是为了更深入理解这个控件构建的方式和它的内部机制。尽管代码量非常庞大，要深入每个 WPF 控件的内部几乎是不可能的，但也无需过于担心。

WPF 的所有源代码都是开放的，并在 GitHub 上进行管理。这就意味着我们可以随时根据需要找到并分析具体的控件。

除了 Slider 控件之外，未来我们还计划剖析和分析更多更复杂和多样化的控件。所以如果你对这一部分的内容有兴趣，不要忘了关注我们Bilibili或Youtube频道，具体的源代码我们也会分享在GitHub上。

## 内容目录

1. WPF 系列教程
2. 规范
3. 创建应用项目
4. 分析 Slider 的主要功能
5. 提取原始样式过程
6. 提取源码分析
7. 检查后台代码（GitHub 开源）
8. 跨平台的 OnApplyTemplate
9. 总结 Slider 分析
10. 创建 Riot 风格 Slider（自定义控件）
11. 项目创建和开始准备
12. TextBlock（Hi Slider）
13. 添加引用和测试执行
14. 设置 Riot Slider 的尺寸
15. PART_Track
16. 添加滑动条
17. 调整滑动条和轨道之间的间隙
18. PART_SelectionRange
19. 添加 Riot 风格设计元素
20. 实现 Riot 风格的滑块
21. 声明滑块资源
22. 完成 RiotSlider 模板（最后润色）
23. 最终评论

## WPF 系列教程

> 目前为止，我们已经在 YouTube 和 BiliBili 上发布了四个教程系列。并提供了英文和中文配音，及韩文字幕。我们希望通过这些视频，结合精细的源码和详细讲解，能提升大家对 WPF 的理解。

- [x] 主题切换：[YouTube](https://youtu.be/rGox76Bm6VY?si=jTNutyRFctAfTTeS)，[BiliBili](https://www.bilibili.com/video/BV1ez4y1N7B8/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2)，[CodeProject](https://www.codeproject.com/Articles/5372268/Use-WPF-to-Create-a-Day-and-Night-Theme-Switch-But)，[GitHub](https://github.com/vickyqu115/themeswitch)

- [x] Riot 播放按钮：[YouTube](https://youtu.be/xgUqDavCJGg?si=Ok8qZjNQUKUEbgfT)，[BiliBili](https://www.bilibili.com/video/BV1Tu4y1j7Ei/?spm_id_from=333.788.recommend_more_video.-1&vd_source=f62a1783ac9166a4d798823ad1861fd2)，[CodeProject](https://www.codeproject.com/Articles/5373396/Creating-a-League-of-Legends-Inspired-Play-Button)，[GitHub](https://github.com/vickyqu115/riotplaybutton)

- [x] 魔法导航栏：[YouTube](https://youtu.be/dxuLWlukthg?si=CpvQ-TJ2tQu9sY3F)，[BiliBili](https://www.bilibili.com/video/BV1Ui4y1a717/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2)，[CodeProject](https://www.codeproject.com/Articles/5375482/Customizing-ListBox-for-a-Smooth-Animated-Navigati)，[GitHub](https://github.com/vickyqu115/navigationbar)

- [x] Riot 滑块：[YouTube](https://www.youtube.com/watch?v=jyv2fP9TUtY)，[BiliBili](https://www.bilibili.com/video/BV1uy421a7yM/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2)，[CodeProject](https://www.codeproject.com/Articles/5377407/Analyzing-and-Customizing-the-Detailed-Mechanisms)，[GitHub](https://github.com/vickyqu115/riotslider)

## 规范

该项目基于 .NET Core，但由于使用了 WPF，因此仅限于 Windows 平台。
该项目可以通过 VS2022 运行，运行条件：.NET 8.0 或者，也可以在JetBrains 的 Rider上运行此项目。

- [x] 操作系统：Microsoft Windows 11
- [x] IDE：Microsoft Visual Studio 2022
- [x] 版本：C# / .NET 8.0 / WPF / 仅限 Windows
- [x] NuGet：Jamesnet.Wpf

建议使用最新版本的 Windows 作为操作系统。当然，如果考虑将平台扩展到 Avalonia UI、Uno Platform、MAUI 等，也可以将 MacOS 作为辅助设备。我们的编程环境中也同时使用 Thinkpad 和 MacBooks。需要注意的是，Visual Studio 在 MacOS 或基于 Linux 的系统上不可用，因此 Rider 是唯一的替代品。~~vscode~~

## 3. 创建应用项目

首先，我们需要创建一个 WPF Application项目。

- [x] 项目类型：WPF  Application
- [x] 项目名称：DemoApp
- [x] 项目版本：.NET 8.0

## 4. Slider 主要功能分析

与 Button 这样的简单控件不同，WPF Slider 控件具有非常多的属性。特别是这些属性在控件功能中起着重要作用，因此值得我们仔细研究。其中一些特殊的主要属性如下：

**Orientation:**

WPF 提供的控件通常具有通用性。Slider 控件也不例外，Orientation 属性就是一个例子。通过这个属性，可以指定水平或垂直方向。

Orientation 属性也可以在 StackPanel 控件中找到。StackPanel 的 Orientation 属性默认值为 Vertical，但 Slider 的 Orientation 属性默认值为 Horizontal。因此，通常情况下，Slider 是以 Horizontal 形式出现，可能很多人不知道还有 Orientation 功能。

为了帮助大家更好地理解 Orientation 属性，我们来看一个简化的 Slider 例子。

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

这里我们可以看到，根据 Orientation 属性，触发器会切换（ControlTemplate）模板。
因此，通过查看这个控件的详细结构，就可以很容易理解 Orientation 属性的重要性。

> 这是一个有趣的部分。在看到原始代码之前，你能想象或应用通过 Orientation 切换模板的概念吗？开源项目确实能以这样的方式激发开发者的灵感。另外，通过这段代码，我们可以确认，切换模板的最佳时机是在“Style.Trigger”中。

在本次教程视频中，我们只会实现 Horizontal 方向的功能，因此不会通过 Orientation 进行分支切换。不过，也建议大家可以尝试实现 Vertical 方向，并通过 Fork 向我们提交 Pull Request，这是一个小任务哦。

那么接下来，让我们看看分别应用 Horizontal 和 Vertical 属性的效果吧：

- [x] Orientation: **Horizontal**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300" style="float:left"/>

> 在下面即将讨论的 SelectionRange（蓝色）区域也可以看到。

- [x] Orientation: **Vertical**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/bbcbeaa9-1763-4435-b92b-be2a71a5ee73" width="300" style="float:left"/>

> 类似于这种切换（ControlTemplate）模板的控件还有很多，比如 ScrollViewer 等。

##### **Minimum, Maximum 和 Value:** 

这些属性分别表示最小值/最大值和当前值，他们都是 double 类型的属性。内部会根据这些值自动计算控件的大小和比例，以及 Range 和 Value 值的位置。

由于这些属性都是 DependencyProperty，因此可以通过绑定实现动态交互。例如，在 MVVM 结构中，可以利用这三个值根据特定场景动态更改范围或实现各种有趣的应用。

##### SelectionStart, SelectionEnd 和 IsSelectionRangeEnabled:

这两个属性（SelectionStart/SelectionEnd）用于设置特定区域。实际上，这个区域并不包含特别的功能，只是为了指定某个区间并在视觉上突出显示。IsSelectionRangeEnabled 是用于表示该区域是否启用的属性，根据是否启用，通过触发器切换区域的 Visibility 属性值（Visible/Collapsed）。

综上所述，这些功能仅用于简单的区域显示，因此是否需要这些功能可能会让人困惑。但由于它们在不同的设计和领域中具有通用性，因此可以理解并预见它的必要性。~~尊重20年前的风格偏好~~

实际上，如果将这些与 Value 值结合应用，可以产生非常有趣的效果，如下所示：

```xaml
<Slider Orientation="Horizontal"
        Minimum="0"
        Maximum="100"
        Value="30"
        SelectionStart="0"
        SelectionEnd="{Binding Value, RelativeSource={RelativeSource Self}"
        IsSelectionRangeEnabled="True"/>
```

令人惊讶的是，Value 值通过 SelectionEnd 绑定，每次值发生变化时，Selection (Range) 范围都会动态变化。不知道WPF 的开发团队是不是有意为之呢？总之非常棒，实现方式也非常简洁。

> 在后面将要实现的 Riot 风格 Slider (CustomControl) 中，这个部分起到非常重要的作用，请大家注意。

## 5. 提取原始样式的过程

如上述内容所示，WPF 通过 GitHub 仓库以开源方式进行管理，因此我们可以查看所有控件的源代码。但是由于仓库中包含了解决方案以及所有项目和文件，所以仅提取特定控件部分的内容就非常困难了。

幸运的是，Visual Studio 提供了提取特定控件默认样式（Template）的功能，并以 GUI 形式呈现。因此，我们无需在开源代码中一步步寻找也可以轻松提取相应的代码。

> 类似于 Blazor 中的 Identity Scaffolding（虽然性质稍有不同，但为了帮助理解，可以这样类比）。

除此之外，通过 Visual Studio 提取原始样式后，实际会以可修改的资源形式连接，因此就可以马上自定义设计和功能。因此，不仅是 Slider，所有控件的原始样式和模板都可以提取，这在 WPF 的研究和学习的过程中具有很高的应用价值。

> Infragistics、Syncfusion、ArticPro 等商用组件中并不一定提供这种提取的功能。每家公司公开的范围和政策都不同，在大多数情况下，更倾向于通过 DataTemplate 而非 ControlTemplate 来模块化并引导自定义。因此，如果大家有兴趣的话也可以研究一下正在用的组件是什么的方式。

##### 提取方法和步骤：Visual Studio

- [x] 提取默认控件（Slider）样式（Edit a Copy...）
- [x] 提取到当前文件（This document）
- [x] 提取到 App.xaml 文件（Application）
- [x] 创建新的 ResourceDictionary 文件并提取（Resource Dictionary）

不过，提取步骤只能在 Partial 形式的 UserControl 界面的设计区域进行，可以选择控件并右键点击来进行操作。在这个过程中，需要选择“指定样式名称/指定提取样式的复制位置”选项。

> 可以试着在 VScode 或 Rider 中也查找一下，看看是否提也供类似功能？

让我们来逐步查看这个过程。

- [x] 样式提取命令：Slider > 右键点击 > Edit Template > Edit a Copy...

<img width="365" alt="image" src="https://github.com/vickyqu115/riotslider/assets/52397976/0d7f2e38-f616-4260-a256-f3e4eb5c9f09" style="float:left">

> 如果没有提供可提取的样式，那么这个选项将不会被激活。

- [x] 样式提取选项窗口：Create ControlTemplate Resource（Window）

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/d895d1fd-f709-4909-a968-3cd4692550ac" style="float: left; width: 500px"/>

> 选择 Name（Key）和 Define in 选项，

通常，指定名称是测试或管理方面的正确选择。如果不指定名称而选择“Apply to all”项，生成的样式将根据定义的提取位置全局应用。所以需要充分理解这一点并谨慎进行提取。

在视频中，设置了名称，并将定义位置指定为 Application。因此，（如果文件存在）提取的资源将包含在 App.xaml 文件的 Resources 区域中。

个人建议进行这种提取操作时，尽可能在新项目中以测试性质进行。实际上在实时项目中进行这个操作可能会导致一些小错误和问题，因此从避免副作用的角度来看，这是个不错的选择。

## 6. 提取的源代码分析

正如教程视频所示，Slider 控件的样式我们已经成功提取。我们来查看一下 App.xaml 文件中的相关资源，并逐一检查一下重要的注意事项。

##### 确认 Orientation 分支：

在前面解释 Orientation 属性时，我们简单地提到了触发器和切换机制，现在我们来实际查看一下实现的源代码。

以下样式是提取的包含 SliderStyle1 模板的 WPF 基本样式原本。（实际应用无错误并能正常运行。）

```xaml
<Style x:Key="SliderStyle1" TargetType="{x:Type Slider}">
    <Setter Property="Stylus.IsPressAndHoldEnabled" Value="false"/>
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="BorderBrush" Value="Transparent"/>
    <Setter Property="Foreground" Value="{StaticResource SliderThumb.Static.Foreground}"/>
    <Setter Property="Template" Value="{StaticResource SliderHorizontal}"/>
    <Style.Triggers>
        <Trigger Property="Orientation" Value="Vertical">
            <Setter Property="Template" Value="{StaticResource SliderVertical}"/>
        </Trigger>
    </Style.Triggers>
</Style>
```

从内容来看，默认的 Template 被设置为 SliderHorizontal（ControlTemplate），通过触发器，当 Orientation 属性值为 Vertical 时，Template 切换为 SliderVertical（ControlTemplate）。

> 这样通过模块化管理 ControlTemplate 模板，可以一目了然地看到样式的结构。即使在不需要切换的情况下，这也是一种值得尝试的管理方式。我们经常使用这样的方法，并且从这个过程中也能获得灵感。

因此，Slider 控件的实际功能分别在 SliderHorizontal 和 SliderVertical 两个 ControlTemplate 模板中实现。

现在我们来查看一下默认设置的 SliderHorizontal（ControlTemplate）模板。

##### 确认ControlTemplate：

分别查看 Horizontal/Vertical 专用模板。在App.xaml 文件中可以找到。

- [x] 检查 Horizontal 专用模板
- [x] 检查 Vertical 专用模板

ControlTemplate: **SliderHorizontal**

```xaml
<ControlTemplate x:Key="SliderHorizontal" TargetType="{x:Type Slider}">
    <Border ...>
		...
    </Border>
    <ControlTemplate.Triggers>
		...
    </ControlTemplate.Triggers>
</ControlTemplate>
```

ControlTemplate: **SliderVertical**

```xaml
<ControlTemplate x:Key="SliderVertical" TargetType="{x:Type Slider}">
    <Border ...>
		...
    </Border>
    <ControlTemplate.Triggers>
		...
    </ControlTemplate.Triggers>
</ControlTemplate>
```

如上所示，Horizontal/Vertical 的源代码被分别实现。两者的实现内容相同，仅在设计方向上有所不同。

### 名称: `PART_`

在自定义控件的结构中，保持 XAML 和代码后端的紧密连接是非常重要的。然而，为了进行这种连接，需要通过 `GetTemplateChild` 方法找到控件名称，这在可读性方面并不理想。为了改善这种开发方式并进行系统化管理，使用了 `PART_` 命名规则。

这是一个命名规则，它在所有通过 `GetTemplateChild` 找到的控件名称前加上 `PART_` 前缀，以便在 XAML 中推测其功能。因此，在分析控件模板时，如果发现以 `PART_` 开头的控件名称，可以猜测它们是必需的元素，并提前预料到删除它们可能产生的副作用。

最终，这对于实现自定义控件是非常有帮助的。此外，这一规则不仅在 WPF 中适用，在共享 XAML 的其他跨平台框架中也常见，是一个重要且不可忽视的部分。

### `Slider` 控件中的两个 `PART_` 控件

- `PART_Track`
- `PART_SelectionRange`

结果表明，除了这两个 `PART_` 控件外，其他控件在代码后端中都不会使用。通过这个命名规则可以保证这一点。因此，在自定义控件开发中严格遵守这一规则是非常重要的。

### 测试：有意更改 `PART_Track` 的名称后检查影响

现在，我们有意更改 `PART_Track` 控件的名称。

```xaml
<Track x:Name="PART_Track1" Grid.Row="1">
    ...
</Track>
```

> 请仔细检查是否在 `SliderHorizontal` 区域内。

运行应用程序时，如同在教程视频中那样，无论如何拖动，`Track` 的 `Thumb` 都不会左右移动。`Thumb` 无法移动的原因是，由于之前有意更改了名称，代码后端无法通过 `GetTemplateChild` 方法找到 `PART_Track` 控件。

因此，由于找不到 `PART_Track` 控件，拖动鼠标时没有目标 `Thumb` 来移动。将名称 `PART_Track1` 恢复到原来的 `PART_Track` 后，功能将恢复正常。

> 这种现象在许多其他基本控件中也可以找到，TextBox 的 `PART_ContentHost` 就是其中之一。

### 测试：有意更改 `PART_SelectionRange` 的名称后检查影响

接下来，我们也有意更改 `PART_SelectionRange` 控件的名称。

```xaml
<Rectangle x:Name="PART_SelectionRange1" .../>
```

> 请仔细检查是否在 `SliderHorizontal` 区域内。(x2)

接着，我们还需要更改触发器部分使用 `PART_SelectionRange` 的部分。

```xaml
<Trigger Property="IsSelectionRangeEnabled" Value="true">
    <Setter Property="Visibility" TargetName="PART_SelectionRange1" Value="Visible"/>
</Trigger>
```

> 请仔细检查是否在 `SliderHorizontal` 区域内。(x3)

在 `Slider` 中，我们还需要设置如下属性来启用 `PART_SelectionRange`。

```xaml
<Slider Style="{DynamicResource SliderStyle1}"
        Minimum="0" Maximum="100"
        SelectionStart="0" SelectionEnd="50"
        IsSelectionRangeEnabled="True"/>
```

> 需要设置 `Minimum`、`Maximum` 以及 `SelectionStart`、`SelectionEnd`、`IsSelectionRange` 等属性，才能启用范围区域。

#### 更改前: `PART_SelectionRange`

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/664d0d18-1fd4-4c70-bb91-b9d655bada3d" style="width:400px; float: left"/>

> 在更改前，可以看到正常显示的范围区域。

#### 更改后: `PART_SelectionRange1`

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/c1708893-0b3c-47fa-a793-c26d85ad55ca" style="width:400px; float: left"/>

> 更改后，范围区域不再显示。

同样，由于无法在内部找到 `PART_SelectionRange` 控件，无法计算范围区域的目标。

由此可见，WPF控件虽然功能构建相对松散，但却构建了一个模块化的结构。因此，如果我们能够利用好这些特性，不仅可以很好的利用已经实现的功能，还可以排除很多不必要的功能。

## 7. Code behind 确认 (GitHub 开源代码)

前面我们详细讨论了`PART_`控件的命名规则及其影响，现在是时候看看这些控件在实际类中是如何使用的。

Code behind（类）区域无法通过直接提取来确认。因此，我们需要通过WPF代码库中的官方源代码查看。这个部分建议查看我们的视频教程了解具体的查看方法。

在实际的源代码中，每个`PART_`控件的名称都如下所示地定义为`string`：

```csharp
private const string TrackName = "PART_Track";
private const string SelectionRangeElementName = "PART_SelectionRange";
```

> 因为名称是固定定义的，所以必须遵守这个命名规则。

##### WPF: OnApplyTemplate

接下来我们从(ControlTemplate) 模版中获取 Track 和 SlectionRange的这个部分开始查看。

```csharp
public override void OnApplyTemplate()
{
    base.OnApplyTemplate();

    SelectionRangeElement = GetTemplateChild(SelectionRangeElementName) as FrameworkElement;
    Track = GetTemplateChild(TrackName) as Track;

    if (_autoToolTip != null)
    {
        _autoToolTip.PlacementTarget = Track != null ? Track.Thumb : null;
    }
}
```

> 注意：(Override) OnApplyTemplate 方法在类和样式关联后调用，所以这是使用 GetTemplateChild 的最佳时机。

查看源代码，我们可以看到它们分别被定义为 FrameworkElement 和 Track。

- [x] PART_SelectionRange: SelectionRangeElement (FrameworkElement)
- [x] PART_Track: TrackName (Track)

这里需要注意的是，Track 的类型与 XAML 中的类型相同，但 SelectionRange 被定义为 FrameworkElement，而不是原来的 Rectangle，这意味着 SelectionRange 可以是任何类型的控件，而不仅仅是 Rectangle。这是有意将类型定义得更加灵活。

因此，我们可以推测 SelectionRangeElement (定义为 FrameworkElement 类型) 仅处理此类型所能处理的基本功能。

下面是实际处理 SelectionRangeElement 的部分。


```csharp
private void UpdateSelectionRangeElementPositionAndSize()
{
    Size trackSize = new Size(0d, 0d);
    Size thumbSize = new Size(0d, 0d);

    if (Track == null || DoubleUtil.LessThan(SelectionEnd,SelectionStart))
    {
        return;
    }

    trackSize = Track.RenderSize;
    thumbSize = (Track.Thumb != null) ? Track.Thumb.RenderSize : new Size(0d, 0d);

    double range = Maximum - Minimum;
    double valueToSize;

    FrameworkElement rangeElement = this.SelectionRangeElement as FrameworkElement;

    if (rangeElement == null)
    {
        return;
    }

    if (Orientation == Orientation.Horizontal)
    {
        // Calculate part size for HorizontalSlider
        if (DoubleUtil.AreClose(range, 0d) || (DoubleUtil.AreClose(trackSize.Width, thumbSize.Width)))
        {
            valueToSize = 0d;
        }
        else
        {
            valueToSize = Math.Max(0.0, (trackSize.Width - thumbSize.Width) / range);
        }

        rangeElement.Width = ((SelectionEnd - SelectionStart) * valueToSize);
        if (IsDirectionReversed)
        {
            Canvas.SetLeft(rangeElement, (thumbSize.Width * 0.5) + Math.Max(Maximum - SelectionEnd, 0) * valueToSize);
        }
        else
        {
            Canvas.SetLeft(rangeElement, (thumbSize.Width * 0.5) + Math.Max(SelectionStart - Minimum, 0) * valueToSize);
        }
    }
    else
    {
        // Calculate part size for VerticalSlider
        if (DoubleUtil.AreClose(range, 0d) || (DoubleUtil.AreClose(trackSize.Height, thumbSize.Height)))
        {
            valueToSize = 0d;
        }
        else
        {
            valueToSize = Math.Max(0.0, (trackSize.Height - thumbSize.Height) / range);
        }

        rangeElement.Height = ((SelectionEnd - SelectionStart) * valueToSize);
        if (IsDirectionReversed)
        {
            Canvas.SetTop(rangeElement, (thumbSize.Height * 0.5) + Math.Max(SelectionStart - Minimum, 0) * valueToSize);
        }
        else
        {
            Canvas.SetTop(rangeElement, (thumbSize.Height * 0.5) + Math.Max(Maximum - SelectionEnd,0) * valueToSize);
        }
    }
}
```
> 由于 Orientation 的逻辑（水平/垂直）实际上是相同的，因此我们只需要关注水平方向的逻辑即可。

正是通过这个 (UpdateSelectionRangeElementPositionAndSize) 方法来决定 SelectionRange 的大小和位置。尽管源代码的量可能看起来有些多，但考虑到 Orientation 分支逻辑的重复代码，我们可以很容易地看出对 SelectionRange 的处理还是相对简洁的。

这样，我们可以通过反向查找和分析 CustomControl 控件以及 `PART_` 控件在内部是如何处理的。

## 8. 跨平台中的 OnApplyTemplate

许多跨平台框架在设计上与 WPF 有许多相似之处，因此它们在流程上也遵循类似的模式。因此，我们可以基于前面分析的 OnApplyTemplate 来看看其他平台上的实现。

共享 OnApplyTemplate 设计的平台列表：

- [x] **AvaloniaUI**
- [x] **Uno Platform**
- [x] **OpenSilver**
- [x] **MAUI**
- [x] **Xamarin**
- [ ] UWP
- [ ] WinUI 3
- [ ] Silverlight

在这些平台中，已勾选的 AvaloniaUI、Uno Platform、OpenSilver、MAUI 和 Xamarin 的原始源代码值得我们进一步查看。

> 值得一提的是，除了 Silverlight，这些平台的代码库都由 Microsoft 官方组织 Dotnet 或 xamarin 在 GitHub 上进行管理，因此我们可以轻松找到这些代码库。


##### AvaloniaUI: OnApplyTemplate

下面是 AvaloniaUI 中 Slider 控件的 OnApplyTemplate 部分。

```csharp
protected override void OnApplyTemplate(TemplateAppliedEventArgs e)
{
    ...
    base.OnApplyTemplate(e);
    _decreaseButton = e.NameScope.Find<Button>("PART_DecreaseButton");
    _track = e.NameScope.Find<Track>("PART_Track");
    _increaseButton = e.NameScope.Find<Button>("PART_IncreaseButton");
    ...
}
```

> AvaloniaUI 也是开源项目，因此可以像 WPF 一样查看所有源代码。可以看到它的方式与 WPF 非常相似。

通过这种命名规则，我们可以很容易地看出这三个带有 `PART_` 前缀的控件在 XAML 中是作为必需组件存在的。那么我们也来看一下 Uno 的实现吧。

##### Uno Platform: OnApplyTemplate

```csharp
protected override void OnApplyTemplate()
{
    ...	
    base.OnApplyTemplate(e);
	
    // 获取组件
    var spElementHorizontalTemplateAsDO = GetTemplateChild("HorizontalTemplate");
    _tpElementHorizontalTemplate = spElementHorizontalTemplateAsDO as FrameworkElement;
    var spElementTopTickBarAsDO = GetTemplateChild("TopTickBar");
    ...
}
```

> Uno 中的实现方式也和 WPF 相似。

不过，Uno 并没有遵循 `PART_` 命名规则。可能是从一开始就决定不使用这种规则。

当然在 MAUI、OpenSilver 和 Xamarin 中我们也可以找到类似的代码。

##### MAUI: OnApplyTemplate

```csharp
protected override void OnApplyTemplate()
{
    base.OnApplyTemplate();
    _thumb = (Thumb)GetTemplateChild("HorizontalThumb");
    _originalThumbStyle = _thumb.Style;

    UpdateThumbStyle();
}
```

> 在 WPF 中，我们通常声明变量名时会使用 Track，而在 MAUI 中而是使用下划线前缀。所以比较各个平台的命名规则和开发模式也是开源中非常有趣的事情。

##### OpenSilver: OnApplyTemplate

```csharp
public override void OnApplyTemplate()
{
    base.OnApplyTemplate();

    // 获取组件
    ...
    ElementVerticalThumb = GetTemplateChild(ElementVerticalThumbName) as Thumb;
    ...
}
```

> 似乎使用了与 Uno 类似的注释风格。

##### Xamarin: OnApplyTemplate

```csharp
public override void OnApplyTemplate()
{
    base.OnApplyTemplate();
    FormsContentControl = Template.FindName("PART_Multi_Content", this) 
        as FormsTransitioningContentControl;
}
```

> 虽然略有不同，但都共享了类似于 WPF 的设计。

## 9. 总结 Slider 分析

我们详细分析了 WPF 的 Slider 控件。通过这次分析，我们可以确认 WPF 的 (CustomControl) 控件设计是非常精巧的。这些规则同样适用于其他控件，并且这是设计新控件时非常重要的基础。

有些人认为 WPF 已经死了（Is WPF Dead），但实际上WPF依然存在，并且持续发展。深入研究WPF会带来无限的可能性和乐趣。

过去，我们可能梦想过能用 WPF 进行所有开发，但随着 Xamarin 和 .NET Core 等平台的出现，这个梦想已经成为现实。这是热爱 WPF 的众多开发者共同努力的结果。

通过这次分析，我们了解了基本控件分析的重要性。建议大家通过教程视频再次复习和学习这些内容。

接下来，我们将基于这次分析，创建一个新的 Riot 风格的 (CustomControl) Slider。

## 10. 创建 Riot 风格的 Slider (CustomControl) 控件

现在，我们将基于对Slider的分析，利用控件的特性，通过最少的设计来实现它。在这个过程中，核心是利用PART_部分而不使用任何代码来完成控件。

##### 动机

直接使用默认的Slider并不常见，所以我们需要灵感。恰好，我曾尝试设计一个以Riot Games的《英雄联盟》游戏为设计概念的Slider，因此决定以此为控件的灵感来源。

实际上，这个设计源于几年前我想用WPF实现一个高水平的游戏客户端，开始了“英雄联盟”应用程序的开发。因此，如果你想了解这个Slider控件的实际效果，可以查看[这个仓库](https://github.com/jamesnet214/leagueoflegends)。通过Fork，任何人都可以参与贡献，目前已经有超过80次Fork记录。

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/003948fe-70c3-4be4-927f-d0b391ac7b05" style="width:600px; float: left"/>

那么现在我们开始创建一个新的 (CustomControl) Slider 控件吧。


## 11. 创建和启动项目

在前面创建了DemoApp（WPF应用程序）项目后，现在是时候创建一个CustomControl库项目了。如果你希望在DemoApp项目中继续进行，可以跳过这次的项目创建过程。

##### 项目创建：

- [x] 项目名称：SliderControl
- [x] 项目类型：WPF CustomControl Library
- [x] 项目版本：.NET 8.0

<img width="253" alt="11-1" src="https://github.com/vickyqu115/riotslider/assets/101777355/eb164539-2bd1-46b1-a658-d339cc39b865">

##### 删除基础文件：

- [x] AssemblyInfo.cs
- [x] Themes/Generic.xaml
- [x] CustomControl1.cs

<img width="243" alt="11-2" src="https://github.com/vickyqu115/riotslider/assets/101777355/ecd698ad-674e-440f-84b4-481caa5b8272">

这些被删除的文件实际上是构成(CustomControl)控件的必需文件，但为了重新组织项目结构或位置，我们会将它们删除。

> 在重新创建控件的过程中，删除的元素会自动重新生成，因此不需要担心文件删除的问题。

##### 创建(CustomControl)文件：

- [x] 创建RiotSlider.cs (CustomControl)Class

<img width="253" alt="11-3" src="https://github.com/vickyqu115/riotslider/assets/101777355/0c52e698-2353-4e11-8253-0a801de9f119">

只有在将文件类型设置为CustomControl类时，DefaultStyleKeyProperty相关语句才会与静态构造函数一起包含。如果在创建过程中选择了错误的类型，则会遗漏CustomControl相关代码，需要手动输入，因此务必仔细确认每个步骤。

```csharp
public class RiotSlider : Slider
{
    static RiotSlider()
    {
        DefaultStyleKeyProperty.OverrideMetadata(typeof(RiotSlider), new FrameworkPropertyMetadata(typeof(RiotSlider)));
    }
}
```

##### 确认自动生成的文件：

- [x] Properties/AssemblyInfo.cs
- [x] Themes/Generic.xaml

<img width="708" alt="11-4" src="https://github.com/vickyqu115/riotslider/assets/101777355/867bdc09-adf2-4852-9aa3-3c4828df6672">

如果不将文件类型设置为CustomControl类，同样这些文件也不会自动生成。这一点务必注意。

## 12. TextBlock (Hi Slider)

接下来是测试Slider控件是否已经正确配置为CustomControl格式的步骤。

初次创建(CustomControl) Slider控件时，默认会生成一个空的ControlTemplate模板。因此，为了在视觉上确认控件，我们通常会添加一些设计元素。我们将在这里添加一个临时的TextBlock和文本。

##### 添加临时TextBlock：

- [x] Hi Slider

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Template">
    	<Setter.Value>        
            <ControlTemplate TargetType="{x:Type RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
	                <TextBlock Text="Hi Slider" Foreground="Blue"/>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

> 在空的ControlTemplate的Border中添加TextBlock和“Hi Slider”文本。也可以更改字体颜色。

## 13. 添加引用并测试运行

准备好测试用的TextBlock后，现在是时候运行DemoApp应用程序，检查RiotSlider控件是否正确加载。

##### 在DemoApp项目中添加引用：

- [x] 添加引用：RiotSliderControl项目

##### 在MainWindow.xaml中声明xmlns并添加控件：

- [x] 声明xmlns：xmlns:riots
- [x] 插入控件：riots:RiotSlider

```xaml
<Window x:Class="DemoApp.MainWindow" 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:riots="clr-namespace:SliderControl;assembly=SliderControl"
        mc:ignorable="d"
        Title="MainWindow" Width="800" Height="450">
    <Grid>
        <riots:RiotSlider/>
    </Grid>
</Window>
```

这样，我们就可以在DemoApp应用程序中查看和测试我们创建的RiotSlider控件了。

##### 检查运行结果：

- [x] Riot Slider: "Hi Slider"

<img width="395" alt="11-5" src="https://github.com/vickyqu115/riotslider/assets/101777355/145a7aa7-364d-41a2-a74a-dfe0b95a9e26">

到这里，我们已经完成了(CustomControl) RiotSlider控件的配置，并确认它能够正常运行。

由于CustomControl方式比UserControl方式复杂，在熟悉这个过程之前，可能会遇到一些困难。因此，需要通过重复练习来克服这些困难。

现在，这个RiotSlider控件已经模块化为CustomControl形式，便于管理。我们可以将这个控件上传到GitHub仓库进行管理，或者发布到NuGet包进行分发。WPF中的CustomControl模块化在管理方面有很多优势，所以在项目设计时应考虑这一点。

当然，这个项目已经通过NuGet Package商店进行发布。挺有意思的吧？

## 14. 设置Riot Slider的大小

接下来，我们将设置控件的大小。

WPF提供了非常强大和灵活 (Responsive) 的响应式布局。因此，在指定控件大小时，通常会设计为响应式布局。然而，对于某些特殊控件，比如包含许多设计元素的Slider，可能需要设置固定的高度或宽度来实现自然的设计。因此，根据控件的特性灵活应对是很重要的。

这次，我们将设计一个高度为50的(Thumb)控件。因此，我们会预先指定RiotSlider的高度。此外，虽然宽度将作为Track的移动路径实现响应式，但为了开发方便，我们会暂时把它限制为200。

##### 调整控件尺寸和颜色：

- [x] 宽度：200
- [x] 高度：50
- [x] 背景色："#EEEEEE"

```xaml
<Window x:Class="DemoApp.MainWindow" 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression.blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:riots="clr-namespace:SliderControl;assembly=SliderControl"
        mc:ignorable="d"
        Title="MainWindow" Width="800" Height="450">
    <Grid>
        <riots:RiotSlider Width="200" Height="50" Background="#EEEEEE"/>
    </Grid>
</Window>
```

> 为了更好地估计控件尺寸，可以临时更改背景色，便于识别控件。这是个小技巧。

##### 检查运行结果：

- [x] 检查控件尺寸：宽度/高度
- [x] 检查控件颜色：背景色

<img width="591" alt="11-6" src="https://github.com/vickyqu115/riotslider/assets/101777355/6e84759d-cb7c-4582-a9d8-36f05ca2df1f">

在确认运行结果没有问题后就可以移除背景色了。


## 15. PART_Track

Track是包含Thumb的Slider的核心控制元素。通过分析，我们可以看到通过`PART_Track`声明，Slider控件可以处理所有这些功能。因此，在实现过程中正确地包含这个关键元素是非常重要的。

让我们仔细看看。

##### 添加Track：

- [x] 插入PART_Track控制元素

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Track x:Name="PART_Track"/>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

> Track是少数几个直接继承自FrameworkElement而不是Control的控件之一。这意味着它没有像ControlTemplate那样直接设计布局的资格。因此，它包含Thumb并直接构建布局，所以可以认为这个控件主要关注的是Thumb。

##### 定义Thumb：

接下来，我们定义在Track上移动的Thumb。

- [x] 扩展Thumb并定义模板
- [x] 实现Ellipse

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Track x:Name="PART_Track">
                        <Track.Thumb>
                            <Thumb>
                                <Thumb.Template>
                                    <ControlTemplate>
                                        <Ellipse Width="50" Height="50" Fill="#000000"/>
                                    </ControlTemplate>
                                </Thumb.Template>
                            </Thumb>
                        </Track.Thumb>
                    </Track>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

> 这是在Track中直接扩展并实现Thumb的案例。语法上可能有些难理解，但教程视频中有详细的视觉解释，建议大家观看教学视频。

在这个Thumb示例中，通过模板定义控件与Track不同。这意味着Thumb继承自Control而不是FrameworkElement，因此可以通过ControlTemplate来灵活设计控件。

##### 检查运行结果：

- [x] 检查Thumb (Ellipse) 设计
- [x] 检查Track移动功能

<img width="372" alt="11-7" src="https://github.com/vickyqu115/riotslider/assets/101777355/59f2ecdb-6468-40f4-804f-92f31926abd5">

由于Thumb被设计为Ellipse形状，因此这个大的（50x50）椭圆会在Track区域内移动。但是，如果将Track的名称从`PART_Track`更改为其他名称，Thumb将无法移动。

> 为了再次认识到这种相关性，大家可以尝试更改一下名称。


## 16. 添加滑块条

接下来我们来添加滑块。这一步只是为了添加设计元素，不涉及功能。所以省略这一步，也不会影响功能。但由于接下来的步骤是 SelectionRange 阶段，并需要结合设计元素，所以这一步也需要仔细进行。

##### 修改布局：

到目前为止，Border 内只有 Track 元素，但现在需要添加滑块条，所以需要修改现有布局。此外，由于 Track 和滑块条需要叠加效果，因此使用 Grid 是唯一的选择。因此，首先将 Track 包裹在 Grid 中。

- [x] 修改布局：使用 Grid

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Grid>
                        <Track x:Name="PART_Track">
                            <Track.Thumb>
                                <Thumb>
                                    <Thumb.Template>
                                        <ControlTemplate>
                                            <Ellipse Width="50" Height="50" Fill="#000000"/>
                                        </ControlTemplate>
                                    </Thumb.Template>
                                </Thumb>
                            </Track.Thumb>
                        </Track>
                    </Grid>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

由于只需要简单的叠加效果，因此不需要使用 Grid 的 RowDefinitions 或 ColumnDefinitions。

##### 添加与 Track 叠加的滑块条：

滑块条需要与 Track 叠加，但需要先逻辑上确定哪个元素在前（前端）。由于 Track 的 Thumb 控件需要覆盖滑块条区域，因此需要先添加滑块条，然后再声明 Track。

- [x] 添加：（Border）滑块条
- [x] 高度：2.5
- [x] 背景色：#CCCCCC

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Grid>
                        <Border Background="#CCCCCC" Height="2.5"/>
                        <Track x:Name="PART_Track">
                            <Track.Thumb>
                                <Thumb>
                                    <Thumb.Template>
                                        <ControlTemplate>
                                            <Ellipse Width="50" Height="50" Fill="#000000"/>
                                        </ControlTemplate>
                                    </Thumb.Template>
                                </Thumb>
                            </Track.Thumb>
                        </Track>
                    </Grid>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

此外，由于需要视觉上表现 Track 的长度，所以使用 Border 这样的布局是有效的。特别是 Border 的 CornerRadius 属性，可以处理边角的圆角效果，相比其他控件可以表达更丰富的设计。

##### 运行结果确认:

- [x] 确认 Thumb 的移动: (Ellipse)
- [x] 确认滑块条设计: (Border)

<img width="367" alt="11-8" src="https://github.com/vickyqu115/riotslider/assets/101777355/6e3bc204-c9ad-4bb4-ab7c-2c56c033d6b0">

如图所示，滑块条的设计和位置应与 Track 的移动路径和 Thumb 的移动和谐一致，这是本阶段的关键点。

## 17. 调整滑块条和 Track 之间的误差

虽然滑块条的设计和位置看起来不错，但实际上 Track 的移动范围在起始和结束处各限制了 Thumb 的半径。查看 WPF 的原始源码，我们可以发现如下代码：

```csharp
Canvas.SetLeft(rangeElement, (thumbSize.Width * 0.5) + Math.Max(Maximum - SelectionEnd, 0) * valueToSize);
```

> 上述代码是针对 Orientation="Horizontal" 的情况。因此，如果值变为垂直方向，则需要更改为 Height。

从这段代码中我们可以推断，Track 的实际移动范围也在内部被 ThumbSize 的半径限制了。因此，我们之前添加的滑块条由于不是 Slider 控件内部管理的 `PART_` 元素，所以需要手动应用这一规则。虽然可以通过动态方法处理，但在此步骤中我们通过 Margin 属性来精确调整滑块条和 Track 移动范围之间的误差。

##### 设置 Thumb Ellipse 的透明度：

为了更方便地工作，指定 Ellipse 控件的透明度。

- [x] Ellipse 填充颜色: #55000000

```xaml
<Ellipse Width="50" Height="50" Fill="#55000000"/>
```

> 在 WPF 中，设置元素透明度时通常使用对象的透明度属性 Opacity，但通过使用颜色的 Alpha 值，可以只对特定颜色应用透明度。这是 WPF 的一个小技巧，非常值得利用。

##### 应用滑块条的 Margin 以考虑 Thumb 的半径：

目前 Ellipse 的宽度为 50，因此左右各应用 25 的 Margin。

- [x] Margin="25 0 25 0"

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Grid>
                        <Border Background="#CCCCCC" Height="2.5" Margin="25 0 25 0"/>
                        <Track x:Name="PART_Track">
                            <Track.Thumb>
                                <Thumb>
                                    <Thumb.Template>
                                        <ControlTemplate>
                                            <Ellipse Width="50" Height="50" Fill="#55000000"/>
                                        </ControlTemplate>
                                    </Thumb.Template>
                                </Thumb>
                            </Track.Thumb>
                        </Track>
                    </Grid>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```


##### 结果确认:

- [x] 确认 Thumb 半径和 Margin尺寸

<img width="389" alt="11-9" src="https://github.com/vickyqu115/riotslider/assets/101777355/b1bfdef5-e8dd-4b38-9d7f-60eeccc00dcd">

如图所示，可以看到 Track 的最大移动范围和滑块条的设计尺寸完全一致。

当然之后也可以再考虑考虑这次Sync 操作动态处理。目前想到的一种方法是将这个滑块条控件指定为 `PART_`，然后在 CodeBehind 中处理。当然还有其他各种方法，值得思考一下。

## 18. PART_SelectionRange

SelectionRange 是通过之前的 Slider 分析，负责指定特定 Range 范围的元素。

这个控件也和 Track 一样是 `PART_` 元素，Slider 控件内部处理其所有功能，因此只需按照约定的名称正确放置即可。由于设计上与滑块条高度一致，所以高度应与之前添加的滑块条相同。

##### 添加 SelectionRange Border 区域:

- [x] 名称: `PART_SelectionRange`
- [x] 高度: 2.5 
- [x] 背景色: #000000
- [x] Margin: 25 0 25 0

```xaml
<Border x:Name="PART_SelectionRange" 
        Background="#000000" 
        Height="2.5"
        Margin="25 0 25 0"/>
```

##### 指定 Range 范围:

通过 RelativeSource Binding 将 SelectionEnd 的范围与 Value 值同步。

- [x] SelectionStart: 0
- [x] SelectionEnd: {Binding RelativeSource {RelativeSource Self}, Path=Value}

```xaml
<Setter Property="SelectionStart" Value="0"/>
<Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
```

通过将 SelectionEnd 的值与 Value 值同步，可以动态表达 Range 范围。实际上，英雄联盟客户端应用程序的 Slider 控件也是以相同的方式实现的。

##### 完整代码:

将以上步骤综合，得到完整代码如下：

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Grid>
                        <Border x:Name="PART_SelectionRange" 
                                Background="#000000" 
                                Height="2.5"
                                Margin="25 0 25 0"/>
                        <Border Background="#CCCCCC" Height="2.5" Margin="25 0 25 0"/>
                        <Track x:Name="PART_Track">
                            <Track.Thumb>
                                <Thumb>
                                    <Thumb.Template>
                                        <ControlTemplate>
                                            <Ellipse Width="50" Height="50" Fill="#55000000"/>
                                        </ControlTemplate>
                                    </Thumb.Template>
                                </Thumb>
                            </Track.Thumb>
                        </Track>
                    </Grid>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

这样，我们就完成了 SelectionRange 的添加和滑块条与 Track 的同步处理。

##### 处理 IsSelectionRangeEnabled 启用:

考虑到 Riot Slider 控件的概念，可能不需要处理这个功能。但由于可以通过触发器简单地实现，我们可以在学习的过程中尝试一下。

> 教程视频中未涉及此部分内容。

- [x] IsSelectionRangeEnabled: True

```xaml
<Setter Property="IsSelectionRangeEnabled" Value="True"/>
```

> 将 IsSelectionRangeEnabled 属性的默认值设置为 True。

- [x] PART_SelectionRange Visibility: (默认) Collapsed

```xaml
<Border x:Name="PART_SelectionRange" 
        Background="#000000" 
        Height="2.5"
        Margin="25 0 25 0"
        Visibility="Collapsed"/>
```

> 将 SelectionRange 的默认 Visibility 设置为 Collapsed。

- [x] 触发器: PART_SelectionRange.Visibility=Visible

```xaml
<Trigger Property="IsSelectionRangeEnabled" Value="True">
    <Setter TargetName="PART_SelectionRange" Property="Visibility" Value="Visible"/>
</Trigger>
```

> 将 SelectionRange 的默认 Visibility 设置为 Collapsed，当 IsSelectionRangeEnabled 属性的值为 True 时，通过触发器将 Visibility 设置为 Visible。虽然可以反向设置，但在触发器中检查 Boolean 属性的 True 值更符合常规代码习惯。

##### 源代码及运行结果确认:

- [x] 应用 Setter
- [x] SelectionRange (默认) Collapsed
- [x] 应用触发器 IsSelectionRangeEnabled

```xaml
<Style TargetType="{x:Type local:RiotSlider}">
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="SelectionStart" Value="0"/>
    <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
    <Setter Property="Minimum" Value="0"/>
    <Setter Property="Maximum" Value="100"/>
    <Setter Property="IsSelectionRangeEnabled" Value="True"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                <Border Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                    <Grid>
                        <Border Background="#CCCCCC" Height="2.5" Margin="25 0 25 0"/>
                        <Border x:Name="PART_SelectionRange" 
                                Background="#000000" 
                                Height="2.5"
                                Margin="25 0 25 0"
                                HorizontalAlignment="Left"
                                Visibility="Collapsed"/>
                        <Track x:Name="PART_Track">
                            <Track.Thumb>
                                <Thumb>
                                    <Thumb.Template>
                                        <ControlTemplate>
                                            <Ellipse Width="50" Height="50" Fill="#55000000"/>
                                        </ControlTemplate>
                                    </Thumb.Template>
                                </Thumb>
                            </Track.Thumb>
                        </Track>
                    </Grid>
                </Border>
                <ControlTemplate.Triggers>
                    <Trigger Property="IsSelectionRangeEnabled" Value="True">
                           <Setter TargetName="PART_SelectionRange" Property="Visibility" Value="Visible"/>
                    </Trigger>
                </ControlTemplate.Triggers>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

<img width="391" alt="11-10" src="https://github.com/vickyqu115/riotslider/assets/101777355/617d041b-b54f-4df9-aabb-4d63ac1d7815">

现在，我们已经添加了构成 Slider 功能的所有元素。接下来，我们将再次检查 `PART_` 控件元素的功能，以完成此步骤，并进入下一个阶段。

##### 再次确认 PART_ 控件功能:

- [x] PART_Track
- [x] PART_SelectionRange


### 19. 添加 Riot 风格的设计元素

接下来是为 Riot Slider 添加所需的设计元素。

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/c060395c-03f8-4abf-a630-bf17a2587106" style="width: 600px; float: left"/>

##### 添加 Geometry 设计资源:

- [x] Geometry: ThumbData

```xaml
<Geometry x:Key="ThumbData">
    M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z
</Geometry>
```

我们使用 Geometry Path 元素来绘制 Thumb 图标，而不是图像文件，这是因为它具有以下优点：可以通过颜色触发器自由更改颜色，并保持基于矢量的高质量。

> 这种简单的图标，非设计师也可以通过 Visual Studio Blend 或 Figma、Illustrator 等工具轻松制作。不难，值得一试。

向同事请求矢量图标时，建议使用 SVG 格式，并要求单色设计的图标为组合形态。此外，还有很多开源图标可免费使用。例如 [Pictogrammers](https://pictogrammers.com) 提供超过 8000 个单色设计图标，包括 `.SVG`、`.PNG` 和 `.XAML` 格式。并且通过 GitHub 开源管理，可以查看主要贡献者或参与开源项目。

接下来是添加主要颜色资源。

##### 添加 LinearGradientBrush 设计资源:

- [x] LinearGradientBrush: ThumbColor
- [x] LinearGradientBrush: ThumbOver
- [x] LinearGradientBrush: ThumbDrag
- [x] SolidColorBrush: SliderColor
- [x] LinearGradientBrush: RangeColor
- [x] LinearGradientBrush: SliderOver
- [x] LinearGradientBrush: SliderDrag

```xaml
<LinearGradientBrush x:Key="ThumbColor" StartPoint="0.5,0" EndPoint="0.5,1">
    <GradientStop Color="#B79248" Offset="0"/>
    <GradientStop Color="#997530" Offset="0.5"/>
    <GradientStop Color="#74592B" Offset="1"/>
</LinearGradientBrush>

<LinearGradientBrush x:Key="ThumbOver" StartPoint="0.5,0" EndPoint="0.5,1">
    <GradientStop Color="#EDE1C8" Offset="0"/>
    <GradientStop Color="#DCC088" Offset="0.5"/>
    <GradientStop Color="#CBA14A" Offset="1"/>
</LinearGradientBrush>

<LinearGradientBrush x:Key="ThumbDrag" StartPoint="0.5,0" EndPoint="0.5,1">
    <GradientStop Color="#473814" Offset="0"/>
    <GradientStop Color="#57421B" Offset="0.5"/>
    <GradientStop Color="#684E23" Offset="1"/>
</LinearGradientBrush>

<SolidColorBrush x:Key="SliderColor" Color="#1E2328"/>

<LinearGradientBrush x:Key="RangeColor" StartPoint="0,0.5" EndPoint="1,0.5">
    <GradientStop Color="#463714" Offset="0"/>
    <GradientStop Color="#58471D" Offset="0.5"/>
    <GradientStop Color="#695625" Offset="1"/>
</LinearGradientBrush>

<LinearGradientBrush x:Key="SliderOver" StartPoint="0,0.5" EndPoint="1,0.5">
    <GradientStop Color="#795B28" Offset="0"/>
    <GradientStop Color="#C1963B" Offset="0.5"/>
    <GradientStop Color="#C8AA6D" Offset="1"/>
</LinearGradientBrush>

<LinearGradientBrush x:Key="SliderDrag" StartPoint="0,0.5" EndPoint="1,0.5">
    <GradientStop Color="#685524" Offset="0"/>
    <GradientStop Color="#55441B" Offset="0.5"/>
    <GradientStop Color="#463714" Offset="1"/>
</LinearGradientBrush>
```

> 颜色等设计资源的 x:Key 规则通常有大写或驼峰命名法，以及类似命名空间的方式。个人建议尽量简短。虽然每年看法有所变化，但目前倾向于尽量简短。

仔细观察《英雄联盟》的设计风格，可以发现大量使用渐变色。提取这些颜色的方法是使用 Photoshop 或带有吸管颜色提取功能的应用程序。

> 对于可能存在渐变色的区域，可以多次使用吸管功能提取颜色。多加练习后，眼力也会变得敏锐。

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/b7e9fae1-9f8f-4fab-baab-9cfa1c9e013f" style="width: 500px; float: left"/>

## 20. 实现 Riot 风格的 Thumb

现在，我们将使用准备好的 Geometry 和设计元素，正式创建一个符合《英雄联盟》风格的 Thumb 控件。

在开始之前，我们需要先删除之前定义的临时 Ellipse 作为 Thumb 的模板。因此，删除所有包含 Ellipse 的 Thumb 定义部分。

##### 删除现有 Thumb:

- [x] 删除 Thumb 及其模板

```xaml
<Track x:Name="PART_Track">
    <Track.Thumb>
        <Thumb>
            <Thumb.Template>
                <ControlTemplate>
                    <Ellipse Width="50" Height="50" Fill="#55000000"/>
                </ControlTemplate>
            </Thumb.Template>
        </Thumb>
    </Track.Thumb>
</Track>
```

> 删除直接在 Track 内定义的 Thumb 和模板，只保留 Track。

现在是创建 Riot 风格新 Thumb 的时候了。

刚才删除的 Thumb 是通过扩展 Track 直接定义的临时模板，但这次我们将通过 StaticResource 进行整洁的资源管理来实现它。

##### 定义新的 Thumb 模板:

- [x] 实现 Riot 风格的 Thumb 并细化资源

```xaml
<Style TargetType="{x:Type Thumb}" x:Key="ThumbStyle">
    <Setter Property="Background" Value="#010A13"/>
    <Setter Property="Width" Value="24"/>
    <Setter Property="Height" Value="24"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="{x:Type Thumb}">
                <Grid Background="{TemplateBinding Background}">
                    <Path x:Name="path" Data="{StaticResource ThumbData}" Fill="{StaticResource ThumbColor}"/>
                </Grid>
                <ControlTemplate.Triggers>
                    <Trigger Property="IsMouseOver" Value="True">
                        <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbOver}"/>
                    </Trigger>
                    <Trigger Property="IsDragging" Value="True">
                        <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbDrag}"/>
                    </Trigger>
                </ControlTemplate.Triggers>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

> 在 CustomControl 基础上，XAML 资源管理实际上非常简单。由于通过 Generic.xaml 已经物理分离了资源，因此可以继续通过 x:Key 进一步细化管理。正因如此，我们也将 Geometry 和 LinearGradientBrush 分离出来。这些资源只需要包含在 RiotSlider 控件的样式文件中即可。

Thumb 作为从 Control 继承的控件，可以通过模板（ControlTemplate）进行设计。因此，可以创建一个详细触发器实现的控件。此外，如果想要创建更详细的控件，还可以通过 CustomControl 方式进一步细化 Thumb。这种情况在 WPF 基本控件中非常常见。

探索一些知识。例如，像 ToolBarOverflowPanel 这样的控件，尽管听起来比较陌生，但实际上有很多。这些都是在 CustomControl 基础上需要更细化控件时创建的，通常归类在 Primitives 命名空间中。

因此，归属于 Primitives 命名空间的控件，通常是包含在其他（CustomControl）控件中的。以 Primitives 的代表控件 ToggleButton 为例，它不仅是 CheckBox/RadioButton 的父控件，还可以在 ComboBox 等控件的模板中用作切换项的控件。

> 挺有意思的吧？这些架构概念适用于所有共享 XAML 的跨平台技术。因此，能够灵活应用这些概念，在 AvaloniaUI、Uno、MAUI 等环境中也会大有帮助。

> 当然，归属 Primitives 命名空间的控件不一定都通过 DefaultStyleKey 指定为 CustomControl。其中也有很多只是简单封装的类。

## 21. 声明 Thumb 资源

最后，将 Thumb 以资源形式声明，以便在 Track 中通过 StaticResource 使用。

##### 添加 Thumb 资源:

- [x] 将包含模板的 Thumb 样式与 Thumb 资源连接定义

```xaml
<Thumb x:Key="SliderThumb" Style="{StaticResource ThumbStyle}"/>
```

> 教程视频中详细介绍了这部分内容，如果在语法觉得不是很自然，建议可以参考视频。

现在，只需在 Track 中使用资源化的 Thumb。

##### 在 Track 中简洁定义 Thumb:

- [x] 使用 StaticResource 连接替换现有 Thumb

```xaml
<Track Thumb="{StaticResource SliderThumb}"/>
```

> 使用 Resource 形式的 Thumb 可以大大减少在 Track 中定义 Thumb 时的代码量。此外，通过这种方式管理资源，有助于整体上更清晰地掌握资源，是保持代码质量的重要方法之一。所以需要认真学习这种管理方式。

继续下一个步骤：检查所有 `PART_` 控件功能是否正常。

##### 再次确认 `PART_` 控件功能:

- [x] PART_Track
- [x] PART_SelectionRange


### 22. 完成 RiotSlider 模板 (收尾)

现在我们将完成 RiotSlider 控件的模板实现。除此之外，还包含了 Jamesnet.WPF 库，因此我们使用了 JamesGrid，普通的 Grid 也是可以替代的。

##### (CustomControl) RiotSlider:

- [x] 查看 Generic.xaml 的完整源代码

```xaml
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:james="https://jamesnet.dev/xaml/presentation"
    xmlns:local="clr-namespace:SliderControl">

    <Geometry x:Key="ThumbData">
        M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z
    </Geometry>

    <LinearGradientBrush x:Key="ThumbColor" StartPoint="0.5,0" EndPoint="0.5,1">
        <GradientStop Color="#B79248" Offset="0"/>
        <GradientStop Color="#997530" Offset="0.5"/>
        <GradientStop Color="#74592B" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="ThumbOver" StartPoint="0.5,0" EndPoint="0.5,1">
        <GradientStop Color="#EDE1C8" Offset="0"/>
        <GradientStop Color="#DCC088" Offset="0.5"/>
        <GradientStop Color="#CBA14A" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="ThumbDrag" StartPoint="0.5,0" EndPoint="0.5,1">
        <GradientStop Color="#473814" Offset="0"/>
        <GradientStop Color="#57421B" Offset="0.5"/>
        <GradientStop Color="#684E23" Offset="1"/>
    </LinearGradientBrush>

    <Style TargetType="{x:Type Thumb}" x:Key="ThumbStyle">
        <Setter Property="Background" Value="#010A13"/>
        <Setter Property="Width" Value="24"/>
        <Setter Property="Height" Value="24"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type Thumb}">
                    <Grid Background="{TemplateBinding Background}">
                        <Path x:Name="path" Data="{StaticResource ThumbData}" Fill="{StaticResource ThumbColor}"/>
                    </Grid>
                    <ControlTemplate.Triggers>
                        <Trigger Property="IsMouseOver" Value="True">
                            <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbOver}"/>
                        </Trigger>
                        <Trigger Property="IsDragging" Value="True">
                            <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbDrag}"/>
                        </Trigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>

    <Thumb x:Key="SliderThumb" Style="{StaticResource ThumbStyle}"/>

    <SolidColorBrush x:Key="SliderColor" Color="#1E2328"/>

    <LinearGradientBrush x:Key="RangeColor" StartPoint="0,0.5" EndPoint="1,0.5">
        <GradientStop Color="#463714" Offset="0"/>
        <GradientStop Color="#58471D" Offset="0.5"/>
        <GradientStop Color="#695625" Offset="1"/>
    </LinearGradientBrush>
    
    <LinearGradientBrush x:Key="SliderOver" StartPoint="0,0.5" EndPoint="1,0.5">
        <GradientStop Color="#795B28" Offset="0"/>
        <GradientStop Color="#C1963B" Offset="0.5"/>
        <GradientStop Color="#C8AA6D" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="SliderDrag" StartPoint="0,0.5" EndPoint="1,0.5">
        <GradientStop Color="#685524" Offset="0"/>
        <GradientStop Color="#55441B" Offset="0.5"/>
        <GradientStop Color="#463714" Offset="1"/>
    </LinearGradientBrush>

    <Style TargetType="{x:Type local:RiotSlider}">
        <Setter Property="Minimum" Value="0"/>
        <Setter Property="Maximum" Value="100"/>
        <Setter Property="SelectionStart" Value="0"/>
        <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
        <Setter Property="Background" Value="Transparent"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type local:RiotSlider}">
                    <Grid Background="{TemplateBinding Background}">
                        <james:JamesGrid Rows="*" Columns="Auto,*" Height="2.5" Margin="12 0 12 0">
                            <Border Background="{StaticResource RangeColor}" x:Name="PART_SelectionRange"/>
                            <Border Background="{StaticResource SliderColor}"/>
                        </james:JamesGrid>
                        <Track x:Name="PART_Track" Thumb="{StaticResource SliderThumb}"/>
                    </Grid>
                    <ControlTemplate.Triggers>
                        <DataTrigger Binding="{Binding ElementName=PART_Track, Path=Thumb.IsMouseOver}" Value="True">
                            <Setter TargetName="PART_SelectionRange" Property="Background" Value="{StaticResource SliderOver}"/>
                        </DataTrigger>
                        <DataTrigger Binding="{Binding ElementName=PART_Track, Path=Thumb.IsDragging}" Value="True">
                            <Setter TargetName="PART_SelectionRange" Property="Background" Value="{StaticResource SliderDrag}"/>
                        </DataTrigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```

> 此外，还添加了两个触发器，并将 RiotSlider 控件的 (ControlTemplate) 模板区域细分为资源，以便一目了然地管理所有元素，这是这个项目的特点。

由于 Slider 控件是基于 (CustomControl) 实现的，因此可以像管理资源包一样管理相关资源。

##### 确认最终结果:

- [x] 测试 `PART_Track` 相关功能
- [x] 测试 `PART_SelectionRange` 相关功能
- [x] 确认应用设计元素

> 虽然功能部分已经经过多个阶段的分析和实现，但还是以 `PART_` 控件为标准再检查一次功能。

<img width="435" alt="11-11" src="https://github.com/vickyqu115/riotslider/assets/101777355/f46ed494-a7ee-4fb8-8105-b6a12977bc12">

至此，从分析基础 Slider 控件到实现《英雄联盟》风格的 RiotSlider 控件，基于 (CustomControl) 的开发过程的详解及教程视频的回顾就完成了。

> 如果内容中的代码有与视频内容不一致的地方，可以及时向我们反馈。

## 23. 最后的话

我们从架构的角度深入探讨了WPF Slider 控件。表面上看似简单，但实际上有很多值得讨论的地方，这也说明了通过 WPF 在设计方面可以学习到很多内容。
也非常建议大家观看我们的教程视频，内容中我们展示编码的同时也进行了详细的讲解。

WPF 是一个老旧的平台，因此在漫长的岁月里，各种开发方法论、框架和组件开源库不断发展和变化。随着时间的推移，主流的评价和解释也会不断变化。因此，迄今为止的历史积累实际上都可以成为我们技术的基石。如果能够灵活地判断和评估这些历史，我们就能找到更多丰富且优质的参考资料。主流并不一定总是正确的。

WPF 是一个有些历史的平台，因此在漫长的岁月里，各种开发方法论、框架和组件开源库不断发展和变化。随着时间的推移，主流的评价和解释也会不断变化。因此，迄今为止积累的历史经验实际上都可以成为我们技术的基石。如果能够灵活地判断和评估这些历史，我们就能找到更多丰富且优质的参考资料。主流的观点并不一定总是正确的。
这是我们久违地用心撰写的一篇长篇回顾，希望能传递给更多人。
谢谢！
