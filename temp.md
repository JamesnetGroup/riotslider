안녕하세요. 오랜만입니다!

현재 해외 출장 중입니다, 2월에 있을 닷넷 컨퍼런스에 참여하지 못해 많이 아쉽네요.

짬을 내서 이번에 업로드 된 Vicky의 튜토리얼 영상 내용을 (상세하게) 리뷰하는 글을 함께 작성했습니다. 벌써 네 번째 영상이네요. 영상 하나를 만드는데 얼마나 오래 걸리고 ~~개~~고생 하는지, 옆에서 실시간으로 보고 있네요. ~~멘탈 케어~~ 유튜브 정말 아무나 하는게 아닌 것 같단 생각이 듭니다만,

그래도 영상을 통해 많은 분들과 소통하게 되어, 결론적으로는 아주 의미있고 재밌는 도전인 것 같습니다. 또 이런 비하인드 스토리와 근황을 궁금해 하실 분들이 계실까 하여, 짧게나마 이야기 드렸습니다.

그리고 이번 내용은 WPF를 시작하는 분들에게 특별히 권하는 마음을 담아 작성했으니, 기술에 발전에 도움이 되길 바랍니다. 튜토리얼 영상과 글의 전반적인 내용은 크로스플랫폼인 AvaloniaUI, Uno Platform, 그리고 OpenSilver의 기술/설계와도 꽤나 밀접한 관계가 있으니, WPF에 능숙하신 분들도 한번 흥미를 가지고 살펴보시기를 바랍니다. (저는 일단 쓰면서도 재밌다는...)

---



## WPF 슬라이더 컨트롤의 세부 메커니즘 분석 및 Riot 스타일의 커스터마이징 (Analyzing and Customizing the Detailed Mechanisms of WPF Slider Control)

WPF에서 Template을 포함하는 Button 그리고 ToggleButton 등과 같은 기본적인 컨트롤들은 구조적으로나 논리적으로도 매우 간단하게 설계되어 있으며, (별도의 Code behind  처리 없이도) XAML만으로도 충분히 구현 가능한 단순하고 심플한 컨트롤입니다. 한편, 이보다 좀 더 구조화 되어있는 TextBox 그리고 ComboBox, Slider와 같은 컨트롤들을 살펴보면, (XAML 뿐만 아니라) C# 코드를 통한 복잡한 처리를 반드시 필요로 합니다. 

따라서 단순하지 않고  복합적인 컨트롤을 구현할 때 WPF 컨트롤의 세부 구성을 잘 이해하고 있고 있고, 그것을 응용할 수 있다면 더욱 더 우아하고 유연한 CustomControl 설계 및 개발이 가능해집니다. 따라서 이처럼 컨트롤의 근본적인 구성 요소들을 잘 다룰 수 있다면, 자연스레 MVVM 개발 패턴에서의 부족한 부분들을 새로운 시각에서 보완이 가능하고 완성도 높은 WPF 애플리케이션 구현의 길로 이어지게 됩니다.

특히, 이번에는 WPF에서 제공하는 컨트롤인 Slider를 통해 WPF가 컨트롤을 어떻게 설계하고 있는지 내부 메커니즘을 깊이 있게 살펴보고, 이를 세부적으로 분석해볼 것입니다. 물론 WPF의 모든 컨트롤 내부 사정을 일일이 살펴보는 것은 불가능에 가까우며, 그 방대한 소스코드에 파뭍힐 필요는 없습니다. 

왜냐하면 WPF의 모든 소스코드가 GitHub 레포지터리를 통해 오픈소스로 공개 및 관리되고 있기 때문입니다. 따라서 우리는 필요에 따라 언제든지 GitHub 레포지터리에 접근해서 해당 컨트롤을 찾아 분석하는 것이 가능하기 때문에 더 이상 급할 필요가 없습니다. 더 피곤해지긴 했어도, 불평할 필요도 없겠네요.

이 뿐만 아니라, 앞으로도 Slider 컨트롤 이상으로 더 복잡하고 다양한 컨트롤들을 해부하고 분석할 예정입니다. 우리의 GitHub 레포지터리와 CodeProject, 그리고 YouTube와 BiliBili에서 제공하는 튜토리얼 영상까지도 많은 응원과 관심, 그리고 지지를 부탁드립니다.



## Contents

1. WPF Tutorial Series
2. Specification
3. 애플리케이션 프로젝트 생성
4. Slider 주요 기능 분석
5. 원본 스타일 추출 과정
6. 추출된 소스코드 분석
7. Code behind 확인 (GitHub 오픈소스)
8. 크로스플랫폼에서의 OnApplyTemplate
9. Slider 분석을 마치며
10. Riot 스타일의 Slider (CustomControl) 컨트롤 만들기
11. 프로젝트 생성 및 시작 준비
12. TextBlock (Hi Slider)
13. 참조 추가 및 실행 테스트
14. Riot Slider 크기 설정
15. PART_Track
16. 슬라이더 바 추가
17. 슬라이더 바와 Track 간의 오차 간격 맞추기
18. PART_SelectionRange

19. Riot 스타일의 디자인 요소 추가
20. Riot 스타일의 Thumb 구현하기
21. Thumb 리소스 선언
22. RiotSlider 템플릿 전체 완성 (마무리)
23. 마지막 남기는 말



## 1. WPF Tutorial Series

> 현재까지 4개의  튜토리얼  (Vicky) 시리즈가 YouTube와 BiliBili를 통해 공개되었습니다. 이 튜토리얼 영상들은 영어와 중국어로도 제공되며, 유튜브에서는 한글 자막과 함께 지원됩니다. 잘 다듬어진 소스코드와 상세하고 전문적인 설명을 통해 WPF의 깊이를 더욱 끌어올릴 수 있는 기회가 되길 바랍니다.

- [ ] Theme Switch: [Youtube](), [BiliBili](), [CodeProject]()

- [ ] Riot PlayButton: [Youtube](), [BiliBili](), [CodeProject]()

- [ ] Magic Navigation Bar: [Youtube](), [BiliBili](), [CodeProject]()

- [x] Riot Slider: [BiliBili]()



## 2. Specification

WPF플랫폼에 익숙치 않은 경우, 환경이 혼란스러울 수도 있습니다. 이 프로젝트는 **닷넷 8.0** 기반이지만 WPF를 사용하기 때문에 프레임워크 타겟이 Windows **단독으로 한정**되며, 따라서 윈도우 환경에서만 개발 및 실행이 가능합니다. 그리고 IDE는 비주얼스튜디오 또는 JetBrains 회사에서 제공하는 Rider 중 하나를 선택하면 됩니다. 단 비주얼스튜디오의 경우에는 **VS2022** 버전부터 닷넷 8.0 사용이 가능합니다. 따라서 **VS2022** 이하의 버전에서는 자신의 IDE 버전에 맞게 닷넷 버전을 마이그레이션 하여 사용하시기 바랍니다. 또한 마이그레이션이 학습에 큰 영향을 주진 않습니다.

- [x] OS: Microsoft Windows 11
- [x] IDE: Microsoft Visual Studio 2022
- [x] Version: C# / NET 8.0 / WPF / windows target only
- [x] NuGet: Jamesnet.Wpf



운영체제는 최신 윈도우 버전을 사용하는 것을 권장합니다. 다만 Avalonia UI, Uno Platform, MAUI 등으로의 플랫폼 확장을 고려할 경우 서브 디바이스로써 충분히 MacOS 고려하는 것도 좋습니다. 저희 또한 Thinkpad/Macbook을 사용하고 있습니다. 단 MacOS 또는 리눅스 기반에서는 비주얼스튜디오 사용이 불가능하므로 Rider가 유일한 대안이라는 점을 알아두시기를 바랍니다. ~~vscode~~



## 3. 애플리케이션 프로젝트 생성

이 모든 시작을 위해 WPF Application 프로젝트를 생성이 먼저 필요합니다.

- [x] 프로젝트 타입: WPF Application

- [x] 프로젝트 이름: DemoApp

- [x] 프로젝트 버전: 닷넷 8.0



## 4. Slider 주요 기능 분석

WPF Slider 컨트롤은 Button과 같은 단순 컨트롤과는 달리 아주 다양한 속성들이 존재합니다. 특히 이 속성들을 컨트롤의 기능적인 중요한 역할을 담당하기 때문에 관심 있게 살펴볼 필요가 있으며, 그 중에서도 특별하게 동작하는 주요 속성들은 다음과 같습니다.



**Orientation:** 

WPF에서 제공되는 컨트롤은 기본적으로 범용적인 성격을 가지고 있는 경우가 종종 있습니다. 이번 Slider 컨트롤에서도 마찬가지로, Orientation 속성이 바로 그 예입니다. 이 속성을 통해 가로/세로 방향을 지정할 수 있습니다. 

