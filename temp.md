## WPF 슬라이더 컨트롤의 세부 메커니즘 분석 및 커스터마이징 (Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control)

WPF의 기본 컨트롤 중 Button, CheckBox, ToggleButton과 같은 컨트롤들은 구조적으로 간단하여, Code behind 없이 XAML만으로도 충분히 구현할 수 있는 단순한 컨트롤들입니다. 반면, TextBox, ComboBox, Slider 등과 같은 컨트롤들은 XAML 뿐만 아니라 C# 코드를 통한 복합적인 처리가 필요합니다. 이러한 컨트롤들의 기본 CustomControl 구성을 잘 이해하고 있으면, 새롭게 커스터마이징할 때 매우 유용합니다.

특히, 이번에는 WPF의 기본 Slider 컨트롤을 통해 WPF 컨트롤의 내부 메커니즘을 깊이 있게 이해해볼 예정입니다. 모든 컨트롤의 내부 구조를 일일이 연구할 필요는 없으나, WPF의 소스코드가 GitHub에 오픈소스로 공개되어 있기 때문에, 필요할 때마다 GitHub 레포지터리에서 해당 컨트롤을 찾아 분석하는 것이 좋습니다.

앞으로 Slider 뿐만 아니라 다양한 컨트롤들을 해부하고 분석할 계획입니다. 저희의 GitHub 레포지터리, CodeProject Article, 그리고 YouTube 및 BiliBili에서 제공하는 튜토리얼 영상들에 많은 관심과 지지를 부탁드립니다.



## WPF Tutorial Series

> 현재까지 4개의 튜토리얼 시리즈가 YouTube와 BiliBili를 통해 공개되었습니다. 이 영상들은 영어와 중국어로 제공되며, 유튜브에서는 한글 자막도 지원됩니다. 고급 수준의 소스코드와 상세한 전문적 설명을 통해 WPF의 깊이를 더욱 끌어올릴 수 있는 기회가 되길 바랍니다.

- [x] Theme Switch: [Youtube](), [BiliBili](), [CodeProject]()

- [x] Riot PlayButton: [Youtube](), [BiliBili](), [CodeProject]()

- [x] Magic Navigation Bar: [Youtube](), [BiliBili](), [CodeProject]()

- [ ] Riot Slider: [BiliBili]()



## Specification

이 프로젝트는 닷넷 코어 기반이지만 WPF를 사용하기 때문에 닷넷의 타겟 기반은 Windows 단독으로 지정됩니다. 또한 NET 8.0을 동작하기 위한 필수 비주얼스튜디오 버전인 **VS2022**를 통해 실행됩니다. 또는 JetBrains 사의 Rider를 사용하는 것도 가능합니다.

- [x] OS: Microsoft Windows 11
- [x] IDE: Microsoft Visual Studio 2022
- [x] Version: C# / NET 8.0 / WPF / windows target only
- [x] NuGet: Jamesnet.Wpf 

>  Jamesnet.Wpf 라이브러리를 통해 JamesGrid를 사용합니다. 이는 선택적인 사항이며, 선호하는 라이브러리, 레이아웃 구성 방식에 따라 제외하더라도 상관 없습니다.



### 2. Slider 주요 기능 확인

WPF Slider 컨트롤은 Button과 같은 일반적인 컨트롤과는 달리 다양한 속성들이 존재합니다. 그 중에서도 Slider에서만 동작하는 특별한 의미를 지닌 속성들이 다양하게 제공되는데 주요 속성들은 다음과 같습니다.

**Orientation:** Slider는 WPF에서 제공되는 기본 컨트롤이므로, 범용적인 기능을 제공하는 것에 초점을 두고 있습니다. 따라서 일반적으로는 Horizontal 방향으로 대부분 사용하겠지만, 경우에 따라서는 Vertical 방향으로도 사용해야할 필요가 있을 것입니다. 따라서 이 기본 Template에서는 Orientation 속성을 통해 디자인이 분기되어 사용할 수 있습니다. 단, 이번 프로젝트에서는 Horizontal 방향만 CustomControl으로 구현할 것이기 때문에 Orientation을 통한 분기 작업을 하지는 않을 것입니다.

**Minimum, Maximum:** 이 속성은 선택된 Value 값이 최소 또는 최대 값을 넘지 않도록 설정하는 역할 뿐만이 아니라 전체 길이를 비율로 계산하여 Value 위치를 값으로 변환하기 위해 필수적으로 필요한 기준 값입니다. 일반적으로는 0



- [ ] Orientation 속성 확인 (가로/세로 변경해보기)
- [ ] Minimum(0), Maximum(100) 지정하기 (Slider 길이(Range) 지정하기)
- [ ] Value 값 지정(30)하기 (Minimum/Maximum 값과 비교)
- [ ] SelectionStart(20)/SelectionEnd(60) 값 지정하기
- [ ] IsSelectionRangeEnabled 활성화 (Selection Range 활성화 확인하기)
- [ ] SelectionEnd RelativeSource Binding (Value)

