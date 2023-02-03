---
layout: post
title: "[Flutter] - UI"
excerpt: "플러터의 기초 공부"
categories: [Flutter]
tag: [Flutter, Dart]
date: 2023-01-29 00:00:01 +0300
---

## 시작하기 전...
---
![image](/assets/img/Flutter/flutter_UI_1.png){: width="50%" height="50%"}<br>
클래스에 커서를 올려서 확인해보면 nullsafety가 되어있는 필드들은 꼭 필요하지 않다는것 같음.<br>
(근데 나머진 다 디폴트로 들어가있는데…? 아직 잘 모르겠음)<br>

## Color
---
Colors.색상 다음 .을 입력하면 색조를 고를 수 있음 (얼마나 어두워질것인지)<br>
![image](/assets/img/Flutter/flutter_UI_2.png){: width="50%" height="50%"}<br>
Color(0xFF색상코드)로 색상지정이 가능<br>
![image](/assets/img/Flutter/flutter_UI_3.png){: width="50%" height="50%"}<br>
색상.Opacity()로 투명도를 조정할 수 있다<br>
![image](/assets/img/Flutter/flutter_UI_4.png){: width="50%" height="50%"}<br>

## Column
---
자식으로 다른 위젯을 넣으려면 Column 클래스 이용<br>
하나의 Column에 두개의 Text를 넣는 예제<br>
```dart
home: Scaffold(
        backgroundColor: Colors.black,
        body: Column(
          children: [
            Row(
              children: [
              Column(
                  children: [
                    Text(
                      'Hey, Mindol',
                      style: TextStyle(
                        color: Colors.white,
                      ),
                    ),
                    Text(
                      'Welcom Back',
                      style: TextStyle(
                        color: Colors.white,
                        ),
                    ),
                  ],
                )
              ]
            )
          ],
        )
      ),
```
결과<br>
![image](/assets/img/Flutter/flutter_UI_5.png){: width="50%" height="50%"}<br>
너무 위에 있어 아래로 좀 내려야 할듯함 그럴때 사용하는게 SizedBox<br>

## SizedBox
---
크기가 있는 박스, 공간을 만들때 사용
```dart
body: Column(
          children: [
            SizedBox(
              height: 80,
            ),
            Row(
              children: [
              Column(
                  children: [
                    Text(
                      'Hey, Mindol',
                      style: TextStyle(
                        color: Colors.white,
                      ),
                    ),
                    Text(
                      'Welcom Back',
                      style: TextStyle(
                        color: Colors.white,
                        ),
                    ),
                  ],
                )
              ]
            )
          ],
        )
```
Column안에 SizedBox의 높이를 80으로 지정해주고 두개의 Text를 넣어준 예제<br><br>
결과<br>
![image](/assets/img/Flutter/flutter_UI_6.png){: width="50%" height="50%"}<br>
위에 SizedBox가 생긴 후 Text가 그려지는것을 볼 수 있다<br>
이제 텍스트를 오른쪽으로 옮겨보자<br>

## Alignment(정렬)
---
Row의 MainAxisAlignment은 수평방향이지만<br>
Column의 MainAxisAlignment은 수직방향 <br>
```dart
home: Scaffold(
        backgroundColor: Colors.black,
        body: Column(
          children: [
            SizedBox(
              height: 80,
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.end, // <- Row의 정렬 속성 변경
              children: [
              Column(
                  children: [
                    Text(
                      'Hey, Mindol',
                      style: TextStyle(
                        color: Colors.white,
                      ),
                    ),
                    Text(
                      'Welcom Back',
                      style: TextStyle(
                        color: Colors.white,
                        ),
                    ),
                  ],
                )
              ]
            )
          ],
        )
      ),
```
Row클래스의 mainAxisAlignment를 start에서 end로 변경해주었다<br><br>
결과<br>
![image](/assets/img/Flutter/flutter_UI_7.png){: width="50%" height="50%"}<br>
이제 Text의 크기를 변경해줄 것이다<br>

## Text
---
첫번째 텍스트의 크기를 더 크고, 진하게 할 것이다<br>
```dart
body: Column(
          children: [
            SizedBox(
              height: 80,
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.end,
              children: [
              Column(
                  children: [
                    Text(
                      'Hey, Mindol',
                      style: TextStyle(
                        color: Colors.white,
                        fontSize: 38,
                        fontWeight: FontWeight.w600,
                      ),
                    ),
                    Text(
                      'Welcom Back',
                      style: TextStyle(
                        color: Colors.white,
                        ),
                    ),
                  ],
                )
              ]
            )
          ],
        )
```
fontSize로 폰트의 크기를 정해고, fontWeight로 더 진하게 해줄 것이다<br><br>
결과<br>
![image](/assets/img/Flutter/flutter_UI_8.png){: width="50%" height="50%"}<br>
현재 텍스트는 가운데 정렬을 하고있지만 모든 텍스트를 오른쪽에 붙이고 싶다<br>
하지만 Column의 MainAxis는 수직이고, CrossAxis는 수평이다(Row와 반대)<br>
Column 클래스의 crossAxisAlignment값을 CrossAxisAlignment.end로 변경<br><br>
결과<br>
![image](/assets/img/Flutter/flutter_UI_9.png){: width="50%" height="50%"}<br>
잘 정렬되는 것을 볼 수 있다.<br>
하지만 너무 오른쪽에 붙어 있는것 같아 공백을 조금 주고 싶다<br>

## Padding
---
지금까지 만들었던 Column을 Padding으로 감싸서 전체적으로 공백을 주려한다.<br>
padding을 사용할때는 padding을 명시해야하는데,<br>
padding클래스에`padding: EdgeInsets`를 입력하면 알 수 있다.<br>
+ EdgeInsets.all(): 값만큼 화면의 상하좌우 모두 padding을 줌<br>
+ EdgeInsets.only(): 상하좌우 중 한 가지를 구체적으로 정할 수 있음<br>
+ EdgeInsets.symmetric(): 수직 padding과 수평 padding을 지정할 수 있음<br>