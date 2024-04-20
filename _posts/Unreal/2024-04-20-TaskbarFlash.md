---
layout: post
title: "TaskbarFlash"
excerpt: "TaskbarFlash (Icon 깜빡임)"
categories: [Unreal]
tag: [unreal, cpp, study]
date: 2024-04-20 01:00:00 +0300
---
## 개요

---

회사에서 언리얼 프로젝트를 진행하던 중 프로그램이 최소화 상태이거나 사용중이 아닐때 프로그램에 이벤트 발생시 아이콘을 깜빡여야하는 기능을 추가하게 되었다.

### TaskbarFlash
윈도우 작업표시줄의 아이콘을 깜빡여 알림을 주는 효과

## C++

---

Windows.h는 unreal에 포함되어있지 않다. Windows.h 기능을 사용하기 위해서는 모듈 WindowsHRapper.h를 사용해야한다.

참고 - [Third-Party Libraries](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/BuildTools/UnrealBuildTool/ThirdPartyLibraries/)

TaskbarFlash 기능을 개발하기 위해 StartTaskFlaShing 함수를 만들었다.

### .h

```h
    UFUNCTION(BlueprintCallable, Category = "Windows")
    static void StartTaskbarFlashing();
```

### .cpp
```c++
void UTaskFlash::StartTaskbarFlashing()
{
	FLASHWINFO fi;
	fi.cbSize = sizeof(FLASHWINFO);
	fi.hwnd = GetActiveWindow();
	fi.dwFlags = FLASHW_TRAY;
	fi.uCount = 3;
	fi.dwTimeout = 0;
	FlashWindowEx(&fi);
}
```

## 설명
- FLASHWINFO fi: 구조체 인스턴스 생성
- fi.cbSize = sizeof(FLASHWINFO): 구조체 크기 할당
- fi.hwnd = GetActiveWindow(): 현재 활성창의 핸들러를 hwnd에 할당
- fi.dwFlags = FLASHW_TRAY: 깜빡임이 발생할 위치를 작업표시줄로 지정
- fi.uCount = 3: 깜빡임 숫자 지정
- fi.dwTimeout = 0; 깜빡임 시간 지정 (0으로 지정시활성 창으로 변경될 때까지 유지)
- FlashWindowEx(&fi): 깜빡임 동작 실행

`GetActiveWindow(): 현재 활성창 윈도우 핸들러`

위 코드를 작성하여 솔루션 빌드를 진행한 후 블루프린트에 함수를 등록해준다.

이벤트 발생 위치에 해당 블루프린트를 연결해준다.

위 코드처럼 실행하면 프로그램을 사용중이면 Taskbar Flash 기능이 정상 작동하지만 최소화 상태이거나 사용중이지 않을때 기능이 작동하지 않는 문제가 생겼다.

이에 코드를 아래처럼 수정하였다.

### .h

```h
    UFUNCTION(BlueprintCallable, Category = "Windows")
    static void SetHandler();

    UFUNCTION(BlueprintCallable, Category = "Windows")
    static void StartTaskbarFlashing();
```

### .cpp
```c++
HWND this_Handler = nullptr;

void UTaskFlash::SetHandler()
{
	this_Handler = GetActiveWindow();
}

void UTaskFlash::StartTaskbarFlashing()
{
	if (this_Handler != GetActiveWindow())
    {
		FLASHWINFO fi;
		fi.cbSize = sizeof(FLASHWINFO);
		fi.hwnd = this_Handler;
		fi.dwFlags = FLASHW_TRAY;
		fi.uCount = 3;
		fi.dwTimeout = 0;
		FlashWindowEx(&fi);
	}
}
```

SetHander() 함수를 만들어 핸들러를 전역변수에 저장한 뒤 이벤트 발생시 저장한 핸들러가 최상단이 아니면 기능을 작동한다.

## 출처

---

[[SNIPPET] Flashing taskbar icon on Windows with C++](https://www.reddit.com/r/unrealengine/comments/qe84w6/snippet_flashing_taskbar_icon_on_windows_with_c/?rdt=54468)