Orientation 속성은 StackPanel 컨트롤에서도 찾아볼 수 있습니다. StackPanel은 Orientation의 기본 값이 Vertical이지만 Slider의 Orientation 기본 값은 Horizontal입니다. 따라서 Sldier를 보통 Horizontal 형식으로 사용하는 것이 일반적이기 때문에 대부분 Orientation 기능을 알지 못했을 수도 있을 것입니다.

이번에는 Orientation의 이해를 돕기위해 의도적으로 간소화한 Slider 일부분을 살펴보겠습니다.

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



Orientation 속성을 기준으로 트리거에서 (ControlTemplate) 템플릿이 스위칭 되는 것을 볼 수 있습니다. 따라서 실제 이 컨트롤의 세부 구성이 어떻게 되어있는지를 한번 쯤 살펴본다면, Orientation 속성이 꽤나 중요한 역할을 하고 있다는 것을 단번에 쉽게 이해할 수 있습니다.

> 재밌는 부분입니다. 원본을 보기전까지는 Orientation을 통해 템플릿을 스위칭하는 상상이나 응용을 할 수 있었을까요? 오픈소스는 이렇게 다양한 영감을 주기도 합니다. 그리고 이 소스코드를 통해 Template을 스위칭하는 최적의 타이밍이 바로 "Style.Trigger"라는 사실도 체크합시다.



이번 튜토리얼 영상에서는 Horizontal 방향만 구현할 예정이기 때문에 Orientation을 통한 분기 스위칭 작업은 하지 구현하지 않습니다. 그렇지만 여러분은 Vertical 방향도 한번 만들어 보고, Fork를 통해 Pull Request 요청을 해보세요. 미션입니다.



그럼 Hrizontal/Vertical 속성이 각각 적용된 모습도 한번 살펴볼까요?

- [x] Orientation: **Horizontal**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/9b8a3528-6a84-4982-aff1-78cd9eb3cdb7" width="300" style="float:left"/>

> 아래서 다룰 SelectionRage (파랑) 영역도 보이네요.



- [x] Orientation: **Vertical**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/bbcbeaa9-1763-4435-b92b-be2a71a5ee73" width="300" style="float:left"/>

> 이처럼 (ControlTemplate) 템플릿 자체를 스위칭하는 비슷한 컨트롤을 더 찾아보면 꽤나 존재합니다. (ScrollViewer 등)



##### **Minimum, Maximum 그리고 Value:** 

이들은 각각 최소범위/최대범위 그리고 값을 나타내는 역할을 하는 double 타입의 속성들입니다. 내부적으로는 이 값들에 의해 컨트롤의 크기와 비율에 비례하여 Range와 Value 값의 위치가 자동으로 계산됩니다. 

그리고 이 속성들이 모두 DependencyProperty로 되어 있기 때문에, 바인딩을 통해 동적인 상호작용도 가능합니다. 예를 들어 MVVM 구조에서 이 세 개의 값을 활용하여 특정 시나리오에 따라 Range를 동적으로 변경하거나 다양한 응용을 통해 재미있는 구현이 가능해집니다.



##### SelectionStart, SelectionEnd 그리고 IsSelectionRangeEnabled:

이 두 속성(SelectionStart/SelectionEnd)은 특별한 영역을 설정하는 역할을 합니다. 사실 이 영역이 특별한 기능을 포함하고 것은 아닙니다. 단지 어느 구간을 지정하고 시각적으로 강조하기 위함입니다. 그리고 IsSelectionRangeEnabled은 이 영역의 활성화 여부를 나타내는 속성입니다. 그리고 이 활성화 여부에 따라 트리거를 통해 영역의 Visibility 속성 값이 스위칭 됩니다. (Visible/Collapsed) 

결과적으로 이 기능들을 살펴보면, 단순한 영역 표시에 불과하기 때문에 굳이 이 기능이 존재해야 하는지에 대해 의문이 듭니다. 하지만 디자인과 분야에 따라 범용성 있게 사용되는 만큼, 그 필요성에 대해 이해해보고 예상해볼 수도 있을 것입니다.  ~~20년전 스타일 취향 존중~~

그런데 사실, 이것을 Value 값과 함께 응용한다면 아주 흥미로운 효과를 나타낼 수 있는데, 바로 아래와 같습니다.

```xaml
<Slider Orientation="Horizontal"
        Minimum="0"
        Maximum="100"
        Value="30"
        SelectionStart="0"
        SelectionEnd="{Binding Value, RelativeSource={RelativeSource Self}"
        IsSelectionRangeEnabled="True"/>
```



놀랍게도, Value값이 SelectionEnd Binding을 통해 연결되어 값이 변결될 때마다 Selection (Range) 범위가 동적으로 변경되는 효과를 얻을 수 있습니다. WPF 개발진이 의도한걸까요? 멋집니다, 구현 방식도 매우 깔끔해서 기분가지 좋아집니다.

> 글 후반부에 있을 Riot 스타일의 Slider (CustomControl)  구현에서 아주 알짜배기 역할을 하게 될 것입니다, 살짝 기억해주세요.




## 5. 원본 스타일 추출 과정

앞서 언급한 것처럼, WPF는 GitHub 레포지토리를 통해 오픈소스로 관리되기 때문에 모든 컨트롤의 소스코드를 살펴볼 수 있습니다. 하지만 레포지터리에는 솔루션을 비롯해 모든 프로젝트 및 파일이 포함되어 있기 때문에, 특정 컨트롤 부분의 내용만 추출하는 것은 불가능에 가까울 정도로 어려운 작업입니다. 

다행이도, 비주얼스튜디오는 특정 컨트롤의 기본 스타일을 (Template) 추출하는 기능을 GUI 형태로 제공합니다. 따라서 오픈소스를 찾아 헤메는 절차 없이도 손쉽고 간단하게 이를 활용한 해당 코드 추출이 가능해집니다.

> 마치 Balzor에서의 Identity 스케폴딩을 떠올리는 것도 괜찮습니다. (성격은 조금 다르지만 이해를 돕기 위해)



또한 비주얼스튜디오를 통해 원본 스타일을 추출하게 되면 실제 수정 가능한 리소스 형태로 연결되기 때문에 디자인과 기능을 바로 커스터마이징하여 사용할 수 있게 됩니다. 따라서 Slider뿐만 아니라 모든 컨트롤의 원본 스타일 및 템플릿 추출이 가능하기 때문에 WPF 연구/학습에 있어 활용 가치가 아주 높은 요소입니다. 

> Infragistics, Syncfusion, ArticPro 같은 상용 컴포넌트를 살펴보면, 이 추출 기능을 무조건 제공하는 것은 아닙니다. 회사마다 공개하는 범위나 정책이 각기 다르며, 대부분의 경우 ControlTemplate을 공개하기보다는 DataTemplate으로 모듈화 하여 커스터마이징할 수 있도록 유도하는 정책을 선호합니다. 따라서 여러분이 사용중인 컴포넌트에 대해서는 흥미롭게 한번 살펴보시기 바랍니다.



##### 추출 방법과 절차: Visual Studio

- [x] 기본 컨트롤 (Slider) 스타일 추출하기 (Edit a Copy...)
- [x] 현재 파일에 추출 (This document)
- [x] App.xaml 파일에 추출 (Application)
- [x] 새로운 ResourceDictionary 파일을 생성해서 추출 (Resource Dictionary)



단, 추출 절차는 Partial 형태의 UserControl 화면의 디자인 영역에서만 진행할 수 있으며, 컨트롤을 선택하고 우측 클릭을 통해 절차를 진행합니다. 이 과정에서 "스타일 이름 지정/추출 스타일의 복사 위치를 지정" 옵션을 선택하는 단계를 거칩니다.

> VScode 또는 Rider에서의 방법도 한번 찾아보세요, 제공하고 있을까요?



과정을 단계적으로 살펴봅시다.

- [x] 스타일 추출 명령: Slider > Right click > Edit Template > Edit a Copy...

<img width="365" alt="image" src="https://github.com/vickyqu115/riotslider/assets/52397976/0d7f2e38-f616-4260-a256-f3e4eb5c9f09" style="float:left">

> 제공되는 추출 스타일이 없는 경우에는 이 항목이 활성화되지 않음,



- [x] 스타일 추출 옵션 창: Create ControlTemplate Resource (Window)

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/d895d1fd-f709-4909-a968-3cd4692550ac" style="float: left; width: 500px"/>

> Name (Key) 그리고 Define in 옵션 선택,



일반적으로는 Name을 지정하는 것이 테스트나 관리적인 측면에서 옳은 선택입니다. 이를 지정하지 않고 "Apply to all" 항목을 선택하여 추출할 경우 Define 항목을 통해 지정한 위치를 기준으로 생성된 스타일이 전역으로 적용됩니다. 따라서 이 점을 제대로 이해하고 신중하게 추출을 진행하도록 합니다.

