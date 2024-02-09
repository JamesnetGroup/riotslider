Introduction
> M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z

[Pictogrammers(rhombus-split-outline)](https://pictogrammers.com/library/mdi/icon/rhombus-split-outline/)

![WechatIMG12](https://github.com/vickyqu115/riotslider/assets/101777355/b686d831-2de6-410b-949d-52b6b9993873)

## Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control

Among WPF's basic controls such as Button, CheckBox, and ToggleButton, the structure is relatively straightforward, enabling complete implementation through XAML without code-behind. In contrast, controls like TextBox, ComboBox, and Slider necessitate intricate handling through both XAML and C# code. A solid understanding of these controls' fundamental CustomControl structures proves incredibly beneficial when customizing afresh.

This time, we will delve deeply into the internal mechanisms of WPF's standard Slider control. While it's not essential to study the internal structure of every control, it's advantageous to analyze the relevant controls from the GitHub repository when needed, as WPF's source code is openly available there.

Moving forward, we plan to dissect and analyze various controls, not just the Slider. We appreciate your continued interest and support for our GitHub repository, CodeProject articles, and tutorial videos provided on YouTube and BiliBili.

![20240201163433798](https://github.com/vickyqu115/riotslider/assets/101777355/f30773e5-9ad3-4b19-b104-5c026af2577e)

## Contents

1. WPF Tutorial Series
2. Specification
3. Creating an Application Project
4. Analyzing the Main Features of Slider
5. Extracting the Original Style Process
6. Analysis of Extracted Source Code
7. Checking Code Behind (GitHub Open Source)
8. OnApplyTemplate in Cross-Platform
9. Concluding the Slider Analysis
10. Creating a Riot-Style Slider (CustomControl) Control
11. Project Creation and Preparation for Start
12. TextBlock (Hi Slider)
13. Adding References and Testing Execution
14. Setting the Size of Riot Slider
15. PART_Track
16. Adding the Slider Bar
17. Aligning the Gap Between Slider Bar and Track
18. PART_SelectionRange
19. Adding Riot-Style Design Elements
20. Implementing a Riot-Style Thumb
21. Declaring Thumb Resources
22. Completing the RiotSlider Template (Finishing Touches)
23. Final Remarks


## WPF Tutorial Series

> To date, four tutorial series have been released on YouTube and BiliBili. These videos are available in English and Chinese, with Korean subtitles on YouTube. We hope these videos, through sophisticated source code and detailed expert explanations, will enhance your understanding of WPF.

- [x] Theme Switch: [Youtube](https://youtu.be/rGox76Bm6VY?si=jTNutyRFctAfTTeS), [BiliBili](https://www.bilibili.com/video/BV1ez4y1N7B8/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5372268/Use-WPF-to-Create-a-Day-and-Night-Theme-Switch-But)

- [x] Riot PlayButton: [Youtube](https://youtu.be/xgUqDavCJGg?si=Ok8qZjNQUKUEbgfT), [BiliBili](https://www.bilibili.com/video/BV1Tu4y1j7Ei/?spm_id_from=333.788.recommend_more_video.-1&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5373396/Creating-a-League-of-Legends-Inspired-Play-Button)

- [x] Magic Navigation Bar: [Youtube](https://youtu.be/dxuLWlukthg?si=CpvQ-TJ2tQu9sY3F), [BiliBili](https://www.bilibili.com/video/BV1Ui4y1a717/?spm_id_from=333.999.0.0&vd_source=f62a1783ac9166a4d798823ad1861fd2), [CodeProject](https://www.codeproject.com/Articles/5375482/Customizing-ListBox-for-a-Smooth-Animated-Navigati)

- [ ] Riot Slider: [BiliBili]()

## Specifications:

This project is based on .NET Core but is designated for Windows only due to the use of WPF. It is executable through VS2022, which is mandatory for running NET 8.0. Alternatively, JetBrains' Rider can also be used.

- [x] OS: Microsoft Windows 11
- [x] IDE: Microsoft Visual Studio 2022
- [x] Version: C# / NET 8.0 / WPF / windows target only
- [x] NuGet: Jamesnet.Wpf

Using the latest version of Windows as your operating system is recommended. However, if you are considering platform expansion to Avalonia UI, Uno Platform, MAUI, etc., it's also worth considering MacOS as a sub-device. We are using Thinkpad/Macbooks as well. Note that Visual Studio is not available on MacOS or Linux-based systems, so Rider is the only alternative. ~~vscode~~

## 3. Creating an Application Project

To get started, you first need to create a WPF Application project.

- [x] Project Type:WPF Application
- [x] Project Name: DemoApp
- [x] Project Version: .NET 8.0

## 4. Analyzing the Main Features of Slider

The WPF Slider control, unlike simpler controls such as Button, has a variety of properties. These properties play crucial functional roles in the control, and some operate in unique ways, making them particularly worthy of attention.

**Orientation:**

Controls in WPF often have a versatile nature, and the Orientation property of the Slider control is a prime example. This property allows for specifying the direction as either horizontal or vertical.

The Orientation property can also be found in the StackPanel control. While the default value of Orientation in StackPanel is Vertical, the default for Slider's Orientation is Horizontal. Thus, it is common to use the Slider in a Horizontal format, which might be why the Orientation feature is not widely known.

Let's take a closer look at a simplified part of the Slider to better understand Orientation:

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
You can see that the (ControlTemplate) template switches based on the Orientation property in the trigger. Thus, a closer look at the actual configuration of this control can easily illustrate the significant role of the Orientation property.

> It's an interesting part. Could you have imagined or applied the concept of switching templates through Orientation before seeing the original source? Open source can inspire in such ways. And let's note that the optimal timing for switching templates is indeed through the "Style.Trigger".

For this tutorial video, we will only implement the Horizontal direction, so we will not perform any branch switching through Orientation. However, you are encouraged to try creating a Vertical version and submit a Pull Request via Fork. Consider it a mission.

Let's also take a look at how the Horizontal/Vertical properties are applied:

- [x] Orientation: **Horizontal**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300" style="float:left"/>

> The SelectionRange (blue) area that will be discussed below is also visible.

- [x] Orientation: **Vertical**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/bbcbeaa9-1763-4435-b92b-be2a71a5ee73" width="300" style="float:left"/>

> Similarly, you will find quite a few controls that switch the (ControlTemplate) template itself in a similar manner (e.g., ScrollViewer).

##### **Minimum, Maximum, and Value:**

These are double type properties that represent the minimum range, maximum range, and value, respectively. Internally, the control's size and ratio calculate the position of the Range and Value automatically based on these values.

Since all these properties are DependencyProperty, dynamic interactions through binding are possible. For example, in an MVVM structure, leveraging these three values allows for dynamic changes to the Range according to specific scenarios or enables interesting implementations through various applications.

##### SelectionStart, SelectionEnd, and IsSelectionRangeEnabled:

These two properties (SelectionStart/SelectionEnd) serve to set a specific area. In reality, this area doesn't include any special functionality; it's merely for designating a segment and visually highlighting it. IsSelectionRangeEnabled is a property that indicates whether this area is active, and depending on its activation status, the area's Visibility property value switches through a trigger (Visible/Collapsed).

Upon examination, these features might seem merely for area marking, leading to questions about their necessity. However, given their versatile use across designs and fields, understanding and anticipating their necessity is possible. ~~Respecting style preferences from 20 years ago~~

Interestingly, applying these with the Value can produce a fascinating effect as shown below:

```xaml
<Slider Orientation="Horizontal"
        Minimum="0"
        Maximum="100"
        Value="30"
        SelectionStart="0"
        SelectionEnd="{Binding Value, RelativeSource={RelativeSource Self}}"
        IsSelectionRangeEnabled="True"/>
```

Surprisingly, linking the Value to SelectionEnd through Binding allows for a dynamic change in the Selection (Range) as the value changes. Was this intended by the WPF developers? It's impressive, and the clean implementation method is quite satisfying.

> This will play a crucial role in the implementation of the Riot-style Slider (CustomControl) discussed later in the article, so keep it in mind.

## 5. Extracting the Original Style Process

As mentioned earlier, since WPF is managed as open-source through the GitHub repository, it's possible to examine the source code of all controls. However, given that the repository contains solutions, all projects, and files, extracting content for a specific control part is a task close to impossible.

Fortunately, Visual Studio provides a GUI feature for extracting the default style (Template) of a specific control. Thus, without the need to sift through open-source, you can easily and simply extract the relevant code.

> It's okay to think of this similar to Identity scaffolding in Blazor. (Though the nature is slightly different, it helps in understanding)

Moreover, extracting the original style through Visual Studio links you to an actual modifiable resource form, allowing for immediate customization of design and functionality. Therefore, since the original style and template extraction is possible not only for Slider but for all controls, this is a highly valuable element in WPF research/learning.

> If you look at commercial components like Infragistics, Syncfusion, ArticPro, not all provide this extraction feature. Each company has its disclosure scope and policy, and most prefer to modularize via DataTemplate for customization rather than exposing the ControlTemplate. It's interesting to take a look at the components you are using.

##### Extraction Method and Procedure: Visual Studio

- [x] Extracting the default control (Slider) style (Edit a Copy...)
- [x] Extract to the current file (This document)
- [x] Extract to the App.xaml file (Application)
- [x] Create a new ResourceDictionary file for extraction (Resource Dictionary)

Note, the extraction process can only proceed in the design area of a Partial UserControl, by selecting the control and right-clicking to proceed. This step involves choosing the "specify style name/define copy location of the extracted style" option.

> Try looking up the method in VScode or Rider, do they offer it?

Let's take a closer look at the process.

- [x] Style extraction command: Slider > Right click > Edit Template > Edit a Copy...

<img width="365" alt="image" src="https://github.com/vickyqu115/riotslider/assets/52397976/0d7f2e38-f616-4260-a256-f3e4eb5c9f09" style="float:left">

> If no extractable style is provided, this item will not be activated.

- [x] Style Extraction Options Window: Create ControlTemplate Resource (Window)

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/d895d1fd-f709-4909-a968-3cd4692550ac" style="float: left; width: 500px"/>


> Select Name (Key) and Define in options,

Typically, specifying a Name is the right choice for testing and management perspectives. If you choose "Apply to all" without specifying a name, the style created based on the Define location will be applied globally. Therefore, understand this point well and proceed with the extraction carefully.

In the video, the name is set, and the Define location is specified as Application. Thus, the extracted resource is included in the Resources area of the App.xaml file (if the file exists).

Personally, when performing such extraction work, it's recommended to proceed in a test nature in a new project. Actually conducting this process in a live project may result in minor mistakes and problems, so it's a good choice also from the perspective of preventing such side effects.

## 6. Analysis of Extracted Source Code

As demonstrated in the tutorial video, the Slider control style has been successfully extracted. Let's take a look at the related resources within the App.xaml file and examine the elements that are important to note one by one.

##### Checking Orientation Branch:

As briefly mentioned when explaining the Orientation property earlier, it's time to check the actual source code implemented.

The style below is the original WPF default style containing the extracted SliderStyle1 template. (It works without errors upon immediate application.)

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
From this, we can see that the default Template is set to the SliderHorizontal (ControlTemplate) template, and through a trigger, it switches to the SliderVertical (ControlTemplate) template when the Orientation property value is Vertical.

> By modularizing the (ControlTemplate) template like this, you gain the advantage of being able to see the actual style at a glance, which is a management structure worth trying even in non-switching situations. I do it often. You can also get inspiration from these aspects.

Thus, the Slider control's functionalities are essentially implemented within both the SliderHorizontal and SliderVertical (ControlTemplate) areas.

Let's now check the default SliderHorizontal (ControlTemplate) template.

##### Checking ControlTemplate:
Let's examine each of the Horizontal/Vertical specific templates, which can be found continuously within the App.xaml file.

 - [x] Check Horizontal specific template
 - [x] Check Vertical specific template

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


As seen, both the Horizontal/Vertical source codes are branched and implemented separately. Therefore, the implemented content is the same for both, differing only in design orientation.

Let's verify this precisely. The common elements included are as follows:

 - [ ] Name: TopTick
 - [ ] Name: BottomTick
 - [ ] Name: TrackBackground
 - [ ] **Name: PART_SelectionRange**
 - [ ] **Name: PART_Track**
 - [ ] Name: Thumb
 - [ ] Trigger: TickPlacement
 - [ ] Trigger: IsSelectionRangeEnabled
 - [ ] Trigger: IsKeyboardFocused


We can see that the common elements are included in both ControlTemplates, confirming that both have the same composition. Now, let's focus on and examine only the SliderHorizontal part.


##### Naming rule: `PART_`

In the structure of (CustomControl) controls, maintaining a tight connection between XAML and Code-behind is crucial. However, connecting them through the GetTemplateChild method to find control names can be visually unappealing. To mitigate this development approach and manage it systematically, the `PART_` naming rule is used.

This rule prefixes all control names found through GetTemplateChild with `PART_`, allowing you to guess the function in XAML. Thus, when analyzing (ControlTemplate) controls, discovering a control named starting with `PART_` suggests it's likely an essential element, and you can anticipate the side effects that might occur if it's removed.

Ultimately, this is immensely helpful in implementing CustomControls. Moreover, this rule is common not only in WPF but also in other cross-platforms sharing XAML, emphasizing its importance.

_Slider contains two `PART_` controls._

- [x] PART_Track
- [x] PART_SelectionRange

Consequently, aside from these two `PART_` controls, the rest are not used in Code-behind, ensured by this naming rule. Therefore, adhering strictly to this rule in CustomControl development is crucial.

##### Test: Check the impact after intentionally changing the name of PART_Track

Let's intentionally change the name of the `PART_Track` control.

```xaml
<Track x:Name="PART_Track1" Grid.Row="1">
    ...
</Track>
```


> Ensure you're in the correct Sliderhorizontal area.

Now, when you run the application, dragging the Track's Thumb will no longer move it left or right, as seen in the tutorial video. The reason the Thumb no longer moves is that the intentional name change prevents Code-behind from finding the PART_Track control through GetTemplateChild.

Since the PART_Track control cannot be found, there's no target for the mouse drag to move. Reverting the name to PART_Track1 will restore functionality.

> This phenomenon can be observed in many other standard controls, notably the TextBox’s PART_ContentHost.

##### Test: Check the impact after intentionally changing the name of PART_SelectionRange
Next, let's intentionally change the name of the PART_SelectionRange control.

```xaml
<Rectangle x:Name="PART_SelectionRange1" .../>
```

