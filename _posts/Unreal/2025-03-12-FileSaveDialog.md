---
layout: post
title: "FileSaveDialog"
excerpt: "언리얼에서 파일 저장 다이얼로그 사용"
categories: [Unreal]
tag: [unreal, cpp, study]
date: 2025-03-12 01:00:00 +0300
---
## 개요

---

언리얼 프로젝트를 진행하던 중 첨부파일을 저장하는 코드를 작성하게 되었다.

## 콘솔 프로젝트와 Unreal 프로젝트

### 콘솔
우선 무작정 C++ 콘솔 프로젝트를 만들어 C++로 기능을 제작해 보았다.

일단 첫번째로 File Save Dialog를 띄우는 기능을 제작하였다.

```c++

#include <windows.h>
#include <shobjidl.h> 

int main() {
    HRESULT hr = CoInitialize(NULL);
    if (SUCCEEDED(hr)) {
        IFileSaveDialog* pFileSave;

        hr = CoCreateInstance(CLSID_FileSaveDialog, NULL, CLSCTX_INPROC_SERVER, IID_PPV_ARGS(&pFileSave));
        if (SUCCEEDED(hr)) {
            pFileSave->Show(NULL);
            pFileSave->Release();
        }

        CoUninitialize();
    }
    return 0;
}
```

Windows API를 사용하기 위해 windows.h와 IFileSaveDialog 인터페이스를 사용하기 위해 shobjidl.h를 include 하였다.

- CoInitialize(NULL): COM(Component Object Model) 라이브러리를 초기화한다.
- CoCreateInstance: 인스턴스 생성 함수
- - CLSID_FileSaveDialog: FileSaveDialog 클래스 ID
- - NULL: IUnknown 인터페이스를 기반으로 객체를 생상할 때 사용
- - CLSCTX_INPROC_SERVER: 같은 프로세스 내에서 COM 객체를 생성한다
- - IID_PPV_ARGS(&pFileSave): IFileSaveDialog 인터페이스의 포인터를 반환 받기 위한 매크로

![image](/assets/img/Unreal/FileDialog_Empty.png)

파일 저장 다이얼로그가 잘 표현된다.

다음으로 특정 URL의 경로를 다운로드 하는 기능을 제작하였다.

```c++
#include <windows.h>
#include <wininet.h>
#include <iostream>
#include <fstream>
#pragma comment(lib, "wininet.lib")

void main() {
    HINTERNET hInternet = InternetOpen(L"WinINet Download", INTERNET_OPEN_TYPE_DIRECT, NULL, NULL, 0);
    if (!hInternet) {
        std::cerr << "InternetOpen failed.\n";
    }

    HINTERNET hFile = InternetOpenUrl(hInternet, L"https://hhh57463.github.io/assets/img/MinTa.png", NULL, 0, INTERNET_FLAG_RELOAD, 0);
    if (!hFile) {
        InternetCloseHandle(hInternet);
    }

    std::ofstream outFile(L"파일 저장 경로\\MinTa.png", std::ios::binary);
    if (!outFile) {
        InternetCloseHandle(hFile);
        InternetCloseHandle(hInternet);
    }

    char buffer[4096];
    DWORD bytesRead;
    while (InternetReadFile(hFile, buffer, sizeof(buffer), &bytesRead) && bytesRead > 0) {
        outFile.write(buffer, bytesRead);
    }

    outFile.close();
    InternetCloseHandle(hFile);
    InternetCloseHandle(hInternet);
}
```

인터넷 관련(HTTP 요청, 파일 다운로드) 기능을 사용하기 위해 wininet.h와 파일 입출력을 위해 fstream을 include 하였다.

`#pragma comment(lib, "wininet.lib"): 라이브러리를 링크하여 WinINet 함수를 사용하도록 설정, 컴파일러에 특정 라이브러리를 링크하드록 지시를 한다.`

- InternetOpen(): 인터넷 연결 초기화 함수
- - L"WinINet DownLoad": 사용자 에이전트 (브라우저 이름과 유사한 역할)
- - INTERNET_OPEN_TYPE: 프록시 서버를 사용하지 않고 직접 연결한다.
- - NULL: 프록시 서버, 사용하지 않으면 NULL
- - NULL: 프록시 우회 주소, 사용하지 않으면 NULL
- - 추가 옵션 플래그: 추가 플래그로 기본 동작은 0

- InternetOpenUrl(): 지정한 URL에서 데이터를 
- - hInternet: 인터넷 핸들
- - URL: 다운로드할 파일 URL
- - NULL: 추가 HTTP 헤더, 헤더가 없으면 NULL
- - 0: 추가 HTTP 헤더 길이, 헤더가 없으면 0
- - INTERNET_FLAG_RELOAD: 캐시 데이터를 사용하지 않고 새로 요청
- - 0: 비동기 컨텍스트로 예제는 동기 모드 사용 비동기모드 사용시 (DWORD_PTR)1234 처럼 1234 값을 식별자로 사용할 수 있음

- outFile(): 파일 저장 준비 함수
- - 파일 저장 경로: 파일 저장 경로
- - std::ofstream: 바이너리 모드로 파일 생성

- InternetReadFile()
- 서버에서 데이터를 읽어와 buffer에 저장
- 읽어온 바이트 수를 bytesRead에 저장
- 데이터 저장이 끝날때까지 반복