영상에서는 이름을 설정하고, Define 위치를 Application으로 지정하고 있습니다. 따라서 (파일이 존재하는 경우) App.xaml 파일의 Resources 영역 안에 추출된 리소스가 포함됩니다.

개인적인 의견으로는 이러한 추출 작업을 할 때, 되도록이면 신규 프로젝트에서 테스트 성격으로 진행하는 방식을 권합니다. 실제로 이 과정을 라이브 프로젝트에서 진행할 경우 사소한 실수, 문제들이 생길 수도 있기 때문에 이러한 사이드 이펙트를 방지하는 차원에서도 좋은 선택입니다.



## 6. 추출된 소스코드 분석

튜토리얼 영상에서처럼 Slider 컨트롤의 스타일이 성공적으로 추출되었습니다. App.xaml 파일 안에 관련 리소스들을 확인해보고, 중요하게 눈여겨볼 요소들을 하나씩 확인해봅시다.



##### Orientation 분기 확인:

앞 부분에서 Orientation 속성을 설명할 때 잠깐 트리거와 스위칭에 대해 간략하게 언급했지만, 이번에는 구현되어 있는 실제 소스코드를 확인해볼 차례입니다.

아래 스타일은 추출된 SliderStyle1 이름의 템플릿이 포함된 WPF 기본 스타일 원본입니다. (실제로 에러 없이 바로 적용되어 동작도 합니다.)

```xaml
<Style x:Key="SliderStyle1" TargetType="{x:Type Slider}">
    <Setter Property="Stylus.IsPressAndHoldEnabled" Value="false"/>
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="BorderBrush" Value="Transparent"/>
    <Setter Property="Foreground" 
            Value="{StaticResource SliderThumb.Static.Foreground}"/>
    <Setter Property="Template" Value="{StaticResource SliderHorizontal}"/>
    <Style.Triggers>
        <Trigger Property="Orientation" Value="Vertical">
            <Setter Property="Template" Value="{StaticResource SliderVertical}"/>
        </Trigger>
    </Style.Triggers>
</Style>
```



내용을 살펴보면 기본 Template은 SliderHorizontal (ControlTemplate) 템플릿이 지정되어 있고, 트리거를 통해 Orientation 속성 값이 Vertical일 경우 SliderVertical (ControlTemplate) 템플릿으로 스위칭 되는 부분을 확인할 수 있습니다.

> 이처럼 (ControlTemplate) 템플릿을 모듈화하여 관리하면 실제 스타일의 형태를 한눈에 볼 수 있는 장점이 생깁니다, 꼭 스위칭하지 않는 상황이더라도 한번 해볼법한 관리 구조 방식입니다. 저는 자주 합니다. 이런 부분들을 통해서도 영감을 받을 수 있죠, 



따라서 Slider 컨트롤과 관련한 기능이 실질적으로 SliderHorizontal 그리고 SliderVertical 두 (ControlTemplate) 템플릿 영역에 각각 구현되어 있습니다.

이제 기본으로 지정되어 있는 SliderHorizontal (ControlTemplate) 템플릿을 확인해봅시다. 



##### ControlTemplate 확인:

각각의 Horizontal/Vertical 전용 템플릿을 확인해봅시다. 모두 계속해서 App.xaml 파일 안에서 찾아볼 수 있습니다.

- [x] Horizontal 전용 템플릿 확인
- [x] Vertical 전용 템플릿 확인



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



위와 같이 Horizontal/Vertical 각각의 소스코드가 분기되어 구현되어있는 것을 확인할 수 있습니다. 따라서 구현된 내용은 둘 다 동일하며, 단지 디자인적인 측면에서의 방향만 다를 뿐입니다. 

이를 정확히 확인해봅시다.  공통으로 포함된 요소들은 다음과 같습니다.

- [ ] Name: TopTick
- [ ] Name: BottomTick
- [ ] Name: TrackBackground
- [x] **Name: PART_SelectionRange**
- [x] **Name: PART_Track**
- [ ] Name: Thumb
- [ ] Trigger: TickPlacement
- [ ] Trigger: IsSelectionRangeEnabled
- [ ] Trigger: IsKeyboardFocused



공통으로 포함된 요소들이 각각의 ControlTemplate 모두에 포함된 것을 확인할 수 있습니다. 둘 다 동일한 구성이라는 것을 확인했으니, 이제는 SliderHorizontal 부분만 집중해서 살펴봅시다.



##### Naming rule: `PART_`

(CustomControl) 컨트롤 구조에서는 XAML과 Code behind간의 연결을 긴밀하게 가져가는 것이 매우 중요한 요소입니다. 하지만 연결을 하기 위해서는 GetTemplateChild 메서드를 통해 컨트롤 이름을 찾기 때문에 가독성 측면에서 좋지 않아보입니다. 이러한 개발 방식을 보완하고 체계적으로 관리하기 위해  `PART_` 네이밍 규칙을 사용합니다. 

이는 GetTemplateChild를 통해 찾는 모든 컨트롤 이름에 `PART_`를 접두어가 붙어, XAML 상에서의 기능을 짐작할 수 있도록 하기 위한 네이밍 규칙입니다. 따라서 (ControlTemplate) 컨트롤을 분석할 때, `PART_`로 시작하는 이름의 컨트롤을 발견한다면, 필수요소일 가능성 짐작이 가능하며 이를 지웠을 때 발생하게 될 사이드 이펙트를 미리 예상하는 것이 가능해집니다. 

결론적으로는 CustomControl 구현에 있어 큰 도움이 됩니다. 또한 이 규칙은 WPF 뿐만 아니라, XAML을 공유하는 다른 크로스플랫폼에서도 흔히 볼 수 있는 공통적인 구조이므로, 높치지 말아야 할 중요한 부분임을 다시 한 번 강조합니다.



_Slider에서는 두 개의 `PART_` 컨트롤이 존재합니다._

- [x] PART_Track
- [x] PART_SelectionRange



결과적으로 위 두 개의 `PART_` 컨트롤 외의 나머지 컨트롤은 Code behind에서 사용되지 않습니다. 이를 네이밍 규칙을 통해 보장하는 것입니다. 따라서 우리도 CustomControl 개발에 있어 이 규칙을 철저하게 지키는 것이 매우 중요합니다.



##### 테스트: PART_Track 의도적인 이름 변경 후 영향 체크

`PART_Track` 컨트롤 이름을 의도적으로 변경해봅시다. 

```xaml
<Track x:Name="PART_Track1" Grid.Row="1">
    ...
</Track>
```

> Sliderhorizontal 영역이 맞는지 잘 확인해보세요.



이제 애플리케이션을 실행하면, 튜토리얼 영상에서처럼 아무리 드래그를 통해 Track의 Thumb를 움직여봐도 더 이상 좌우로 이동하지 않게 됩니다. Thumb가 더 이상 움직이지 않는 이유는 앞서 의도적인 이름 변경으로 인해 Code behind 영역에서 GetTemplateChild를 통해 `PART_Track` 컨트롤을 찾을 찾을 수 없기 때문입니다.

따라서 `PART_Track` 컨트롤을 찾지 못했기 때문에, 마우스 드래그를 통해 움직일 Thumb 대상이 존재하지 않게 된 것입니다. 다시 `PART_Track1` 이름을 원래대로 되돌린다면 기능이 다시 정상으로 돌아올 것입니다. 

> 이런 현상은 다른 많은 기본 컨트롤들에서도 찾아볼 수 잇는데, 대표적으로는 TextBox의 `PART_ContentHost`가 그 중의 하나입니다.



##### 테스트: PART_SelectionRange 의도적인 이름 변경 후 영향 체크

이어서 `PART_SelectionRange` 컨트롤 이름도 의도적으로 변경해봅시다.

```xaml
<Rectangle x:Name="PART_SelectionRange1" .../>
```

> Sliderhorizontal 영역이 맞는지 잘 확인해보세요. (x2)



그리고 트리거 부분을 살펴보면 `PART_SelectionRange`를 사용하는 부분이 더 있기 때문에 이 부분도 함께 변경해야 합니다.

```xaml
<Trigger Property="IsSelectionRangeEnabled" Value="true">
    <Setter Property="Visibility" TargetName="PART_SelectionRange1" Value="Visible"/>
</Trigger>
```

> Sliderhorizontal 영역이 맞는지 잘 확인해보세요. (x3)



그리고 Sldier에서도 아래처럼 `PART_SelectionRange`를 활성화하기 위한 속성들을 모두 설정하도록 합니다.

```xaml
<Slider Style="{DynamicResource SliderStyle1}"
        Minimum="0" Maximum="100"
        SelectionStart="0" SelectionEnd="50"
        IsSelectionRangeEnabled="True"/>
```

> Minimum/Maximum 그리고 SelectionStart/SelectionEnd, IsSelectionRange까지 모두 설정해야 Range 영역을 활성화할 수 있습니다.



