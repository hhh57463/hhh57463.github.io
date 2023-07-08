---
layout: post
title: "Asset Bundle 다운로드"
excerpt: "AssetBundle 로컬 저장소 저장"
categories: [Unity]
tag: [unity, study]
date: 2023-07-07 00:00:01 +0300
---
## 개요

---

회사에서 에셋 번들 기능이 필요해서 만들었지만 매번 실행마다 에셋 번들을 다운받아야해서 실행 시간이 너무 걸렸다. 이에 서버에서 에셋을 다운받아 로컬 저장소에 저장하는 기능이 필요했다.

## 웹서버 구축

---

우선 서버에서 저장하는 기능을 만들어야하기에 테스트하기위한 서버가 필요했다. 이에 node.js를 사용하여 로컬 웹서버를 구축한 뒤 다운로드 기능을 만들었다.

```jsx
const express = require('express');
const app = express();

app.get('/download', (req, res, next) => {
    res.download('download/charactor');
})

app.listen(3000, () => {
    console.log('서버 실행');
})
```

## UnityWebRequest

---

서버와 통신하기 위해 UnityWebRequest를 사용하였다. 

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;

public class BundleLoader : MonoBehaviour
{
    void Awake()
    {
        StartCoroutine(ResourceDownLoad());
    }

    [SerializeField] AssetBundle bundle;

    IEnumerator ResourceDownLoad()
    {
        UnityWebRequest www = UnityWebRequestAssetBundle.GetAssetBundle("localhost:3000/download");
        yield return www.SendWebRequest();
        if(www.isDone)
        {
            bundle = DownloadHandlerAssetBundle.GetContent(www);
            Debug.Log("번들 로딩 완료");
        }
    }
}
```

로컬 서버에서 에셋 번들을 다운 받아 bundle에 저장하는 기능이다 작동하는지 확인해보면

![Image](/assets/img/Unity/AssetBundle_2_1.png)

잘 작동하는 것을 볼 수 있다.

## 로컬 저장소 저장

---

하지만 우리가 원하는건 서버에서 받아온 에셋 번들을 로컬 저장소에 불러와 실행마다 다운 받는 것이 아닌 처음 실행에만 다운받는 기능이 필요하다.

이에 아래처럼 파일 입출력을 통해 에셋 번들을 다운 받았다.

```csharp
IEnumerator ResourceDownLoad()
    {
        UnityWebRequest www = UnityWebRequest.Get("localhost:3000/download");
        yield return www.SendWebRequest();
        if (www.isDone)
        {
            string assetBundleDirectory = Application.dataPath + "/AssetBundles";

            if (!Directory.Exists(assetBundleDirectory))
            {
                Directory.CreateDirectory(assetBundleDirectory);
            }

            FileStream fs = new FileStream(assetBundleDirectory + "/" + "charactor", System.IO.FileMode.Create);
            fs.Write(www.downloadHandler.data, 0, (int)www.downloadedBytes);
            fs.Close();
        }
    }
```

저장소에서 에셋 번들을 특정 디렉토리에 다운 받는 기능이다. 중간에 조건문은 지정한 디렉토리가 없을시 새로 만드는 기능이다. 

하지만 실행할때마다 에셋 번들을 다운 받는것은 동일하다. 이에 나는 Unity의 PlayerPrefs 기능을 사용했다.

`PlayerPrefs.SetInt("charactor", 1);` 이런 코드를 다운로드가 끝나고 값을 변경해줌으로써 다운로드 받기 전에 해당 변수를 검사하여 값이 다를시 다운로드 받는 것이다.

![Image](/assets/img/Unity/AssetBundle_2_2.png)

콘솔을 확인해보면 다운로드를 건너뛰었다는 것을 알 수 있다.

## 최종 코드

---

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using System.IO;

public class BundleLoader : MonoBehaviour
{
    void Awake()
    {
        if (PlayerPrefs.GetInt("charactor") == 0)
        {
            Debug.Log("번들 다운로드 시작");
            StartCoroutine(ResourceDownLoad());
        }
        else
        {
            Debug.Log("번들 다운로드 건너 뜀");
            StartCoroutine(CreateHero());
        }
    }

    IEnumerator ResourceDownLoad()
    {
        UnityWebRequest www = UnityWebRequest.Get("localhost:3000/download");
        yield return www.SendWebRequest();
        if (www.isDone)
        {
            string assetBundleDirectory = Application.dataPath + "/AssetBundles";

            if (!Directory.Exists(assetBundleDirectory))
            {
                Directory.CreateDirectory(assetBundleDirectory);
            }

            FileStream fs = new FileStream(assetBundleDirectory + "/" + "charactor", System.IO.FileMode.Create);
            fs.Write(www.downloadHandler.data, 0, (int)www.downloadedBytes);
            fs.Close();
            PlayerPrefs.SetInt("charactor", 1);
            StartCoroutine(CreateHero());
        }
    }

    IEnumerator CreateHero()
    {
        AssetBundle asset = AssetBundle.LoadFromFile(Application.dataPath + "/AssetBundles/charactor");
        if (asset == null)
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

### 출처

[베르의 프로그래밍 노트](https://wergia.tistory.com/36)