![image](/assets/img/Unreal/FileDialog_FileSave.png)

파일이 저장되는 모습을 볼 수 있다.

이후 무작정 언리얼에 올려보기로 생각했다.

### Unreal

### .h

```h
    UFUNCTION(BlueprintCallable, Category = "Windows")
    static bool DownloadFileFromURL(const FString& url, const FString& fileName, const FString& extention);
```

DownloadFileFromURL 함수는 파일을 정상적으로 저장하였는지 검사를 할거기에 bool로 지정하였고, 파일의 경로 url과 파일의 기본 이름 fileName, 확장자 extention을 매개변수로 지정하였다.<br><br><br>

일단... 코드가 많이 변하고 추가됐다...

언리얼에서 shobjidl.h를 사용하려 하니 인식이 안되는 문제가 발생해 버린 것이었다...

그래서 한참을 구글링을 하던 중 언리얼에서 IDesktopPlatform이라는 인터페이스를 지원한다는 것을 알게 됐다.

```c++
#include <wininet.h>
#include <DesktopPlatform/Public/DesktopPlatformModule.h>
#include <DesktopPlatform/Public/IDesktopPlatform.h>
#include <fstream>
#pragma comment(lib, "wininet.lib")

using namespace std;

static wstring ConvertToWString(const FString& str)
{
	return str.GetCharArray().GetData();
}

static FString GetDesktopPath()
{
	TCHAR DesktopPath[MAX_PATH];
	FPlatformMisc::GetEnvironmentVariable(TEXT("USERPROFILE"), DesktopPath, MAX_PATH);
	return FString(DesktopPath) / TEXT("Desktop");
}

static wstring ShowSaveFileDialog(const FString& fileName, const FString& extention)
{
	IDesktopPlatform* DesktopPlatform = FDesktopPlatformModule::Get();
	if (!DesktopPlatform)
	{
		return L"";
	}

	FString DefaultPath = GetDesktopPath();
	FString DefaultFileName = fileName;
	FString FileTypeFilter = extention + TEXT(" Files (*.") + extention + TEXT(")|*.") + extention + TEXT("|All Files (*.*)|*.*");
	TArray<FString> OutFileNames;

	bool bSuccess = DesktopPlatform->SaveFileDialog(
		nullptr,
		TEXT("파일 다운로드"),
		DefaultPath,
		DefaultFileName,
		FileTypeFilter,
		EFileDialogFlags::None,
		OutFileNames
	);

	if (bSuccess && OutFileNames.Num() > 0) {
		return wstring(*OutFileNames[0]);
	}

	return L"";
}

bool UTaskFlash::DownloadFileFromURL(const FString& url, const FString& fileName, const FString& extention)
{
	wstring wUrl = ConvertToWString(url);
	wstring wFilePath = ShowSaveFileDialog(fileName, extention);

	HINTERNET hInternet = InternetOpen(L"WinINet Download", INTERNET_OPEN_TYPE_DIRECT, NULL, NULL, 0);

	if (!hInternet) return false;

	HINTERNET hFile = InternetOpenUrl(hInternet, wUrl.c_str(), NULL, 0, INTERNET_FLAG_RELOAD, 0);
	if (!hFile)
	{
		InternetCloseHandle(hInternet);
		return false;
	}

	ofstream outFile(wFilePath, ios::binary);
	if (!outFile)
	{
		InternetCloseHandle(hFile);
		InternetCloseHandle(hInternet);
		return false;
	}

	char buffer[4096];
	DWORD bytesRead;
	while (InternetReadFile(hFile, buffer, sizeof(buffer), &bytesRead) && bytesRead > 0)
	{
		outFile.write(buffer, bytesRead);
	}

	outFile.close();
	InternetCloseHandle(hFile);
	InternetCloseHandle(hInstance);

	return true;
}
```

wininet을 이용하여 파일을 다운로드 하는 코드는 같으며, 언리얼으로 옮겨오면서 추가된 코드는 다음과 같다.

- ConvertToWString(): FString을 wstring으로 변환해주는 작업을 하는 함수
- GetDesktopPath(): 사용자의 경로는 다를 수 있으므로 사용자들의 바탕화면 경로를 가져오는 함수
- ShowSaveFileDialog(): 파일 저장 대화 상자를 설정 및 보여주는 함수
- DesktopPlatform->SaveFileDialog(): 파일 저장 대화상자를 표시
- -nullptr: 부모 윈도우 핸들, NULL로 지정
- - TEXT("파일 다운로드"): 대화 상자 제목
- - DefaultPath: GetDesktopPath()를 이용하여 바탕화면 경로 지정
- - DefaultFileName: 기본 파일 이름
- - FileTypeFilter: 파일 필터 (저장 가능 파일 확장자 목록)
- - EFileDialogFlags::None: 추가 옵션 플래그
- - OutFileNames: 선택된 파일 경로를 저장할 배열 (OutFileNames[0]을 이용하여 선택된 파일중 첫번째 파일 이름 가져오기 위함)


## 결과
위 코드로 변경한 결과 기존에는 웹 페이지로 이동하여 파일을 다운로드하였지만, 프로그램 내에서 FileSaveDialog를 사용하여 파일을 저장하는 방식으로 변경되었다.