- [x] 이름 변경 전: **PART_SelectionRange**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/664d0d18-1fd4-4c70-bb91-b9d655bada3d" style="width:400px; float: left"/>

> 변경 전, 정상적으로 보이는 Rage 영역을 확인할 수 있습니다.



- [x] 이름 변경 후: **PART_SelectionRange1**

<img src="https://github.com/vickyqu115/riotslider/assets/52397976/c1708893-0b3c-47fa-a793-c26d85ad55ca" style="width:400px; float: left"/>

> 이제는 더 이상 Range 영역이 보이지 않습니다.



이번에도 역시 `PART_SelectionRange` 컨트롤을 내부적으로 찾을 수 없기 때문에 Range 영역을 계산할 대상이 없게 된 것입니다.

이처럼 WPF 컨트롤은 생각보다 기능이 느슨하게 구현되어 있으면서 나름의 모듈화 구조를 구성하고 있습니다. 따라서 이러한 특성을 잘 이용한다면, 이미 구현되어 있는 기능을 잘 활용하거나 또는 불필요한 기능을 제외시키는 것도 가능해집니다.



## 7. Code behind 확인 (GitHub 오픈소스)

앞서 `PART_` 컨트롤의 네이밍 규칙과 영향에 대해 자세하게 살펴봤으니, 이번에는 실제 클래스에서 이 컨트롤이 어떻게 사용되는지를 찾아볼 차례입니다.

Code behind (클래스) 영역은 더 이상 추출을 통해 확인해볼 수 있는 영역이 아닙니다. 따라서 WPF 레포지터리를 통해 Official 소스코드를 살펴봐야 합니다. 이를 찾는 방법은 튜토리얼 영상을 통해 더 자세하게 살펴보는 것을 권합니다.



실제 소스코드에서는 각각의 `PART_` 컨트롤 이름이 아래와 같이 `string`으로 약속되어 있습니다.

```csharp
private const string TrackName = "PART_Track";
private const string SelectionRangeElementName = "PART_SelectionRange";
```

> 이름이 고정으로 정의되어 있기 때문에 반드시 지켜야 하는 네이밍 규칙인 것입니다.



##### WPF: OnApplyTemplate

다음은 Track과 SlectionRange를 (ControlTemplate) 템플릿으로부터 가져오는 부분을 살펴봅시다.

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

> (Override) OnApplyTemplate 메서드는 클래스와 스타일이 연결된 후 호출되므로 GetTemplateChild를 사용하기 위한 최적의 시점이라는 것을 알아둡시다.



원본 소스코드를 살펴보면 각각 FrameworkElement와 Track으로 정의되어 있습니다.

- [x] PART_SelectionRange: SelectionRangeElement (FrameworkElement)
- [x] PART_Track: TrackName (Track)



여기서 주목할 점이 있습니다. Track의 경우 XAML과 동일한 타입이지만 SelectionRange는 원본의 Rectangle과는 다른 FrameworkElement로 되어 있는데, 이는 Range 영역을 Rectangle 뿐만 아니라 어떠한 컨트롤을 사용해도 된다는 된다는 의미로 해석해도 무방합니다. 의도적으로 타입이 유연하게 정의되어 있는 것입니다.

그렇다면 (FrameworkElement 타입으로 정의된) SelectionRangeElement는 이 타입에서 다룰 수 있는 기본적인 기능만을 처리할 것으로 예상을 해볼 수 있습니다. 



다음은 실제 SelectionRangeElement를 다루는 부분입니다.

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

>  Orientation을 분기하는 (Horizontal/Vertical) 로직은 실제로 동일하기 때문에 Horizontal을 기준으로만 살펴보면 됩니다.



바로 이 (UpdateSelectionRangeElementPositionAndSize) 메서드를 통해 SelectionRange의 사이즈 및 포지션이 결정됩니다. 소스코드의 양이 다소 부담스럽다고 생각할 수도 있지만 Orientation을 분기하는 로직의 중복된 소스코드를 감안하면 SelectionRange에 대한 간결한 처리가 이루어지고 있음을 쉽게 파악할 수 있습니다.

이처럼 (CustomControl) 컨트롤을 추출하고 `PART_` 컨트롤이 내부에서 어떻게 처리되는지를 이와 같이 역으로 찾아 들어가서 분석이 가능한 것입니다.



## 8. 크로스플랫폼에서의 OnApplyTemplate

WPF의 설계의 많은 부분을 고유하고 있는 크로스플랫폼들 역시 이와 같은 흐름을 그대로 유사하게 따르고 있습니다. 따라서 앞서 분석해본 OnApplyTemplate를 기준으로 다른 플랫폼에서도 한번 이를 살펴봅시다.



OnApplyTemplate 설계를 공유하는 플랫폼 목록

- [x] **AvaloniaUI**
- [x] **Uno Platform**
- [x] **OpenSilver**
- [x] **MAUI**
- [x] **Xamarin**
- [ ] UWP
- [ ] WinUI 3
- [ ] Sliverlight



이 항목 중에서 체크된 AvaloniaUI, Uno Platform, OpenSilver, MAUI, Xamarin의 실제 원본 소스코드를 한번 살펴보겠습니다.

> 참고로 Silverlight 빼고는 모두 GitHub의 Microsoft 공식 Organization인 Dotnet 또는 xamarin을 통해 관리되고 있기 때문에 레포지토리를 GitHub에서 쉽게 찾아볼 수 있습니다.



##### AvaloniaUI: OnApplyTemplate

아래는 AvaloniaUI에서의 Slider 컨트롤의 OnApplyTemplate 부분입니다. 

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

> AvaloniaUI 역시 오픈소스로 관리되고 있기 때문에 WPF처럼 모든 소스코드를 살펴볼 수 있습니다. 또한 WPF와도 매우 비슷한 방식이라는 것을 알 수 있습니다.



이처럼 네이밍 규칙을 통해 세 개의 `PART_` 컨트롤이 XAML영역에서 필수 구성 요소로써 동작한다는 것을 단번에 파악할 수 있습니다. Uno도 살펴볼까요?



##### Uno Platform: OnApplyTemplate

```csharp
protected override void OnApplyTemplate()
{
	...	
    base.OnApplyTemplate(e);
	
	// Get the parts
    var spElementHorizontalTemplateAsDO = GetTemplateChild("HorizontalTemplate");
    _tpElementHorizontalTemplate = spElementHorizontalTemplateAsDO as FrameworkElement;
    var spElementTopTickBarAsDO = GetTemplateChild("TopTickBar");
    ...
}
```

> Uno에서도 마찬가지로 WPF와 유사한 방식입니다.



다만 Uno는 의외로 `PART_` 네이밍 규칙을 따르지 않고 있습니다. 아마도 처음부터 규칙을 사용하지 않는 것을 규칙으로 정한 것 같습니다.

MAUI와 OpenSilver 그리고 Xamarin에서도 이러한 소스코드를 찾아볼 수 있습니다.



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

> WPF에서는 Track과 같이 변수 이름을 선언하지만 MAUI에서는 언더바를 붙이고 있습니다. 각각의 플랫폼마다의 네이밍 규칙과 개발 패턴을 비교해보는 것 또한 오픈소스 분석에 있어 작은 즐거움 중 하나입니다.



##### OpenSilver: OnApplyTemplate

```csharp
public override void OnApplyTemplate()
{
    base.OnApplyTemplate();

    // Get the parts
    ...
    ElementVerticalThumb = GetTemplateChild(ElementVerticalThumbName) as Thumb;
    ...
}
```

> Uno와 비슷한 스타일의 주석을 사용하는군요.



##### Xamarin: OnApplyTemplate

```csharp
public override void OnApplyTemplate()
{
    base.OnApplyTemplate();
    FormsContentControl = Template.FindName("PART_Multi_Content", this) 
    	as FormsTransitioningContentControl;
}
```

> 조금씩은 다르지만 모두 WPF와 같은 설계를 공유하고 있는 것을 확인했습니다.



## 9. Slider 분석을 마치며

WPF Slider 컨트롤을 잘 살펴보았습니다. 이를 통해 우리는 WPF의 (CustomControl) 컨트롤이 매우 정교하게 잘 설계되어 있다는 것을 확인했습니다. 또한 이러한 규칙들은 다른 컨트롤에도 동일하게 응용되며, 또 새로운 컨트롤을 설계하는데에 있어서도 중요한 기반으로 쓰이게 될 것입니다.

누군가는 WPF가 죽었다고 (Is WPF Dead) 표현합니다. 하지만 WPF는 여전히 사라지지 않았으며 계속해서 자리를 지키고 있습니다. WPF를 깊이 있게 다룬다는 것은 계속해서 무긍무진한 가능성과 재미를 가져다 줍니다.

WPF로 모든 개발을 하고 싶다는 꿈이 과거에는 상상으로만 그쳤다면, Xamarin과 닷넷 코어를 시작으로 현재까지 생겨난 다양한 플랫폼들을 통해 이제는 더 이상 꿈이 아닌 현실이 되었습니다. 이는 WPF를 사랑하는 많은 개발자들의 소망과 기여가 모여 만들어진 결과입니다.

