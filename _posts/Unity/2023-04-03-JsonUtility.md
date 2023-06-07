---
layout: post
title: "JsonUtility"
excerpt: "JsonUtility를 이용한 JSON 파싱"
categories: [Unity]
tag: [unity, study]
date: 2023-04-03 00:00:01 +0300
---

# 개요
---
최근 RPG제작에 용이한 기능들을 제작하는 프로젝트 [RPG_Helper](https://github.com/hhh57463/RPG_Helper)를 제작하던 중 인벤토리 구현에 필요한 DB연동 대신 JSON을 활용하던 중 알게된 부분을 정리한다.

## JSON?
---
JavaScript Object Notaion의 약자로 직역하자면 자바 스크립트 객체 표기법으로 데이터를 쉽게 교환 및 저장하기 위한 텍스트 기반의 데이터 교환 표준이다.<br><br>

### JSON 구조
자료형: string, number, true, false, null, object, array<br>
기본적으로 `key: value` 형태의 구조를 가지며, `중괄호 {}`로 감싸게 됨<br>
key 값은 문자열로 이루어져야하며, value에는 지원되는 자료형으로 이루어지면 된다<br><br>

```JSON
{

    "나이" : 25,                      // number
    "이름" : "Mindol",                // string
    "취미" : ["영화감상", "노래방"]   // array
}
```

### JSON 장단점
+ 텍스트 기반으로 다양한 프로그래밍 언어에서 데이터를 읽고 사용이 가능
+ 내용이 함축적으로 최소한의 정보만을 가짐
+ 내용이 함축적이다 보니 의미 파악이 힘들 수 있음

## JsonUtility
---
Unity에서 지원하는 JSON 데이터 작업을 위한 유틸리티 함수로, 데이터를 JSON형태로 저장 및 읽어올 수 있다.

## Example
---
만약 게임이 종료될 때 플레이어의 위치를 저장하고 게임을 재시작하면 저장된 위치를 불러와야하는 기능을 구현하려면 어떻게 해야할까?

## JSON export
---
우선 2D환경에서 CharactorController를 이용한 간단한 이동을 구현했다.
```cs
float h => Input.GetAxis("Horizontal");
float v => Input.GetAxis("Vertical");
void Move()
{
    Vector2 movement = new Vector2(h, v);
    movement *= (moveSpeed * Time.deltaTime);
    controller.Move(movement);
}
```
게임이 종료될 때 위치를 저장해야하니 OnApplicationQuit함수를 이용했다.<br>
```cs
void OnApplicationQuit()
{
    Vector2 savePos = transform.position;
    string path = System.IO.Path.Combine(Application.dataPath +
                                        "/Json/", "playerdata.json");
    System.IO.File.WriteAllText(path, JsonUtility.ToJson(savePos));
}
```
### 코드해석
Vector2 savePos: 플레이어의 월드좌표를 저장하는 변수<br><br>
string path: JSON파일을 생성할 위치지정 Application.dataPath를 이용하여 현재 프로젝트 Assets폴더에 접근 가능하다. 최종적으로 `Assets/Json/playerdata.json`이라는 경로가 지정된다.<br><br>
JsonUtility.ToJson(savePos): JsonUtility.ToJson함수를 이용하여 객체를 JSON문법으로 변환하여 WriteAllText함수로 지정된 경로에 저장하였다.

### JsonUtility.ToJson
매개변수로 들어온 객체를 JSON문법으로 변환해준다. Debug.Log함수를 이용하여 확인해보면<br>
![image](/assets/img/Unity/JsonUtility_1.png)<br>
Vector2형식을 JSON형식으로 변환한 것을 볼 수 있다.

## JSON import
---
이제 반대로 JSON파일에서 불러오는 작업을 수행해야하는데, JSON파일을 읽어오는 함수 LoadJson을 만들어준 후 플레이어 객체가 생성될 때 호출해야하므로 Start함수에서 호출해줬다.
```cs
void LoadJson()
{
    string loadData = System.IO.File.ReadAllText(
                        System.IO.Path.Combine(Application.dataPath + 
                        "/Json/", "playerdata.json"));
    transform.position = JsonUtility.FromJson<Vector2>(loadData);
}
```
### 코드해석
string loadData: playerdata.json을 ReadAllText함수를 이용하여 문자열로 저장해준다.<br><br>
JsonUtility.FromJson<Vector2>(loadData): 읽어온 JSON 데이터를 Vector2자료형으로 반환한다.<br><br>

### JsonUtility.FromJson
매개변수로 읽어온 JSON데이터(문자열)를 지정한 자료형으로 변환하여 반환한다.<br>
`JsonUtility.FromJson<자료형>(문자열);`

### 결과
---
![image](/assets/img/Unity/JsonUtility_2.gif)<br>
실행하여 플레이어를 이동시킨 후 재시작하면 종료했던 위치로 플레이어가 이동하는 것을 볼 수 있다.<br>

### 여러 데이터 JSON화 시키기
---
만약 플레이어의 위치 뿐만 아닌 여러 데이터를 JSON에 저장해야할 경우에는 배열로 저장하여 JSON에 저장해야한다.<br>

[RPG_Helper](https://github.com/hhh57463/RPG_Helper) 제작 중 획득한 아이템 리스트를 JSON에 저장하였는데 아이템의 종류는 장착, 소비, 기타 이렇게 3개의 종류가 있다. 해당 아이템들을 각각의 리스트로 구현하였는데 이를 각자 다른 데이터로 저장해주었다.

아래는 JSON파일에 들어갈 아이템의 구성 요소이다.
```cs
[System.Serializable]           // 직렬화
public class ItemsData
{
    public string name;         // 아이템 이름
    public string info;         // 아이템 정보
    public int count;           // 아이템 갯수
    public string link;         // 아이템 이미지 링크

    // 새로운 아이템을 획득할 때 사용하는 생성자
    public ItemsData(string name, string info, int count, string link)
    {
        this.name = name;
        this.info = info;
        this.count = count;
        this.link = link;
    }
}
```
아래는 아이템을 관리할 ItemDataMng클래스의 구성 요소이다.
```cs
[System.Serializable]
public class ItemDataMng
{
    // 장착 아이템
    public List<ItemsData> equipItem = new List<ItemsData>();
    // 소비 아이템     
    public List<ItemsData> consumItem = new List<ItemsData>();
    // 기타 아이템
    public List<ItemsData> etcItem = new List<ItemsData>();
}
```

아래는 JSON 데이터를 변환하여 저장하는 소스코드들이다.
```cs
TextAsset itemJson;        // JSON 데이터 파일
ItemDataMng itemDatas;     // 아이템을 관리할 변수(인벤토리)
void Start()
{
    // JSON파일을 읽어와 인벤토리에 데이터 저장
    itemDatas = JsonUtility.FromJson<ItemDataMng>(itemJson.text);
}

// 게임 종료시 가지고 있는 아이템 저장
void OnApplicationQuit()
{
    string saveData = JsonUtility.ToJson(itemDatas);
    string fileName = "ItemData.json";
    string path = Application.dataPath + "/Inventory/Json/" + fileName;
    System.IO.File.WriteAllText(path, saveData);
}
```

## 결과 (JSON 파일)
---
![image](/assets/img/Unity/JsonUtility_3.png)<br>
여러 데이터가 JSON 파일에 저장되는 것을 볼 수 있으며,<br><br>
![image](/assets/img/Unity/JsonUtility_4.gif)<br>
각각의 데이터에 맞는 요소들이 인벤토리에 저장되는 것을 볼 수 있다.(이미지 로드가 늦는 이유는 인터넷에서 받아오기에 속도차이가 있다.)

## 주의점
---
만약 JSON파일에서 내용을 읽어와 클래스나 구조체에 저장한다면 JSON의 이름과 해당 데이터를 저장하는 변수의 이름을 같게 지정해줘야한다.<br><br>
해당 포스팅의 내용을 보면 아이템의 종류를 나타내는 `equipItem, cousumItem, etcItem`이 JSON 내부의 데이터 이름과 같을걸 볼 수 있다.<br><br>
만약 서로 이름이 다르면 아래처럼 데이터가 잘 불러와지지 않거나, 에러가 발생한다.<br>
![image](/assets/img/Unity/JsonUtility_5.png)<br>