#### 2.1 Slider 주요 속성들이 적용된 소스코드 모습

```xaml
<Slider Orientation="Horizontal"
        Minimum="0"
        Maximum="100"
        Value="30"
        SelectionStart="20"
        SelectionEnd="60"
        IsSelectionRangeEnabled="True"/>
```

```xaml
SelectionEnd="{Binding RelativeSource={RelativeSource Self}, Path=Value}"
```


#### 2.2 Horizontal 모드 (Slider)
> Orientation="Horizontal"

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300"/>

#### 2.3 Vertical 모드 (Slider)
> Orientation="Vertical"

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/bbcbeaa9-1763-4435-b92b-be2a71a5ee73" width="300"/>



### 3. 원본 스타일 추출
- [ ] 기본 컨트롤 (Slider) 스타일 추출하기 (Edit a Copy...)
  - [ ] 현재 파일에 추출 (This document)
  - [ ] App.xaml 파일에 추출 (Application)
  - [ ] 새로운 ResourceDictionary 파일을 생성해서 추출 (Resource Dictionary)
  - [ ] 최종적으로 App.xaml에 추출하도록 한다.



#### 3.1 스타일 추출 과정
> Slider > Right Click > Edit Template > Edit a Copy...

<img width="365" alt="image" src="https://github.com/vickyqu115/riotslider/assets/52397976/0d7f2e38-f616-4260-a256-f3e4eb5c9f09">

_Create ControlTemplate Resource (Window)_  