지금까지 기본 컨트롤의 분석이 왜 필요한지에 대해 자세하게 살펴봤습니다. 다시 한 번 튜토리얼 영상의 설명을 통해 내용들을 복기하고 학습하는 것을 권합니다.

다음은 이 분석을 기반으로 새로운 Riot 스타일의 (CustomControl) Slider를 만들어보도록 하겠습니다.



## 10. Riot 스타일의 Slider (CustomControl) 컨트롤 만들기

지금부터는 Slider 분석을 토대로 컨트롤의 특성을 살려 최소한의 설계를 통해 이를 구현합니다. 그 과정 속에서 `PART_` 부분을 활용하여 어떻나 코드도 사용하지 않고 컨트롤을 완성하는 것이 이 프로젝트의 핵심입니다. 

지금부터는 구현 과정과 순서를 잘 확인하면서 내용 파악에 집중하면 됩니다. 그리고 만약 CustomControl에 대한 이해를 좀 더 깊이있게 가져가고 싶다면 WPF 저서인 [WPF Inside Out]()을 통해 이를 심도 있게 학습해보는 것을 권합니다.



##### Motivation

아마도 기본 Slider를 그대로 사용하는 경우는 거의 없으리라 봅니다. 그래서 영감이 필요한데, 마침 라이엇게임즈의 League of Legends 게임에서 볼 수 있는 디자인 컨셉의 Slider를 만들어본 경험이 있어 이를 컨트롤의 모티브로 정했습니다.

