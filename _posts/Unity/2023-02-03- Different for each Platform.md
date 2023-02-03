---
layout: post
title: "Unity 플랫폼별 구분하여 작업"
excerpt: "Unity 플랫폼 의존 컴파일"
categories: [Unity]
tag: [unity, study]
date: 2023-02-03 00:00:01 +0300
---

## 플랫폼 의존 컴파일
---
유니티를 작업하다보면 플랫폼에 따라 다르게 동작해야하는 경우가 있다.<br>
그럴때는 Unity의 `플랫폼 의존 컴파일` 기능을 이용하여 여러 플랫폼을 대응할 수 있다.<br>

## 플랫폼에 따른 코드 구현
---
```csharp
#if 심볼(플랫폼)
    // 코드 작성
#endif
```
이렇게 코드를 작성해 주면 해당 플랫폼에서만 동작하는 코드를 작성할 수 있다. Unity에 지정되어있는 심볼로 아래가 있다.<br>
![image](/assets/img/Unity/Platform_1.png)<br><br>
[링크-유니티 공식 문서](https://docs.unity3d.com/kr/current/Manual/PlatformDependentCompilation.html)<br>

## 예시
---
![image](/assets/img/Unity/Platform_2.png){: width="50%" height="50%"}<br>
플랫폼에 따라 화면에 보이는 Text가 다르게 변화하도록 코드를 작성한다.

```csharp
    void Start()
    {
#if UNITY_EDITOR                        // Unity Editor
        printText.text = "Platform: Unity Editor";
        Debug.Log("Editor");
#endif
#if UNITY_STANDALONE_WIN                // Window
        printText.text = "Platform: Window";
        Debug.Log("Window");
#endif
#if UNITY_STANDALONE_OSX                // macOS
        printText.text = "Platform: OSX";
        Debug.Log("OSX");
#endif
#if UNITY_IOS                           // IOS
        printText.text = "Platform: IOS";
        Debug.Log("IOS");
#endif
#if UNITY_ANDROID                       // Android
        printText.text = "Platform: Android";
        Debug.Log("Android");
#endif
    }
```

## 실행결과
---
Window로 빌드한 결과 잘 되는것을 볼 수 있다.<br>
![image](/assets/img/Unity/Platform_3.png){: width="50%" height="50%"}<br><br>
하지만 Unity Editor상에서도 똑같은 결과가 나오는 것을 확인 할 수 있다.<br>
![image](/assets/img/Unity/Platform_4.png){: width="50%" height="50%"}<br><br>
그 이유는 Build Setting중 Platform이 Window로 되어있기 때문에, Editor에서 실행되는 코드와 Window에서 실행되는 코드 둘 다 실행되기 때문이다.<br>
콘솔창을 확인해 보면 Editor, Window 둘다 출력되는 것을 볼 수 있다.<br>
![image](/assets/img/Unity/Platform_5.png){: width="50%" height="50%"}<br><br>
Platform을 Android로 변경한 후 다시 실행시켜보면 Android가 출력되는 것을 볼 수 있다.<br>
![image](/assets/img/Unity/Platform_6.png){: width="50%" height="50%"}<br><br>
![image](/assets/img/Unity/Platform_7.png){: width="50%" height="50%"}