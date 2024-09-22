---
layout: post
title: "Launch File"
excerpt: "Content 폴더 내 파일 열기"
categories: [Unreal]
tag: [unreal, cpp, study]
date: 2024-09-22 01:00:00 +0300
---
## 개요

---

회사에서 언리얼 프로젝트를 진행하던 중 실행 파일을 실행하는 코드를 작성하게 되었다.

### .h

```h
    UFUNCTION(BlueprintCallable, Category = "Windows")
    static bool LaunchExeFile(const FString& path);
```

LaunchExeFile 함수는 파일이 있는지 검사를 할거기에 bool로 지정하였고, path 매개변수로 경로를 입력할 것이다.

### .cpp
```c++

bool UTaskFlash::LaunchExeFile(const FString& path) {
	if (FPaths::FileExists(path))
	{
		FPlatformProcess::CreateProc(*path, nullptr, true, 
									 false, false, nullptr, 
									 0, nullptr, nullptr);
		return true;
	}
	else
		return false;
}
```

## 설명
FPaths::FileExists 함수를 이용하여 해당 경로에 파일이 있는지 확인한다.<br>
FPlatformProcess::CreateProc 함수를 이용하여 프로세스를 실행한다.<br>
매개변수의 순서에 따라 설명은 아래와 같다.<br>
1. 파일의 경로
2. 실행하는 프로그램에 전달할 명령줄 인자
3. 독립적으로 실행할지 여부
4. 프로세스를 숨긴 상태로 실행할지 여부
5. 더욱 강력하게 숨길지 여부(true일시 시스템 트레이, 작업관리자에도 숨겨짐)
6. 프로세스의 ID 저장할 변수
7. 프로세스의 우선순위
8. 프로세스가 실행될 디렉터리 지정
9. 프로세스의 표준 입출력 파이프 연결에 사용할 핸들

해당 코드를 작성하고 블루프린트에서 연결한 뒤 실행하였다.<br>
파일 경로는 Get Project Content Directory와 append를 이용하여 지정하였다.<br>
![image](/assets/img/Unreal/Launch%20Exe%20File%20args.png)

## 결과
exe 파일을 하나 만든 뒤 Content 디렉터리에 있는지 확인하였다.
![image](/assets/img/Unreal/launch%20exe%20content.png)

파일이 있을 경우 버튼을 클릭하면 exe 파일이 실행되는 것을 볼 수 있다.
![image](/assets/img/Unreal/exist.gif)

파일이 없는 경우 버튼을 클릭해도 아무런 반응이 없는 것을 볼 수 있다.
![image](/assets/img/Unreal/not%20exist.gif)