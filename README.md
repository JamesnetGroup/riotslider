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