사실, 이 디자인은 이미 몇년 전 WPF로 높은 수준의 게임 클라이언트를 구현해보고 싶다는 호기심에 시작되었던 "리그오브레전드" 애플리케이션의 일부입니다. 따라서 이 Slider 컨트롤이 실제 어떤 느낌으로 동작하는지 확인해보고 싶다면 [이 레포지터리](https://github.com/jamesnet214/leagueoflegends)를 확인해보시기 바랍니다. 그리고 Fork를 통해 누구나 기여하실 수도 있습니다. 현재까지 80회 이상의 Fork를 기록하고 있습니다.



<img src="https://github.com/vickyqu115/riotslider/assets/52397976/003948fe-70c3-4be4-927f-d0b391ac7b05" style="width:600px; float: left"/>




그럼 지금부터 새로운 (CustomControl) Slider 컨트롤을 창조해봅시다.

  

## 11. 프로젝트 생성 및 시작 준비

앞서 생성한 DemoApp (WPF Application) 애플리케이션 프로젝트에 이어서, 이번에는 CustomControl 라이브러리 프로젝트를 생성할 차례입니다. 만약 DemoApp 프로젝트에서 계속해서 진행하길 원한다면 이번 프로젝트 생성과정은 생략해도 됩니다.



##### 프로젝트 생성:

- [x] 프로젝트 이름: SliderControl
- [x] 프로젝트 타입: WPF CustomControl Library

- [x] 프로젝트 버전: 닷넷 8.0


<img width="253" alt="11-1" src="https://github.com/vickyqu115/riotslider/assets/101777355/eb164539-2bd1-46b1-a658-d339cc39b865">




##### 기본 파일 삭제:

- [x] AssemblyInfo.cs
- [x] Themes/Generic.xaml
- [x] CustomControl1.cs



<img width="243" alt="11-2" src="https://github.com/vickyqu115/riotslider/assets/101777355/ecd698ad-674e-440f-84b4-481caa5b8272">




삭제하는 모든 파일들은 사실 (CustomControl) 컨트롤을 구성하기 위한 필수 파일이지만 위치 또는 프로젝트 구성을 다시 구성하기 위해 삭제하는 것입니다.

> 컨트롤을 다시 생성하는 과정에서 삭제되었던 요소들이 자동으로 다시 생성됩니다. 따라서 파일 삭제에 대해 걱정할 필요 없습니다.



##### (CustomControl) 파일 생성:

- [x] RiotSlider.cs (CustomControl) 클래스 생성


<img width="253" alt="11-3" src="https://github.com/vickyqu115/riotslider/assets/101777355/0c52e698-2353-4e11-8253-0a801de9f119">




CustomControl 클래스 타입으로 파일을 생성할 경우에만 DefaultStyleKeyProperty 관련 구문이 static 생성자와 함께 포함됩니다. 만약 생성과정에서 타입을 잘못 선택할 경우 CustomControl 관련 코드 구문이 누락되기 때문에 이를 직접 입력해야하는 번거로움이 생기므로 과정을 주의깊게 확인하는 것이 중요합니다.

```csharp
public class RiotSlider :Slider
{
    static RiotSlider()
    {
        DefaultStyleKeyProperty.OverrideMetadata(typeof(RiotSlider), new FrameworkPropertyMetadata(typeof(RiotSlider)));
    }
}
```



##### 자동생성된 파일 확인:

- [x] Properties/AssemblyInfo.cs
- [x] Themes/Generic.xaml



<img width="708" alt="11-4" src="https://github.com/vickyqu115/riotslider/assets/101777355/867bdc09-adf2-4852-9aa3-3c4828df6672">




CustomControl 클래스 타입으로 파일을 생성하지 않을 경우, 마찬가지로 해당 파일들이 자동으로 생성되지 않습니다. 이 점도 잘 유의하시기 바랍니다.



## 12. TextBlock (Hi Slider)

다음은 Slider 컨트롤이 CustomControl 형식으로 제대로 구성되었는지 확인하기 위한 테스트 단계입니다.

처음으로 (CustomControl) Slider 컨트롤을 만들게 되면 기본적으로 텅 비어있는 ControlTemplate 템플릿이 생성됩니다. 따라서 이를 시각적으로 확인하기 위해서는 디자인적인 요소를 추가하는 것이 일반적인 방법입니다. 따라서 임시 TextBlock와 함께 텍스트를 추가해보겠습니다.



##### 임시 TextBlock 추가:

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

> 비어있는 ControlTemplate Border 안에 TextBlock과 함께 "Hi Slider" 텍스트를 추가합니다. 추가적으로 폰트 색상도 변경하는 것도 좋습니다. 다양한 방법으로 시도해보세요.



## 13. 참조 추가 및 실행 테스트

테스트를 위한 TextBlock를 준비를 마쳤으니, 이제 DemoApp 애플리케이션을 실행하여 RiotSlider 컨트롤이 제대로 로드되는지를 확인해볼 차례입니다.



##### DemoApp프로젝트에서 참조 추가하기:

- [x] 참조 추가: RiotSliderControl 프로젝트



##### MainWindow.xaml에서 xmlns 선언 및 컨트롤 추가하기:

- [x] xmlns 선언: xmlns:riots

- [x] 컨트롤 삽입: riots:RiotSlider

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



##### 실행결과 확인:

- [x] Riot Slider: "Hi Slider"


<img width="395" alt="11-5" src="https://github.com/vickyqu115/riotslider/assets/101777355/145a7aa7-364d-41a2-a74a-dfe0b95a9e26">




이것으로 (CustomControl) RiotSlider 컨트롤을 구성하고 제대로 실행되는지를 확인하는 단계까지 마쳤습니다.

CustomControl 방식은 UserControl 방식보다 복잡하기 때문이 지금과 같은 과정이 익숙해질 때까지는 어려움이 따를 수 있습니다. 따라서 이를 극복하기 위해서는 반복적인 훈련 과정이 필요합니다. 

이 RiotSlider는 자연스럽게 CustomControl 형태로 모듈화 되어 관리할 수 있게 되었습니다. 이제 이 컨트롤을 GitHub 레포지터리에 올려 관리할 수도 있고 NuGet 패키지 스토어에 업로드하여 배포할 수도 있습니다. 이처럼 WPF에서의 CustomControl 모듈화는 관리적인 측면에서 많은 이점을 가질 수 있기 때문에 이 점을 고려하여 프로젝트를 설계하는 것이 좋습니다.

그리고 이 프로젝트 또한 이미 NuGet Package 스토어를 통해 배포를 해두었습니다. 재미있죠?



## 14. Riot Slider 크기 설정

다음은 컨트롤의 크기를 설정할 차례입니다. 

WPF는 꽤나 강력하고 유연한 (Responsive) 반응형 레이아웃을 사용할 수 있습니다. 따라서 컨트롤의 크기를 지정할 때에도 반응형으로 유연하게 설계하는 것이 일반적입니다. 하지만 예외적인 컨트롤도 있습니다. Slider와 같은 디자인 요소들이 많이 포함되는 경우에는 높이 또는 너비를 고정으로 두어야 자연스러운 디자인을 구성할 수 있기 때문에 절대적인 크기를 지정할 필요가 생길 수 있습니다. 따라서 컨트롤의 특성에 따라 유연하게 대처하는 것이 중요합니다.

이번 컨트롤은 Height 높이를 50 기준으로 (Thumb) 컨트롤을 설계할 것입니다. 따라서 미리 RiotSlider의 높이를 지정해둘 것입니다. 또한 Width 너비는 Track의 이동 경로가 되기 때문에 반응형으로 구현되겠지만 개발 단계에서의 편의상 200으로 제한합니다.



##### 컨트롤 사이즈 및 색상 조정:

- [x] Width: 200

- [x] Height: 50

- [x] Background: "#EEEEEE"

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
        <riots:RiotSlider Width="200" Height="50" Background="#EEEEEE"/>
    </Grid>
</Window>
```

> 컨트롤 크기를 짐작하기 위해 Background 색상도 임시로 함께 변경해두면 컨트롤을 식별하기가 편해집니다. 사소한 꿀팁이죠,



##### 실행결과 확인:

- [x] 컨트롤 크기 확인: Width/Height
- [x] 컨트롤 색상 확인: Background



<img width="591" alt="11-6" src="https://github.com/vickyqu115/riotslider/assets/101777355/6e84759d-cb7c-4582-a9d8-36f05ca2df1f">




실행 결과 확인 후 이상이 없다면 Background 색상을 다시 제거하도록 합시다.



## 15. PART_Track

Track은 Thumb를 포함한 Slider의 핵심 컨트롤 요소입니다. 또한 `PART_Track` 선언을 통해 Slider 컨트롤이 이 기능을 모두 도맡아 처리해주는 것을 분석을 통해 확인할 수 있었습니다. 따라서 이 필수 요소를 적재적소에 잘 포함시키는 것이 이번 구현에서 가장 핵심적이고 중요한 순간이 될 것입니다.

신중하게 살펴봅시다.



##### Track 추가:

- [x] PART_Track 컨트롤 요소 삽입하기

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

> Track은 Control을 건너 뛴 채로 FrameworkElement를 직접 상속받는 몇 안되는 컨트롤 중 하나입니다. 이는 Template과 같이 레이아웃을 직접 설계할 자격이 없는 컨트롤을 의미 합니다. 따라서 Thumb를 내부적으로 포함 시켜 레이아웃을 직접 구성하기 때문에 이 컨트롤은 사실 Thumb에만 집중하면 된다고 생각해도 무방합니다.



##### Thumb 정의:

다음은 Track에서 이동하게 될 Thumb를 정의할 차례입니다.

- [x] Thumb 확장 및 템플릿 정의하기
- [x] Ellipse 구현

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

> Thumb를 Track에서 직접 확장해서 구현한 모습입니다. 문법적인 이해가 어려울 수 있는데, 튜토리얼 영상에서 시각적으로 자세하게 다루고 있으니 한번 시청해보는 것을 권합니다.



이번 Thumb에서는 Track과는 달리 템플릿을 통한 컨트롤 정의가 가능합니다. 이는 Thumb가 FrameworkElement이 아닌 Control으로 부터 상속된 컨트롤이라는 것을 의미합니다. 따라서 Thumb의 ControlTemplate을 통해 컨트롤을 유연하게 설계할 수 있습니다.



##### 실행결과 확인:

- [x] Thumb (Ellipse) 디자인
- [x] Track 이동 기능



<img width="390" alt="11-7" src="https://github.com/vickyqu115/riotslider/assets/101777355/9f13cde2-25a6-47c7-beaf-c54cc5910419">




Thumb를 Ellipse 형태로 구성했기 때문에 이 큼직한 (50x50) 크기의 타원이 Track 영역 안에서 움직이게 됩니다. 하지만 Track의 이름을 `PART_Track`이 아닌 다른 이름으로 변경한다면 Thumb의 움직임을 바로 잃게 될 것입니다. 

> 이러한 상관관계를 다시 한 번 인지하기 위해 이름을 변경해보세요.



## 16. 슬라이더 바 추가

다음은 슬라이더 바를 추가할 차례입니다. 이 작업은 기능과 관련이 없는 오직 디자인적인 요소만을 추가합니다. 따라서 생략해도 기능에는 지장이 없지만, 다음 순서인 SelectionRange 단계와 디자인적 요소를 결합할 필요성이 있기 때문에 이번 작업 또한 주의 깊게 살펴 볼 필요성이 있습니다.



##### 레이아웃 변경:

지금까지는 Border 안에 Track 요소만 포함되어 있었지만, 이번에는 슬라이더 바를 추가해야 하기 때문에 기존의 레이아웃을 변경하는 작업이 필요합니다. 또한 Track과 슬라이더 바는 서로 중첩되는 효과를 가져야하기 때문에 Grid를 사용하는 것이 외길입니다. 따라서 Track을 Grid로 감싸는 작업을 먼저 진행합니다.

- [x] 레이아웃 변경: Grid

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



단순 중첩 효과가 필요하기 때문에, Grid의 RowDefenitions 또는 ColumnDefenitions를 사용할 필요도 없습니다.



##### Track과 중첩되도록 슬라이더 바를 추가:

슬라이더 바는 Track과 중첩되도록 배치하지만 어느 요소가 더 앞으로 (Front) 나와야 하는지를 논리적으로 먼저 생각할 필요가 잇습니다. Track의 Thumb 컨트롤이 슬라이더 바 영역을 덮도록 해야하기 때문에, 이를 고려하여 슬라이더 바를 Track보다 먼저 추가하여 선언하도록 하는 것이 중요합니다.

- [x] 추가: (Border) 슬라이더 바

- [x] Height: 2.5
- [x] Background: #CCCCCC

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



또한 Track의 길이를 시각적으로 표현해야 하기 때문에 Border와 같은 레이아웃을 사용하는 것이 효과적입니다. 특히 Border의 경우에는 CornerRadius 특성을 통해 모서리 부분의 라운딩 처리가 가능하기 때문에 다른 컨트롤에 비해 좀 더 풍부한 디자인을 표현할 수 있다는 장점을 가질 수 있습니다.



##### 실행결과 확인:

- [x] Thumb 움직임 확인: (Ellipse)
- [x] 슬라이더 바 디자인 확인: (Border)



<img width="367" alt="11-8" src="https://github.com/vickyqu115/riotslider/assets/101777355/6e3bc204-c9ad-4bb4-ab7c-2c56c033d6b0">




결과처럼, 슬라이더 바의 디자인과 위치를 Track의 이동 경로와 Thumb의 움직임과 잘 조화되도록 배치하는 것이 이번 단계의 핵심 포인트입니다.



## 17. 슬라이더 바와 Track 간의 오차 간격 맞추기

슬라이더 바의 디자인과 위치가 그럴 듯 하게 배치된 것 같지만, 사실 Track의 이동 범위는 처음과 끝에 각각 Thumb의 반지름 만큼의 영역을 제한하고 있습니다. 실제로 WPF 원본 소스코드를 살펴보면 아래와 같은 코드를 발견할 수 있습니다.

```csharp
Canvas.SetLeft(rangeElement, (thumbSize.Width * 0.5) + Math.Max(Maximum - SelectionEnd, 0) * valueToSize);
```

> 위 소스코드는 Orientation="Horizontal" 기준입니다. 따라서 값이 Vertical 방향으로 변경되면 Height으로 변경됩니다. 맞는지 하번 찾아보세요.



이와 같은 코드에서 알 수 있듯, Track의 실제 이동 범위 또한 내부적으로 ThumbSize의 반지름 만큼을 양쪽으로 제한하고 있다는 것을 유추해볼 수 있습니다. 따라서 우리가 앞서 추가한 슬라이더 바는 Slider 컨트롤 내부에서 관리되는 `PART_` 요소가 아니기 때문에 직접 이 규칙을 적용해야 합니다. 이를 동적으로 처리하는 방법도 있겠지만, 이번 작업에서는 Margin 속성을 통해 슬라이더 바와 Track 이동 범위간의 오차를 정확하게 맞춰보도록 하겠습니다.



##### Thumb Ellipse 투명도 설정:

작업을 좀 더 편하게 하기 위해 Ellipse 컨트롤의 투명도를 지정합니다.

- [x] Ellipse Fill: #55000000

```xaml
<Ellipse Width="50" Height="50" Fill="#55000000"/>
```

> WPF에서 요소의 투명도를 지정할 때 객체 자체의 투명도인 Opacity를 사용하는 방법도 일반적이지만, 색상의 알파 값을 이용한다면 그 특정 색상만 투명화를 적용시킬 수 있기 때문에 훨씬 더 유용하게 사용될 수 있습니다. WPF의 꿀팁 중 하나이니 유용하게 사용해보세요.



##### 슬라이더 바에서 Thumb 반지름 만큼 Margin 적용:

현재 Ellipse의 Width 너비가 50이기 때문에 좌/우 각각 25만큼 씩의 Margin을 적용합니다.

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



##### 결과 확인:

- [x] Thumb 반지름 만큼의 Margin 여백 확인



<사진>



결과처럼 Track의 최대 이동범위와 슬라이더 바의 디자인상 크기가 정확하게 일치한 것을 확인할 수 있습니다.

추가적으로 이번 Sync 작업을 동적으로 처리하는 아이디어에 대해 고민을 해보는 것도 좋습니다. 당장 떠오르는 방법 중에서는 이 슬라이더 바 컨트롤도 `PART_`로 지정한 다음 CodeBehind 내부에서 처리하는 것도 좋습니다. 그 밖에도 다양한 방법이 있으니 한번 생각해보는 시간을 가져보는 것을 기대합니다.



## 18. PART_SelectionRange

SelectionRange는 앞서 Slider 분석을 통해 특정 Range 범위를 지정하는 역할을 하는 요소입니다. 

이 컨트롤 또한 Track과 마찬가지로 `PART_` 요소이며 Slider 컨트롤 내부에서 기능을 모두 처리하기 때문에 약속된 이름으로 잘 배치하기만 하면 됩니다. 그리고 디자인은 슬라이더 바와 동일한 높이의 크기로 지정하는 것이 자연스럽기 때문에 앞서 추가했던 슬라이더 바와 거의 동일한 느낌으로 요소를 추가하면 됩니다.



##### SelectionRange Border 영역 추가:

- [x] Name: `PART_SelectionRange`
- [x] Heigh:t 2.5 
- [x] Background: #000000
- [x] Margin: 25 0 25 0

```xaml
<Border x:Name="PART_SelectionRange" 
        Background="#000000" 
        Height="2.5"
        Margin="25 0 25 0"/>
```



##### Range 범위 지정:

SelectionEnd의 경우 범위를 RelativeSource Binding을 통해 Value 값과 동기화 하도록 합니다.

- [x] SelectionStart: 0
- [x] SelectionEnd: {Binding RelativeSource {RelativeSource Self}, Path=Value}

```xaml
<Setter Property="SelectionStart" Value="0"/>
<Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self}, Path=Value}"/>
```



SelectionEnd의 값을 Value 값과 동기화함으로써 Range 범위를 동적으로 표현할 수 있습니다. 실제로 리그오브레전드 클라이언트 애플리케이션의 Slider 컨트롤도 이와 동일한 형태로 구현되어 있습니다.



##### IsSelectionRangeEnabled 활성화 처리 작업:

Riot Slider 컨트롤의 컨셉을 생각한다면 이번 처리를 굳이 할 필요는 없을지도 모릅니다. 하지만 트리거를 통해 간단하게 처리가 가능한 부분이므로 학습 차원에서 진행해보도록 합시다.

> 튜토리얼 영상에서는 이 부분을 다루고 있지 않습니다.



- [x] IsSelectionRangeEnabled: True

```xaml
<Setter Property="IsSelectionRangeEnabled" Value="True"/>
```

> IsSelectionRangeEnabled 속성의 기본 값을 True로 지정합니다.



- [x] PART_SelectionRange Visibility: (Default) Collapsed

```xaml
<Border x:Name="PART_SelectionRange" 
        Background="#000000" 
        Height="2.5"
        Margin="25 0 25 0"
        Visibility="Collapsed"/>
