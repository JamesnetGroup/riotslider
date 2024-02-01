Introduction
> M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z

[Pictogrammers(rhombus-split-outline)](https://pictogrammers.com/library/mdi/icon/rhombus-split-outline/)

![WechatIMG12](https://github.com/vickyqu115/riotslider/assets/101777355/b686d831-2de6-410b-949d-52b6b9993873)

## Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control

Among WPF's basic controls such as Button, CheckBox, and ToggleButton, the structure is relatively straightforward, enabling complete implementation through XAML without code-behind. In contrast, controls like TextBox, ComboBox, and Slider necessitate intricate handling through both XAML and C# code. A solid understanding of these controls' fundamental CustomControl structures proves incredibly beneficial when customizing afresh.

This time, we will delve deeply into the internal mechanisms of WPF's standard Slider control. While it's not essential to study the internal structure of every control, it's advantageous to analyze the relevant controls from the GitHub repository when needed, as WPF's source code is openly available there.

Moving forward, we plan to dissect and analyze various controls, not just the Slider. We appreciate your continued interest and support for our GitHub repository, CodeProject articles, and tutorial videos provided on YouTube and BiliBili.

![20240201163433798](https://github.com/vickyqu115/riotslider/assets/101777355/f30773e5-9ad3-4b19-b104-5c026af2577e)


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

> Jamesnet.Wpf library is utilized for employing JamesGrid. This is optional and can be omitted based on preferred libraries and layout configurations.