![image](https://github.com/vickyqu115/riotslider/assets/52397976/d895d1fd-f709-4909-a968-3cd4692550ac)



#### 4. Orientation 분기 확인
- [ ] App.xaml 추출된 리소스에서 Orientation 분기 확인
  - [ ] Template 스위칭 확인
  - [ ] Horizontal 전용 템플릿 확인
  - [ ] Vertical 전용 템플릿 확인

#### 4.1 Slider Style (Template)
> Dotnet WPF에서 제공하는 Slider Original Style
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

### 5. PART_ 컨트롤 확인 
- [ ] PART_Track
- [ ] PART_SelectionRange

#### 6. PART_Track 의도적인 이름 변경 후 영향(Effect) 체크
#### 7. PART_SelectionRange 의도적인 이름 변경 후 영향(Effect) 체크
#### 8. 실제 dotnet/wpf 오픈소스 (GitHub Repo) 확인 
#### 9. 프로젝트 생성: CustomControl
- WPF CustomControl Library: RiotSliderControl
  
#### 10. 기본파일 삭제: 
- AssemblyInfo.cs
- Themes/Generic.xaml
- CustomControl1.cs

#### 11. WPF/CustomControl 파일 생성
- RiotSlider.cs
- AssemblyInfo.cs/Generic.xaml 자동생성 확인

#### 12. 테스트확인을 위한 Template 수정
- TextBlock 넣기= "Riot Slider!!"

#### 13. DemoApp 애플리케이션에서 참조 및 컨트롤 추가
- RiotSlider 참조 추가
- xmlns:riots= 추가
- <riots:RiotSlider/>
- 실행결과: "Riot Slider!!"

#### 14. 컨트롤 사이즈 및 색상 조정
- Width/Height 지정(200/50)
- Background="#EEEEEE"
- 실행결과 확인: 사이즈 색상 확인
  Background="Transparent"

#### 15. PART_Track
- Track 추가
- Thumb 추가
- Ellipse 추가 50X50 Color: #000000
- 실행결과: Thumb 움직임 확인

#### 16. Slider 바 추가
- Border 추가: Height: 2.5 Background: #CCCCCC
- 실행결과: 바 기준으로 Thumb 움직임 확인

#### 17. Thumb 투명도 조정
- Ellipse #55000000

#### 18. Slider 바 마진 추가
- Ellipse 반지름만큼 양쪽에 Margin 추가: 25 0 25 0

#### 19. SelectionRange 추가
- Border 추가: Height 2.5 Background: #000000

#### 20. SelectionRange 설정
- SelectionStart 지정
- SelectionEnd 지정
- IsSelectionRangeEnabled = true (무용지물)
- Trigger 및 기본을 Collapsed로...

#### 21. PART_ 동작확인
- PART_Track
- PART_SelectionRange

#### 22. 기능 완료
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
                        <Border x:Name="PART_SelectionRange" Background="#000000" Height="2.5" HorizontalAlignment="Left" Margin="25 0 25 0"/>
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

#### 23. 전체 레이아웃 변경
- 핵심은 기존 바를 없애고 열 2개(Auto, *)의 Grid 만든다음 각각 열에 배치
- Margin 25 0 25 0 추가
```
<Grid>
    <Grid Margin="25 0 25 0">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto"/>
            <ColumnDefinition Width="*"/>
        </Grid.ColumnDefinitions>
        <Border Grid.Column="0" x:Name="PART_SelectionRange" Background="#000000" Height="2.5"/>
        <Border Grid.Column="1" Background="#CCCCCC" Height="2.5"/>
    </Grid>
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
```

#### 24. Thumb 스타일 작업


#### 25. 최종소스코드
```
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:james="https://jamesnet.dev/xaml/presentation"
    xmlns:local="clr-namespace:RiotSlider">

    <Geometry x:Key="ThumbPath">
        M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z
    </Geometry>

    <LinearGradientBrush x:Key="ThumbColor" StartPoint="0.5, 0" EndPoint="0.5, 1">
        <GradientStop Color="#B79248" Offset="0"/>
        <GradientStop Color="#997530" Offset="0.5"/>
        <GradientStop Color="#74592B" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="ThumbOver" StartPoint="0.5, 0" EndPoint="0.5, 1">
        <GradientStop Color="#EDE1C8" Offset="0"/>
        <GradientStop Color="#DCC088" Offset="0.5"/>
        <GradientStop Color="#CBA14A" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="ThumbDrag" StartPoint="0.5, 0" EndPoint="0.5, 1">
        <GradientStop Color="#473814" Offset="0"/>
        <GradientStop Color="#57421B" Offset="0.5"/>
        <GradientStop Color="#684E23" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="SliderOver" StartPoint="0, 0.5" EndPoint="1, 0.5">
        <GradientStop Color="#795B28" Offset="0"/>
        <GradientStop Color="#C1963B" Offset="0.5"/>
        <GradientStop Color="#C8AA6D" Offset="1"/>
    </LinearGradientBrush>

    <LinearGradientBrush x:Key="SliderDrag" StartPoint="0, 0.5" EndPoint="1, 0.5">
        <GradientStop Color="#685524" Offset="0"/>
        <GradientStop Color="#55441B" Offset="0.5"/>
        <GradientStop Color="#463714" Offset="1"/>
    </LinearGradientBrush>

    <SolidColorBrush x:Key="SliderColor" Color="#1E2328"/>

    <LinearGradientBrush x:Key="RangeColor" StartPoint="0, 0.5" EndPoint="1, 0.5">
        <GradientStop Color="#463714" Offset="0"/>
        <GradientStop Color="#58471D" Offset="0.5"/>
        <GradientStop Color="#695625" Offset="1"/>
    </LinearGradientBrush>

    <Style TargetType="{x:Type Thumb}" x:Key="ThumbStyle">
        <Setter Property="Background" Value="#010A13"/>
        <Setter Property="Width" Value="24"/>
        <Setter Property="Height" Value="24"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type Thumb}">
                    <Grid Background="{TemplateBinding Background}">
                        <Path x:Name="path" Data="{StaticResource ThumbPath}" Fill="{StaticResource ThumbColor}"/>
                    </Grid>
                    <ControlTemplate.Triggers>
                        <Trigger Property="IsMouseOver" Value="true">
                            <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbOver}" />
                        </Trigger>
                        <Trigger Property="IsDragging" Value="true">
                            <Setter TargetName="path" Property="Fill" Value="{StaticResource ThumbDrag}" />
                        </Trigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>

    <Thumb x:Key="SliderThumb" Style="{StaticResource ThumbStyle}"/>

    <Style TargetType="{x:Type local:RiotSlider}">
        <Setter Property="IsMoveToPointEnabled" Value="True"/>
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
                        <Grid Background="{TemplateBinding Background}">
                            <james:JamesGrid Rows="*" Columns="Auto, *" Margin="12 0 12 0" Height="2.5">
                                <Border Grid.Column="0" x:Name="PART_SelectionRange" Background="{StaticResource RangeColor}"/>
                                <Border Grid.Column="1" Background="{StaticResource SliderColor}"/>
                            </james:JamesGrid>
                            <Track x:Name="PART_Track" Thumb="{StaticResource SliderThumb}"/>
                        </Grid>
                    </Border>
                    <ControlTemplate.Triggers>
                        <DataTrigger Binding="{Binding ElementName=PART_Track, Path=Thumb.IsMouseOver}" Value="true">
                            <Setter TargetName="PART_SelectionRange" Property="Background" Value="{StaticResource SliderOver}"/>
                        </DataTrigger>
                        <DataTrigger Binding="{Binding ElementName=PART_Track, Path=Thumb.IsDragging}" Value="true">
                            <Setter TargetName="PART_SelectionRange" Property="Background" Value="{StaticResource SliderDrag}"/>
                        </DataTrigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```
