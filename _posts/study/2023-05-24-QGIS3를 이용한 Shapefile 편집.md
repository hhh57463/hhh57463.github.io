---
layout: post
title: "QGIS3를 이용한 Shapefile 편집"
excerpt: "Shapefile edit"
categories: [QGIS]
tag: [QGIS, shapefile]
date: 2023-05-24 00:00:01 +0300
---

## 개요

---

해당 포스트는 국토지리정보원의 정밀 도로지도(NGII)를 예로 들고 있음.<br><br>
[QGIS3 사용법](https://hhh57463.github.io/qgis/2023/05/22/QGIS3-%EC%82%AC%EC%9A%A9%EB%B2%95.html)

## Shapefile 편집

---

### 편집모드

shapefile을 수정할 때는 항상 편집모드가 켜져야한다. 이는 QGIS3의 상단바에 `편집 모드 전환` 을 이용하여 켤 수 있다.

![Image](/assets/img/QGIS3/edit_1.png)

편집 모드를 켜면 특정 기능들을 사용할 수 있게 버튼들이 활성화 된다.

![Image](/assets/img/QGIS3/edit_2.png)

| 아이콘 | 기능 | 설명 |
| --- | --- | --- |
| ![Image](/assets/img/QGIS3/Icon/save.png) | 레이어 편집 내용 저장 | 해당 레이어에서 변경된 점을 저장한다. |
| ![Image](/assets/img/QGIS3/Icon/add.png) | 객체 추가 | 해당 레이어에 맞는 Shape Type을 추가한다. |
| ![Image](/assets/img/QGIS3/Icon/compass.png) | 꼭짓점 도구 | 현재 있는 객체의 꼭짓점을 기준으로 객체를 수정한다. |
| ![Image](/assets/img/QGIS3/Icon/alledit.png) | 선택한 모든 객체의 속성을 동시에 수정 | 선택한 모든 객체를 동일한 값으로 수정할 수 있다. |
| ![Image](/assets/img/QGIS3/Icon/delete.png) | 선택 삭제 | 선택한 객체를 제거한다. |
| ![Image](/assets/img/QGIS3/Icon/cut.png) | 잘라내기 | 선택한 객체를 잘라낸다. |
| ![Image](/assets/img/QGIS3/Icon/copy.png) | 객체 복사 | 선택한 객체를 복사한다. |
| ![Image](/assets/img/QGIS3/Icon/paste.png) | 객체 붙여넣기 | 복사한 객체를 붙여넣는다. |

## 데이터 수정

---

### 데이터 속성값 수정

`이는 .shp를 수정하는 것이 아닌 .dbf의 요소를 수정하는 작업이다.`

편집 모드가 켜진 상태에서 객체를 식별해준다.

![Image](/assets/img/QGIS3/edit_3.png)

식별 결과 탭에 상단에 있는 `객체 양식 편집` 버튼을 선택하여 객체 속성을 수정할 수 있다.

![Image](/assets/img/QGIS3/edit_4.png)

## Shapefile 수정

---

### 데이터 수정

상단 바 중 `영역 또는 단일 클릭으로 객체 선택` 버튼을 선택한다.

![Image](/assets/img/QGIS3/edit_5.png)

![Image](/assets/img/QGIS3/edit_6.png)

해당 상태로 객체를 선택해주면 객체 위에 X표시가 나타난다

상단 탭의 `꼭짓점 도구` 를 선택하여 Drag & Drop 하면 해당 꼭짓점의 객체를 이동시킬 수 있다.

![Image](/assets/img/QGIS3/shapefile_edit_1.gif)

`Point형의 객체를 선택하면 해당 Point를 이동시킬 수 있다.`

만약 PolyLine의 Point(지점)을 제거하고 싶다면 객체를 선택한 후 x표시가 나타난 상태에서 해당 객체를 우클릭하면 `꼭짓점 편집기` 가 켜진다.

![Image](/assets/img/QGIS3/edit_7.png)

꼭짓점 편집기에서 특정 튜플을 선택하면 객체에도 표시가 되는데 이 상태에서 `Delete` 키를 눌러주면 해당 꼭짓점을 삭제할 수 있다.

### 데이터 삽입

편집 모드 전환을 선택한 상태로 상단바의 `객체 추가` 버튼을 선택한다.

![Image](/assets/img/QGIS3/shapefile_edit_2.gif)

마우스 좌클릭으로 꼭짓점을 지정한 후 마우스 우클릭으로 완료해준다.

`현재는 선택한 레이어가 PolyLine Type이기 때문에 꼭짓점을 지정해주어야 한다, 선택한 레이어가 Point Type일 시 Point의 위치를 지정해주면 된다.`

.shp 데이터를 추가했으니 .dbf에 맞는 속성도 정의해 준 후 확인 버튼을 클릭하면 데이터가 추가된다.

![Image](/assets/img/QGIS3/edit_8.png)

해당 객체를 식별 해보면 입력한 속성 값이 들어간 것을 볼 수 있다.

## 데이터 저장

---

모든 편집이 종료되면 레이어 편집 내용 저장을 통해 변경된 내용을 저장해야 한다.

![Image](/assets/img/QGIS3/edit_9.png)