```

> SelectionRange의 기본 Visibility 값을 Collapsed로 지정합니다.



- [x] 트리거: PART_SelectionRange.Visibility=Visible

```xaml
<Trigger Property="IsSelectionRangeEnabled " Value="True">
    <Setter TargetName="PART_SelectionRange" Property="Visibility" Value="Visible"/>
</Trigger>
```

>SelectionRange의 기본 보이기 값을 Collapsed로 지정하되, IsSelectionRangeEnabled 속성의 값이 True일 때 Visibility 값을 Visible로 변경하도록 트리거를 설정합니다. 이를 반대로 적용할 수 도 있겠지만 트리거에서 Boolean 속성의 True 값을 체크하는 것이 좀 더 일연스러운 일반적인 코드 규칙입니다.



##### 소스코드 및 실행결과 확인:

- [x] Setter 적용
- [x] SelectionRange (Default) Collapsed
- [x] 트리거 적용 IsSelectionRangeEnabled

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
                        <Border x:Name="PART_SelectionRange" 
                                Background="#000000" 
                                Height="2.5"
                                Margin="25 0 25 0"
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
                <ControlTemplate.Rigger>
					<Trigger Property="IsSelectionRangeEnabled" Value="true">
                        <Setter TargetName="PART_SelectionRange" Property="Visibility" Value="Visible"/>
                    </Trigger>
                </ControlTemplate.Rigger>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```



<사진>



이제 Slider를 구성하는 기능상의 모든 요소들을 다 추가하였습니다. 그리고 다시 한 번 `PART_` 컨트롤 요소들의 기능을 점검하는 것으로 이번 순서를 마무리하고 다음 단계로 넘어가보도록 하겠습니다.



##### PART_ 컨트롤 기능 동작을 다시 한 번 확인하기:

- [x] PART_Track
- [x] PART_SelectionRange



### 19. Riot 스타일의 디자인 요소 추가

다음은 Riot Slider에 필요한 디자인 요소들을 추가할 차례입니다.



<img src="https://github.com/vickyqu115/riotslider/assets/52397976/c060395c-03f8-4abf-a630-bf17a2587106" style="width: 600px; float: left"/>



##### Geometry 디자인 리소스 추가:

- [x] Geometry: ThumbData

```xaml
<Geometry x:Key="ThumbData">
    M12 2C11.5 2 11 2.19 10.59 2.59L2.59 10.59C1.8 11.37 1.8 12.63 2.59 13.41L10.59 21.41C11.37 22.2 12.63 22.2 13.41 21.41L21.41 13.41C22.2 12.63 22.2 11.37 21.41 10.59L13.41 2.59C13 2.19 12.5 2 12 2M12 4L15.29 7.29L12 10.59L8.71 7.29L12 4M7.29 8.71L10.59 12L7.29 15.29L4 12L7.29 8.71M16.71 8.71L20 12L16.71 15.29L13.41 12L16.71 8.71M12 13.41L15.29 16.71L12 20L8.71 16.71L12 13.41Z
</Geometry>
```



Thumb 아이콘을 이미지가 파일이 아닌 Geometry Path 요소를 사용하는 이유에 대해서는 이전 컨퍼런스와 영상 등을 통해 여러 번 이야기 했던것 처럼 색상 트리거를 통한 색상 변경이 자유롭고 Vector 기반의 높은 품질을 유지할 수 있는 장점이 있기 때문입니다. 

> 이번과 같은 간단한 수준의 아이콘의 경우에는 비 디자이너의 경우에도 Visual Studio Blend 또는 Figma, Illustrator 등으로 충분히 만들 수도 있습니다. 어렵지 않으니 꼭 한번 도전해보세요.



