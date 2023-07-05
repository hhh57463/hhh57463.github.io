---
layout: post
title: "Asset Bundle"
excerpt: "유니티 에셋 번들"
categories: [Unity]
tag: [unity, study]
date: 2023-07-04 00:00:01 +0300
---
## 개요
---
회사에 인턴을 가게 되었는데 리소스가 자그마치 50GB가 넘었다. 이에 실장님이 리소스를 프로젝트에 넣지 않고 실행시키게 요청을 하였고 구글링 결과 에셋 번들에 대해 알게 되었다.

## 에셋 번들
---
에셋 번들: 프로젝트에 존재하는 에셋들을 일정한 규칙으로 묶어 정리하는 것

에셋 번들 빌드: 에셋번만 따로 빌드하는 것

`에셋 번들 빌드를 완료한 뒤, 전체 프로젝트를 빌드하면 에셋번들을 제외한 나머지만 빌드가 이뤄지며, 빌드시 용량이 줄어들지만 최초 실행시 곧장 프로그램 사용이 불가하며 에셋 번들을 다운받아야함`

## 에셋 번들의 장점

- 용량: 용량이 줄어들어 용량제한(스토어에 올릴 상황 등)에 자유로움
- 패치: 수정사항이나 추가패치 때마다 재설치 대신 에셋 번들만 추가 다운로드하면 됨

## 에셋 번들 생성
---
![Image](/assets/img/Unity/AssetBundle_1.png)

프로젝트에서 에셋을 설치 후 빌드하여 용량을 살펴보면

![Image](/assets/img/Unity/AssetBundle_2.png)

101MB가 나온다

![Image](/assets/img/Unity/AssetBundle_3.png)

에셋번들을 만들 에셋의 폴더를 지정 후 Inspector 하단의 AssetBundle을 지정해준다.

![Image](/assets/img/Unity/AssetBundle_4.png)

유니티 툴바에 새로운 기능을 만들기 위해 Assets→Editor 디렉토리를 만든 후 기능을 추가할 스크립트를 생성한다.

```csharp
using System.IO;
using UnityEditor;

public class AssetbundleBuildManager
{
    [MenuItem("BundleBuild/AssetBundle Build")]
    public static void AssetBundleBuild()
    {
        string directory = "./Bundle";
        if(!Directory.Exists(directory))
        {
            Directory.CreateDirectory(directory);
        }
        BuildPipeline.BuildAssetBundles(directory, BuildAssetBundleOptions.None,BuildTarget.StandaloneWindows);
		EditorUtility.DisplayDialog("에셋 번들 빌드", "에셋 번들 빌드 완료", "완료");
    }
}
```

빌드 기능을 제작할 것이기에 System.IO 지시문과 유니티 에디터를 수정하기에 UnityEditor를 추가해준다.

유니티의 컴포넌트가 필요없기에 MonoBehaviour도 지워준다

![Image](/assets/img/Unity/AssetBundle_5.png)

`[MenuItem("BundleBuild/AssetBundle Build")]` 에디터 탭에 추가하기 위해 사용하며 BundleBuild라는 탭 아래 AssetBundle Build가 생긴것을 볼 수 있다.

![Image](/assets/img/Unity/AssetBundle_6.png)

Assets폴더 아래 Bundle이라는 디렉토리 안에 저장하며 만약 Bundle 디렉토리가 없을 시 새로 만든다

BuildPipeline.BuildAssetBundles의 매개변수는 총 3가지가 들어가는데 순서는 아래와 같다

1. 경로: 지정한 경로
2. 에셋 번들 옵션: 압축 방식
3. 플랫폼: 사용 플랫폼에 맞춰 적용

![Image](/assets/img/Unity/AssetBundle_7.png)

코드 작성 후 생성된 번들 빌드 버튼을 클릭한 후 조금 기다리면 빌드 완료 창이 나온다.

![Image](/assets/img/Unity/AssetBundle_8.png)

프로젝트 디렉토리에 가면 Bundle 디렉토리가 생성된 것을 볼 수 있다.

![Image](/assets/img/Unity/AssetBundle_9.png)

지정한 에셋 번들 이름의 파일이 생긴것을 볼 수 있는데 이곳에 지정한 리소스가 압축된 것이다.

## 에셋 번들 로드
---
![Image](/assets/img/Unity/AssetBundle_10.png)

에셋 번들을 사용할 것이기에 원래 올려놓았던 프리팹을 삭제후 LoadManager라는 스크립트를 생성하였다.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class LoadManager : MonoBehaviour
{
    IEnumerator Start()
    {
        AssetBundle asset = AssetBundle.LoadFromFile("Bundle/charactor");
        if(asset == null)
        {
            yield break;
        }
        var hero = asset.LoadAsset<GameObject>("Hero");
        var player = Instantiate(hero);
        player.transform.position = Vector3.zero;

				yield return new WaitUntil(() => Input.GetMouseButtonDown(0));
        Destroy(player);
        asset.Unload(true);
    }
}
```

Start를 코루틴으로 변경해준 후 코드를 작성해준다.

`AssetBundle asset = AssetBundle.LoadFromFile("Bundle/charactor");`

에셋 번들이 있는 디렉토리의 주소를 적어주면 된다.

`var hero = asset.LoadAsset<GameObject>("Hero")` 에셋 번들에 존재하는 오브젝트를 GameObject로 저장한다.

```csharp
yield return new WaitUntil(() => Input.GetMouseButtonDown(0));
Destroy(player);
asset.Unload(true);
```

위코드는 불러온 에셋 번들의 메모리를 해제하는 작업으로 예시를 통해 마우스 클릭시 해제되게 하였다.

![Image](/assets/img/Unity/AssetBundle_11.png)

코드 실행시 잘 작동하는 것을 볼 수 있다.

![Image](/assets/img/Unity/AssetBundle_12.png)

빌드 후 용량을 확인해보면 용량이 줄어든 것을 볼 수 있다.

![Image](/assets/img/Unity/AssetBundle_13.png)

하지만 빌드파일을 실행하면 아무것도 일어나지 않는것을 볼 수 있는데, 이는 번들 파일이 없기 때문이다.

![Image](/assets/img/Unity/AssetBundle_14.png)

빌드 디렉토리에 번들 디렉토리를 옮긴 후 실행하면 

![Image](/assets/img/Unity/AssetBundle_15.png)

오브젝트가 잘 생성되는 것을 볼 수 있다.

번들 파일이 저장되는 코드를 작성하면 되는 것이다.

### 출처

[오늘코딩 - 초보자를 위한 에셋번들](https://youtu.be/mC2d94bfI2w)