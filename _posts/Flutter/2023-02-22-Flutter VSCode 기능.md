---
layout: post
title: "[Flutter] - VSCode 편의기능"
excerpt: "코드 자동 수정, 가이드라인, 리팩토링"
categories: [Flutter]
tag: [Flutter, Dart]
date: 2023-02-22 00:00:02 +0300
---

## editor.codeActionsOnSave
---
![image](/assets/img/Flutter/flutter_craft_1.png){: width="50%" height="50%"}<br>
VSCode로 Flutter를 하다보면 코드 아래 밑줄이 생기는 것을 볼 수 있다<br><br>
![image](/assets/img/Flutter/flutter_craft_2.png)<br>
커서를 올려보면 바뀌지 않는 값을 const로 지정해 주라는 뜻이다<br><br>
![image](/assets/img/Flutter/flutter_craft_3.png){: width="50%" height="50%"}<br>
VSCode좌하단 설정버튼→명령 팔레트 클릭 또는 `Ctrl + Shift + P` 후<br><br>
![image](/assets/img/Flutter/flutter_craft_4.png)<br>
`open user Settings`를 입력하면<br><br>
![image](/assets/img/Flutter/flutter_craft_5.png)<br>
settings.json 파일이 열린다<br><br>
![image](/assets/img/Flutter/flutter_craft_6.png)<br>
하단에 `"editor.codeActionsOnSave": {"source.fixAll": true},`를 입력해준다<br><br>
이제 코드 작업을 수행한 후 저장하면 밑줄이 생기던 코드가 자동으로 수정된다.

## dart.previewFlutterUiGuides
---
![image](/assets/img/Flutter/flutter_craft_7.png)<br>
위젯의 부모가 무엇인지 가이드라인을 시각적으로 보여주는 기능<br><br>
![image](/assets/img/Flutter/flutter_craft_8.png)<br>
settings.json파일 하단에 `"dart.previewFlutterUiGuides": true,` 를 입력후 저장하고 VSCode를 껏다 켜주면 적용된다.

## Code Actions
---
Code Actions: 코드를 간단한 방법으로 리팩토링 해주는 기능
![image](/assets/img/Flutter/flutter_craft_9.png){: width="50%" height="50%"}<br>
만약 해당 Column을 Padding으로 감싸고 싶으면 Column을 클릭 후 왼쪽 노란 전구를 클릭해준다.<br><br>
![image](/assets/img/Flutter/flutter_craft_10.png){: width="50%" height="50%"}<br>
Wrap with Padding을 클릭해주면 <br><br>
![image](/assets/img/Flutter/flutter_craft_11.png){: width="50%" height="50%"}<br>
원래라면 복사 붙여넣기 해서 Padding을 추가했다면 클릭 한번으로 Padding으로 감쌀 수 있다.<br><br>
![image](/assets/img/Flutter/flutter_craft_12.png){: width="50%" height="50%"}<br>
Remove this widget을 클릭하면<br><br>
![image](/assets/img/Flutter/flutter_craft_13.png){: width="50%" height="50%"}<br>
해당 위젯이 사라지는것을 볼 수 있다.