Vector 기반의 아이콘을 동료에게 요청할 때에는 SVG 타입으로, 그리고 단색의 디자인일 경우에는 결합된 형태로 요구하면 좋습니다. 그리고 이미 오픈소스 진영에서 충분히 많은 아이콘을 무료로 사용할 수도 있습니다. 대표적으로는 [Pictogrammers](https://pictogrammers.com) 오픈소스 팀이 있는데 약 8,000개 이상의 단색 디자인 아이콘을 제공하며 `.SVG` 와 `.PNG`, 심지어는 `.XAML`까지 제공합니다. 또한 재미있는 것은 GitHub를 통해 오픈소스로 관리되고 있기 때문에 주요 기여자를 확인하거나 오픈소스 참여도 가능합니다.



그 다음은 주요 색상 리소스를 추가할 차례입니다. 



##### LinearGradientBrush 디자인 리소스 추가:

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

> 색상과 같은 디자인 리소스의 x:Key 규칙은 보통 대문자 또는 카멜 표기법, 그리고 (.) 네임스페이스와 유사하게 하는 등의 방법이 있습니다. 개인적으로는 해가 바뀔 때마다 이 규칙에 대한 견해가 갈대처럼 변하고 있기 때문에 제 주관을 이야기하기가 망설여지지만 현재는 지금처럼 최대한 짧게 하는 것을 선호합니다. 흘려들어주세요.



리그오브레전드 스타일의 디자인을 유심히 살펴보면 그라데이션을 적극 사용하고 있는 것을 쉽게 파악할 수 있습니다. 이 색상을 추출하는 방법은 Photoshop 또는 스포이드 색상 추출 기능이 포함된 애플리케이션을 이용하는 것입니다. 

> 그라데이션으로 의심되는 색상은 눈대중으로 영역을 나누어 스포이드 기능을 통해 여러 번 색상을 추출해보세요. 자꾸 시도하다 보면 눈썰미도 덩달아 예리해집니다.



<img src="https://github.com/vickyqu115/riotslider/assets/52397976/b7e9fae1-9f8f-4fab-baab-9cfa1c9e013f" style="width: 500px; float: left"/>



## 20. Riot 스타일의 Thumb 구현하기

이제 준비된 Geometry와 디자인 요소를 사용하여 본격적으로 제대로 된 리그오브레전드 스타일의 Thumb 컨트롤을 만들어볼 차례입니다. 

시작하기 전에 앞서 Thumb 템플릿을 정의할 때 임시적으로 Ellipse를 사용하여 구현하였는데 이를 먼저 폐기해야 합니다. 따라서 Ellipse를 포함한 Thumb가 정의된 부분을 모두 삭제하도록 하겠습니다.



##### 기존 Thumb 폐기:

- [x] Thumb 및 템플릿 모두 제거

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

> Track 안에 직접적으로 정의되어 있는 Thumb와 템플릿을 모두 제거하고 Track만 남겨둡니다.



이제 Riot 스타일의 새로운 Thumb를 만들 차례입니다.

방금 제거한 Thumb는 Track을 통해 직접 템플릿을 확장하여 임시로 정의했었지만 이번에는 StaticResource를 통해 리소스를 깔끔하게 정리하는 방식으로 이를 구현할 것입니다. 



##### 새로운 Thumb 템플릿 정의:

- [x] Riot 스타일의 Thumb 구현 및 리소스 세분화

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

> CustomControl 기반에서 XAML 리소스 관리는 의외로 단순합니다. Generic.xaml을 통해 이미 물리적으로 리소스가 나뉘어지기 때문에 계속해서 세부 요소들을 x:Key를 통해 더 세분화하여 관리하면 됩니다. 앞서 같은 이유로 Geometry와 LeanerGradientBrush 또한 분리 시킨 것입니다. 이 리소스는 RiotSlider 컨트롤의 스타일과 같은 `.XAML` 파일안에 포함되어 있기만 하면 됩니다.



Thumb는 앞서 언급했던 것처럼 Control로부터 상속된 컨트롤이므로 템플릿을 통한 (ControlTemplate) 컨트롤 설계가 가능합니다. 따라서 세부적인 트리거까지 상세하게 구현된 또 하나의 컨트롤을 만들 수 있습니다. 또한 더욱 더 디테일한 컨트롤을 만들고자 할 경우 Thumb를 CustomControl 방식으로 더 세분화할 수도 있습니다. 이러한 경우는 WPF 기본 컨트롤에서도 정말 흔히 볼 수 있습니다.

잡지식을 조금 더 탐구해 봅시다. 예로 ToolBarOverflowPanel와 같은 들어도 본적도 없는 이러한 컨트롤들이 찾아보면 상당히 많이 있습니다. 이는 모두 CustomControl 상에서 더 세분화된 컨트롤이 필요할 경우 더 심화된 컨트롤로 만들어 둔 것인데, 이러한 컨트롤들은 대부분 Primitives 네임스페이스로 묶여 있습니다.

따라서 이 네임스페이스로 되어 있는 컨트롤들은 어딘가 다른 (CustomControl) 컨트롤 안에 포함되는 컨트롤이라 생각하면 이해가 쉽습니다. 그럼 Primitives의 대표 주자인 ToggleButton을 한번 예를 들어 보겠습니다. 이 컨트롤은 CheckBox/RadioButton의 부모 역할도 하지만 ComboBox와 같은 컨트롤의 템플릿 안에 포함되어 항목을 스위칭 하는 역할로 사용되기도 합니다.

> 재미있죠? 이러한 아키텍쳐적인 개념들은 XAML을 공유하는 모든 (크로스) 플랫폼들에게도 적용이 됩니다. 따라서 이러한 개념들을 잘 응용할 수 있다면  AvaloniaUI Uno MAUI 등의 환경에서도 다양하게 도움이 될 것입니다.

> 물론 Primitives 네임스페이스로 묶인 컨트롤 모두 DefaultStyleKey를 통해 지정된 CustomControl 방식의 컨트롤임을 의미하는 것은 아닙니다. 이 중에서는 단순 래핑된 클래스들도 많이 존재합니다.



## 21. Thumb 리소스 선언

마지막으로 Thumb를 리소스 형태로 선언을 하여 이를 Track에서 StaticResource로 선언 가능하도록 준비합니다.

##### Thumb 리소스 추가:

- [x] 앞서 템플릿이 포함된 Thumb 스타일을 Thumb 리소스와 함께 연결해서 정의

```xaml
<Thumb x:Key="SliderThumb" Style="{StaticResource ThumbStyle}"/>
```

>  이 부분은 튜토리얼 영상에서도 자세하게 다루고 있으니 문법적으로 어색함이 느껴진다면 한번 살펴보는 것을 권합니다.



이제 리소스화 된 Thumb를 Track에서 사용하기만 하면 됩니다.



##### Track에서 Thumb를 간결하게 정의:

- [x] 기존 Thumb 대신 한 줄로 StaticResource 연결

```xaml
<Track Thumb="{StaticResource SliderThumb}"/>
```

> Thumb 자체를 Resource 형태로 사용하게 되면 Track에서 Thumb를 적용할 때 이처럼 소스코드의 양을 많이 줄일 수 있습니다. 또한 전체적인 리소스를 한눈에 파악하는데 도움이 되기 때문에 이와 같이 리소스를 관리하는 것은 소스코드 품질을 지속적으로 유지하기 위한 중요한 방법 중 하나이기 때문에 이러한 방식을 능숙하게 다룰 수 있도록 유심히 살펴보세요.



### 22. RiotSlider 템플릿 전체 완성 (마무리)

이제 RiotSlider 컨트롤의 템플릿 구현을 마무리합니다. 추가적으로 Jamesnet.WPF 라이브러리도 포함되어 JamesGrid를 사용하고 있는데, 일반 Grid로 대체해도 무방합니다.



##### (CustomControl) RiotSlider:

- [x] Generic.xaml 전체 소스코드 확인

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
        <Setter Property="SelectionEnd" Value="{Binding RelativeSource={RelativeSource Self},Path=Value}"/>
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

> 추가적으로 아래 트리거가 두개 더 추가되었고, RiotSlider 컨트롤의 (ControlTemplate) 템플릿 영역을 한눈에 파악할 수 있도록 모든 요소들을 리소스로 세분화 하여 관리하는 것이 이 프로젝트의 특징입니다.



Slider 컨트롤을 (CustomControl) 기반으로 구현했기 때문에 이와 관련한 리소스들을 하나의 리소스 팩처럼 관리하기에도 용이해집니다.



##### 최종 결과 확인:

- [x] `PART_Track` 관련 기능 테스트
- [x] `PART_SelectionRange` 관련 기능 테스트
- [x] 디자인 요소 적용 확인 

> 기능적인 부분은 이미 분석부터 구현까지 여러 단계를 거치면서 살펴봤지만 다시 한 번 `PART_` 컨트롤을 기준으로 기능 점검을 체크해보시기 바랍니다.



<사진>



이것으로 기본 Slider 컨트롤의 분석부터 리그오브레전드 스타일의 RiotSlider 컨트롤 구현까지 (CustomControl) 기반의 개발 과정 및 튜토리얼 영상 리뷰를 마칩니다. 

> 영상과 다소 다른 부분이 있거나 소스코드 등이 잘못될 수 있습니다. 크게 문제가 있는 부분은 적극 말씀해주세요.



## 23. 마지막 남기는 말

가볍게 만들 수 있는 WPF Slider 컨트롤을 아키텍쳐적인 측면에서 깊이 있게 살펴보았습니다. 뜯어보면 별 것 아닌데 이처럼 할 이야기가 많다는 것은 그만큼 설계적인 측면에서 WPF를 통해 배울 수 있는 부분이 많다는 이야기로 해석할 수도 있을 것 같습니다. 튜토리얼 영상도 한번 살펴보시기 바랍니다. Vicky의 영상을 통한 해석도 재미있습니다.

WPF는 오래된 플랫폼입니다. 따라서 긴 세월 만큼이나 다양한 개발 방법론과 프레임워크, 컴포넌트 오픈소스 라이브러리들이 계속해서 발전하고 또 변화하고 있습니다. 따라서 시간이 지남에 따라 주류의 평가와 해석은 계속해서 달라질 수 있습니다. 그렇기 때문에 지금까지 거쳐온 역사적인 히스토리들은 사실 다방면으로 모두 우리 기술의 밑거름이 될 수 있습니다. 이를 유연하게 판단하고 평가한다면 좀 더 풍부하고 양질의 레퍼런스를 찾아낼 수 있을 것입니다. 꼭 주류만이 정답이 아닐 수 있습니다.



간만에 장문의 리뷰 아닌 리뷰를 만들게 되었는데 많은 분들에게 전해졌음 하는 마음으로 정성담아 작성했습니다. 

모두들 행복한 설 되시길 바랍니다!! 
감사합니다.
