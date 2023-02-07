---
layout: post
title: "Cinemachine"
excerpt: "Unity Cinemachine"
categories: [Unity]
tag: [unity, study]
date: 2023-02-07 00:00:01 +0300
---
## 시네머신?
---
영화 촬영을 하는 것처럼 씬을 촬영하여 게임 화면상에 비추어주는 유니티 패키지.<br><br>
기본적으로 카메라 오브젝트를 생성하지 않고 가상 카메라를 이용.<br><br>
가상 카메라는 원래 있는 카메라 오브젝트를 이용하여 여러 환경에서 비추어줄 씬을 보여주도록 도와줌.<br><br>
가상 카메라는 서로에게 영향을 끼치지 않으며, 유니티 카메라에도 영향을 받지 않아 자유로운 카메라 구성이 가능.

## 패키지 추가
---

![image](/assets/img/Unity/Cinemachine_1.png)<br>
Window→Package Manager<br><br>
![image](/assets/img/Unity/Cinemachine_2.png)<br>
Packages를 Unity Registry로 변경 후 Cinemachine을 찾아 install<br><br>
![image](/assets/img/Unity/Cinemachine_3.png)<br>
Packages 디렉터리에 Cinemachine이 생긴것을 볼 수 있다<br><br>
![image](/assets/img/Unity/Cinemachine_4.png)<br>
Hierachy에서 시네머신 카메라를 생성할 수 있다.<br><br>

## 종류
---

| 카메라 | 설명 |
| --- | --- |
| Virtual Camera | 기본적인 카메라, 유니티 카메라를 조정하듯 자유롭게 활용 가능 |
| FreeLook Camera | 오브젝트를 중심으로 원형의 링을 생성하여 그 구간 안에서 타깃을 관찰하는 카메라 |
| Blend List Camera | 할당된 버추얼 카메라들을 정해진 블랜드 방식에 따라 순차적으로 전환하는 카메라 |
| State-Driven Camera | 타겟 애니메이션의 상태별로 활성화/비활성화 시킬 수 있는 카메라 |
| ClearShot Camera | 플레이어의 충돌/트리거 상태에 따라 활성화/비활성화 시킬 수 있는 카메라 |
| Dolly Camera | 트랙을 깔아 해당 트랙에 따라 움직이는 카메라, Dolly Track과 함께 사용 |
| Target Group Camera | 그룹으로 묶인 카메라들을 자동으로 계산해 한 화면에 보여주는 카메라 |
| Mixing Camera | Child Camera Weight 값에 따라 활성화/비활성화 해주는 카메라 |
| 2D Camera | 직교 뷰로 사용되는 카메라 |

## 프로퍼티 설명
---
💡 가장 기본적인 Virtual Camera의 설명<br>
![image](/assets/img/Unity/Cinemachine_5.png)<br><br>
Save During Play: 에디터 Play 중 내용을 변경해도 설정 저장<br><br>
Priority: 카메라 우선도<br><br>
Follow: 따라다닐 오브젝트<br><br>
Look At: 바라볼 오브젝트<br><br>
Standby Update: Live 상태가 아닌 카메라의 업데이트 빈도
- Never: 항상
- Always: Live 일때만
- Round Robine: 정기적으로

Transitions: 카메라 사이 이동시 씬 전환 효과<br><br>
Body: 카메라의 위치<br><br>
Aim: 카메라의 각도<br>

## Body
---
![image](/assets/img/Unity/Cinemachine_6.png)<br><br>
Follow Offset: 카메라와 대상의 거리<br><br>
Damping(제동): 값의 급격한 변화를 꺾어 부드럽게 이어주는 비율, 값이 커질 수록 부드러워져 민감도가 줄어듬

## Aim
---
![image](/assets/img/Unity/Cinemachine_7.png)<br><br>
Traked Object Offset: 대상에서 얼마나 더 떨어진 곳에서 바라볼지 (y값을 늘리면 대상보다 높은 곳을 조준한다)<br><br>
Damping: 회전 속도에 대한 제동값<br><br>
Dead Zone: 데드존의 크기<br><br>
Soft Zone: 소프트존의 크기<br>

## Soft Zone, Dead Zone
---
![image](/assets/img/Unity/Cinemachine_8.png)<br><br>
<span style="color:red">
Dead Zone(데드존)
</span>: 대상이 데드존 내에서 움직이는 동안에는 카메라가 회전하지 않는다<br><br>
<span style="color:blue">
Soft Zone(소프트존)
</span>: 대상이 소프트존 내에 있다면 카메라가 부드럽게 회전하여 대상이 데드존 안으로 들어가게 한다<br><br>
<span style="color:yellow">
하드리밋
</span>: 소프트존보다 바깥에 있어 더 빠르고 격한 속도로 회전하며 대상이 데드존 안으로 들어가게 함<br><br>

`데드존이 클 수록 화면은 느리고 부드럽게 움직이며, 데드존이 작을수록 화면은 빠르고 격하게 움직임`

## 테스트
---
오브젝트를 생성하여 상하좌우로 움직이는 코드를 구현한 후 시네머신의 Follow, Look At에 연결해주었다.<br>
![image](/assets/img/Unity/Cinemachine_9.gif)<br><br>

카메라가 회전하여 오브젝트가 데드존으로 들어가게 하는 것을 볼 수 있다.