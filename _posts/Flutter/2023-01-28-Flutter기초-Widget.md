---
layout: post
title: "[Flutter] - Widget"
excerpt: "플러터의 기초 공부"
categories: [Flutter]
tag: [Flutter, Dart]
date: 2023-01-28 00:00:01 +0300
---

# 개요
--------------
Flutter는 게임엔진처럼 동작, 엔진이 화면 상의 모든 픽셀을 그려줌. 게임 엔진은 무언가를 렌더링할 때 호스트 플랫폼을 사용하지 않고 스스로 그래픽을 그림, Flutter도 같음.

## React Native vs Flutter
--------------
React Native: 네이티브 앱 운영체제 상에서 가능한 위젯을 사용하고 싶을 때, 호스트 운영체제가 만들어주는 위젯을 사용하는 크로스플랫폼 어플리케이션을 만들 때<br><br>
Flutter: 세밀한 디자인 요구사항이 들어가며, 요소들이나 애니메이션들을 전부 커스터마이징 해야할 때 (ios, android 앱처럼이 아닌, 자체의 디자인으로 이루어진 어플리케이션을 만들 때)

## Widget
--------------
Widget(위젯)
+ Flutter: 앱의 UI를 만드는 것
+ 프로그래밍: class 생성

Widget을 레고 블럭을 조립한다고 생각, Flutter에 있는 모든 건 Widget, Widget을 합치는 방식으로 앱을 제작해나감.<br><br>
공식 위젯: [flutter Widget 카탈로그](https://docs.flutter.dev/development/ui/widgets)에 가면 애니메이션, 레이아웃 등 여러 위젯을 볼 수 있음.<br><br>
클래스를 생성할때 flutter SDK에 있는 3개의 core Widget중 하나를 상속받아야함.<br>
StatelessWidget: 가장 기초적인 위젯으로 바꿔주는 클래스로 그냥 화면에 무언가 띄어주는 용도

## build Method
--------------
모든 위젯은 build 메소드를 구현해야 함<br><br>
build 메소드의 역할: Flutter가 실행하게 되는데, build 메소드가 반환하는걸 화면에서 보여줌<br>
(위젯의 UI를 만드는 것)

## root Widget
--------------
앱의 시작 위젯으로 모든 위젯의 시작점<br>
앱의 root Widget은 두 개의 옵션 중 하나를 반환해야 함
- material: 구글의 디자인 시스템
- cupertino:  애플의 디자인 시스템

🤔 하지만 우리는 구글, 애플의 디자인이 아닌 우리만의 디자인을 만들고 싶은데 왜 material, cuperino를 반환해야 하는 걸까?<br><br>
디자인 시스템을 사용해도 android, ios의 앱처럼 보이진 않지만 root Widget이여서 시작점을 선택하는 것으로, 기본 UI 설정과 같은 재료들을 선택하는 것.<br>
(하지만 Flutter는 구글이 제작했기에 material 앱 스타일이 cuperino 스타일보다 보기 좋다함 😅)

## scaffold
--------------
Flutter는 하나의 개발 규칙이 있는데, 화면이 scaffold를 가져야하는 것<br><br>
scaffold: 화면의 구성 및 구조에 관한 것들을 가지고 있는 위젯, 화면의 구조를 제공하며,<br>
모바일 앱의 모든 화면은 scaffold가 필요함<br><br>
scaffold의 기능: navigation bar 구현, bottom tab bar, 화면 중앙 정렬 등

## home
--------------
앱의 home에 있을 때 무엇을 보여줄지 정하는 프로퍼티로 home안의 위젯을 구성하면 됨

## 구성 요소
--------------
클래스에 커서를 올려 놓으면 클래스의 구성이 나오는데, 그것으로 무엇을 할 수 있는지 유추해 볼 수 있음, 자주 사용하도록 하자