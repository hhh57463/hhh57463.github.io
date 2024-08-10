---
layout: post
title: "Window Toast Notification"
excerpt: "Toast Notification (Window 알림)"
categories: [Unreal]
tag: [unreal, cpp, study]
date: 2024-08-10 01:00:00 +0300
---
## 개요

---

회사에서 언리얼 프로젝트를 진행하던 중 프로그램이 최소화 상태이거나 사용중이 아닐때 프로그램에 이벤트 발생시 윈도우 알림으로 내용을 알려주는 기능을 추가하게 되었다.

### Window Toast Notification
윈도우 알림을 나타내는 효과

## C++

---

이전 [TaskbarFlash](https://hhh57463.github.io/unreal/2024/04/19/TaskbarFlash.html)에서 다루었던 내용처럼 Windows.h는 unreal에 포함되어있지 않다. Windows.h 기능을 사용하기 위해서는 모듈 WindowsHRapper.h를 사용해야한다.

참고 - [Third-Party Libraries](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/BuildTools/UnrealBuildTool/ThirdPartyLibraries/)

TaskbarFlash 기능을 개발하기 위해 SendNotification 함수와 알림 클릭시 실행될 이벤트인 ToastActivated Handler 함수를 만들었다.

### .h

```h
    UFUNCTION(BlueprintCallable, Category = "Windows")
    static void StartTaskFlashing();

    UFUNCTION(BlueprintCallable, Category = "Windows")
    static void SendNotification(const FString& msg, const FString& img);
```

SendNotification 함수의 매개변수인 msg와 img에 대한 설명은 아래와 같다.

- const FString& msg: 메세지에 나타낼 내용
- const FString& img: 메세지에 나타낼 이미지 경로

### .cpp
```c++
#include <Windows/WindowsHWrapper.h>
#include <winrt/windows.data.xml.dom.h>
#include <winrt/windows.ui.notifications.h>
#include <winrt/windows.foundation.h>

using namespace winrt::Windows::Data::Xml::Dom;
using namespace winrt::Windows::UI::Notifications;
using namespace winrt::Windows::Foundation;

HWND this_Handler = nullptr;

static void ToastActivatedHandler(ToastNotification const&, IInspectable const&) {
	ShowWindow(this_Handler, SW_RESTORE);
	SetForegroundWindow(this_Handler);
}

void UTaskFlash::SendNotification(const FString& msg, const FString& img) {
	if (this_Handler != GetForegroundWindow()) {
		XmlDocument doc = ToastNotificationManager::GetTemplateContent(ToastTemplateType::ToastImageAndText02);
		doc.SelectSingleNode(L"//text[1]").InnerText(L"Toast Notification");
		doc.SelectSingleNode(L"//text[2]").InnerText(TCHAR_TO_WCHAR(*msg));
		doc.SelectSingleNode(L"//image[1]").Attributes().GetNamedItem(L"src").InnerText(TCHAR_TO_WCHAR(*img));

		ToastNotification toast(doc);
		toast.Activated(ToastActivatedHandler);
		ToastNotificationManager::CreateToastNotifier(L"Toast Notification").Show(toast);
	}
}
```

## 설명
- ToastActivatedHandler: 추후에 설명할 윈도우 알림을 클릭하면 실행할 코드가 담긴 함수
- SendNotification: 해당 함수를 실행하면 윈도우 알림이 오게 되며 알림에 대한 설정을 지정해주는 함수

위 코드를 작성하여 솔루션 빌드를 진행한 후 블루프린트에 함수를 등록해준다.

이벤트 발생 위치에 해당 블루프린트를 연결해준다.

### ToastActivatedHandler
---

윈도우 알림을 클릭하면 실행할 코드로 작성된 내용은 아래와 같다.
`ShowWindow(this_handler, SW_RETORE)`<br>
최소화된 윈도우를 활성화 시켜준다.
`SetForegroundWindow(this_Handler)`<br>
지정된 윈도우 핸들러를 활성 창으로 설정한다.

### SendNotification
---

윈도우 알림을 설정하는 함수로, 매개변수로 들어온 메세지와 이미지 경로를 활용한다.

`XmlDocument doc = ToastNotificationManager::GetTemplateContent(ToastTemplateType::ToastImageAndText02);`<br>
윈도우 알림은 내부적으로 xml 형태를 띄고 있다, 또한 템플릿이 정해져 있으며 위 코드에선 ToastImageAndText02를 사용하였다.

참고 - [ToastTemplateType 열거형](https://learn.microsoft.com/ko-kr/uwp/api/windows.ui.notifications.toasttemplatetype?view=winrt-26100)

`TCHAR_TOWCHAR(const FString&)`<br>
Toast Notification은 winrt를 사용한다, 하지만 winrt에서 사용하는 문자열 HSTRING과 언리얼 문자열은 호환이 되지 않아 형변환이 필요하여 해당 함수를 이용하여 언리얼 FString을 HSTRING 형태로 변환해준다.

`ToastNoitification toast(doc)`<br>
xml 형태로 작성된 Toast Notification을 설정해준다

`toast.Activated(ToastActivatedHandler)`<br>
윈도우 알림이 클릭되면 실행할 함수를 등록한다.

`ToastNotificationManager::CreateToastNotifier(L"Toast Notification").Show(toast)`<br>
특정 App Name과 App Id를 이용해서 해당 앱의 아이콘과 이름을 적용할 수 있다.

## BluePrint
![image](/assets/img/Unreal/TaskFlashingNToastNotification.png)

Construct 이벤트를 활용하여 해당 위젯 초기화가 종료되면 Handler를 지정해준다.

버튼을 클릭하면 3초 뒤 작업표시줄 아이콘이 깜빡 거림과 동시에 Notification을 나타낸다.

`Get Project Content Directory`<br>
현재 프로젝트의 Content 폴더 경로를 가져온다.<br>
패키징시 패키징 폴더내 Content 폴더에 접근한다.

## 결과

![image](/assets/img/Unreal/Notification.gif)

버튼을 클릭하고 프로그램을 최소화 시켜주면 3초뒤 윈도우 알림이 오며, 해당 알림을 클릭하면 프로그램이 활성화 되는것을 볼 수 있다.

## 참고
---
윈도우 시스템 설정 중 알림이 활성화 되있어야한다.

`설정 - 시스템 - 알림 및 작업 - 앱 및 다른 보낸 사람의 알림 받기`를 활성화해준다.