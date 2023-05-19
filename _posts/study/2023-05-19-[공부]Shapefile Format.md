---
layout: post
title: "Shapefile Format"
excerpt: "shapefile format"
categories: [Study]
tag: [study, shapefile]
date: 2023-05-19 00:00:01 +0300
---

# TMI
----------------------
교내 연구실에서 연구 진행 중 Shapefile format 분석을 의뢰받아 정리하여 요약

## 목차

---

1. shapefile이란?
2. Little Endian, Big Endian
3. shapefile 구성요소

## Shapefile이란?

---

.shp를 확장자로 가지며, 벡터방식으로 공간정보를 저장하는 파일.

shapefile은 점(Point), 선(Line), 면(Polygon) 중 하나의 속성을 가지며, shapefile에서 면(Polygon)을 잘라내거나 합칠 수 있다.

shapefile은 서로 다른 확장명을 가진 세 개 이상의 파일로 구성되는데

- .shp - 피처의 지오메트리(형상)을 저장하는 기본파일
- .shp - 피처의 기하학의 색인을 저장하는 인덱스 파일
- .dbf - 피처의 속성 정보를 저장하는 dBASE 테이블

이들을 구성 요소 파일이라 부른다.

위의 3개의 파일을 main file이라 칭하며 그 외에는 아래의 파일이 존재.

- .prj - 지리 좌표를 알려주는 파일
- .sbn - 지리 공간 인덱스를 저장하는 파일
- .sbx - spatial join의 기능을 수행하거나 shape 필드에 대한 인덱스 생성에 필요한 파일

*spatial join (공간결합): 두 공간 데이터프레임을 결합(merge)하는데, key 값이 아닌 위치정보에 따라 결합(overlay)해주는 방식.

## Little Endian, Big Endian

---

컴퓨터는 데이터를 메모리에 저장할 때 byte 단위로 나누어 저장하지만, 컴퓨터가 저장하는 데이터는 대게 32bit(4byte)나 64bit(8byte)로 구성된다.

따라서 연속되는 byte를 순서대로 저장해야 하는데, 이것을 바이트 저장 순서(byte order)라고 함.

바이트가 저장되는 순서에 따라 두 가지 방법으로 나눌 수 있다.

- 리틀 엔디안(Little Endian)
- 빅 엔디안(Big Endian)

### Little Endian

낮은 주소에 데이터의 낮은 바이트(LSB, Least Significant Bit)부터 저장하는 방식.

이 방식은 평소 우리가 숫자를 사용하는 선형 방식과는 반대로 거꾸로 읽어야 한다.

대부분 인텔 CPU 계열에서 이 방식으로 데이터를 저장함.

0x12345678을 저장해야 할 경우 이 정수는 0x12, 0x34, 0x56, 0x78로 1byte 값 4개로 구성됨.

이 4개의 1byte 값을 Big Endian 방식으로 저장되면 그림과 같이 저장됨.

![리틀 엔디안 저장 방식](/assets/img/Shapefile/Shapefile_1.png)


### Big Endian

낮은 주소에 데이터의 높은 바이트(MSB, Most Significant Bit)부터 저장하는 방식.

평소 우리가 숫자를 사용하는 선형 방식과 같은 방식.

메모리에 저장된 순서 그대로 읽을 수 있으며, 이해하기 쉽다는 장점을 가짐.

SPARC을 포함한 대부분의 RISC CPU 계열에서 이 방식으로 데이터를 저장함.

0x12345678을 저장해야 할 경우 이 정수는 0x12, 0x34, 0x56, 0x78로 1byte 값 4개로 구성됨.

이 4개의 1byte 값을 Big Endian 방식으로 저장되면 그림과 같이 저장됨.

![빅 엔디안 저장 방식](/assets/img/Shapefile/Shapefile_2.png)

### 리틀 엔디안 vs 빅 엔디안

빅 엔디안과 리틀 엔디안은 단지 저장해야 할 큰 데이터를 어떻게 나누어 저장하는가에 따른 차이일 뿐, 어느 방식이 더 우수하다고는 단정할 수 없음.

물리적으로 데이터를 조작하거나 산술 연산을 수행할 때에는 리틀 엔디안 방식이 더 효율적이지만,데이터의 각 바이트를 배열처럼 취급할 때에는 빅 엔디안 방식이 더 적합.

대부분의 시스템은 인텔 기반의 윈도우이므로 리틀 엔디안 방식을 사용.

네트워크를 통해 데이터를 전송할 때에는 빅 엔디안 방식이 사용.

인텔 기반의 시스템에서 소켓 통신을 할 때는 바이트 순서에 신경을 써서 데이터를 전달해야 함.

## Shapefile 구성요소

---

shapefile은 다음과 같이 구성됨.

![Shapefile 구성요소](/assets/img/Shapefile/Shapefile_3.png)

### File Header 구성

| Position | Field | Value | Type | Byte Order |
| --- | --- | --- | --- | --- |
| Byte 0 | File Code | 9994 | Integer | Big Endian |
| Byte 4 | Unused | 0 | Integer | Big Endian |
| Byte 8 | Unused | 0 | Integer | Big Endian |
| Byte 12 | Unused | 0 | Integer | Big Endian |
| Byte 16 | Unused | 0 | Integer | Big Endian |
| Byte 20 | Unused | 0 | Integer | Big Endian |
| Byte 24 | File Length | File Length | Integer | Big Endian |
| Byte 28 | Version | 1000 | Integer | Little Endian |
| Byte 32 | Shape Type | Shape Type | Integer | Little Endian |
| Byte 36 | Bounding Box | Xmin | Double | Little Endian |
| Byte 44 | Bouding Box | Ymin | Double | Little Endian |
| Byte 52 | Bounding Box | Xmax | Double | Little Endian |
| Byte 60 | Bouding Box | Ymax | Double | Little Endian |
| Byte 68* | Bounding Box | Zmin | Double | Little Endian |
| Byte 76* | Bouding Box | Zmax | Double | Little Endian |
| Byte 84* | Bounding Box | Mmin | Double | Little Endian |
| Byte 92* | Bouding Box | Mmax | Double | Little Endian |

Bounding Box란 해당 SHP 파일의 MBR(Minimum Bounding Rectangle)을 의미.

*MBR: 여러 점으로 구성되어 있을 경우 해당 점을 둘러싸고 있는 가장  작은 사각형으로, 해당 개체가 포함된 지도상의 위치를 대략적으로 표시하는 정보를 가짐.

### Shape Type

File Header구성 중 Byte32에 위치한 Shape Type은 이름당 값이 지정되어 있으며, 아래와 같다.

| Name | Value |
| --- | --- |
| NULL | 0 |
| POINT | 1 |
| ARC | 3 |
| POLYGON | 5 |
| MULTIPOINT | 8 |
| POINTZ | 11 |
| ARCZ | 13 |
| POLYGONZ | 15 |
| MULTIPOINTZ | 18 |
| POINTM | 21 |
| ARCM | 23 |
| POLYGONM | 25 |
| MULTIPOINTM | 28 |
| MULTIPATCH | 31 |

SHP 포맷은 한 파일에 하나의 Shape Type만을 수용한다. 따라서 Point와 Line이 같은 파일에 있을 수 없다.

### Record Header

| Position | Field | Value | Type | Order |
| --- | --- | --- | --- | --- |
| Byte 0 | Record Number | Record Number | Integer | Big Endian |
| Byte 4 | Content Length | Content Length | Integer | Big Endian |

Record Header는 거의 사용되지 않음.(.shx 파일을 이용해 shp를 포인팅할 수 있기 때문)

### Record Contents

Record Contents는 실제 점(Point), 선(Line), 면(Polygon)이 기록되어 있으며, Shape Type에 따라 달라짐.

ShapeType - Point

| Position | Field | Value | Type | Number | Order |
| --- | --- | --- | --- | --- | --- |
| Byte 0 | Shape Type | 1 | Integer | 1 | Little Endian |
| Byte 4 | X | X | Double | 1 | Little Endian |
| Byte 12 | Y | Y | Double | 1 | Little Endian |

Point는 double로 된 x, y로 구성되어 있는 값.

```c
struct Point
{
  double x;
  double y;
}
```

ShapeType - Multi Point

Multi Point(다중 포인트)는 여러 개의 점을 하나의 개체로 인식하는 것. 위치가 다른 곳에 있는 점이라도 같은 속성을 지니고 있을 경우 이들을 하나로 묶어서 사용하기 좋음

| Position | Field | Value | Type | Number | Order |
| --- | --- | --- | --- | --- | --- |
| Byte 0 | Shape Type | 8 | Integer | 1 | Little Endian |
| Byte 4 | Box | Box | Double | 4 | Little Endian |
| Byte 36 | NumPoints | NumPoints | Integer | 1 | Little Endian |
| Byte 40 | Points | Points | Point | NumPoints | Little Endian |

Byte 4에 위치한 Box 또한 MBR.

Byte 36의 NumPoints는 개체가 가지는 점(Point)의 개수를 나타내며, Multi Point는 여러 개의 점을 하나의 개체로 묶은 것이기 때문에, 개체당 점이 하나 이상 존재할 수 있음.

Byte 40부터는 개체 내에 실제로 포함된 점(Point)들을 담고 있는 것으로, 개체당 하나 이상의 점이 존재할 수 있으므로, NumPoints만큼의 Point가 나열됨.

```c
struct MuliPoint
{
  double Box[4];
  int NumPoints;
  Point[NumPoints] Points;
}
```

ShapeType - PolyLine, Arc

아크는 Line 혹은 PolyLine을 수용하는 구조체로, 다중 포인트처럼 위치가 다른 곳에 있는 선을 같은 개체로 처리할 수 있음.

| Position | Field | Value | Type | Number | Order |
| --- | --- | --- | --- | --- | --- |
| Byte 0 | Shape Type | 3 | Integer | 1 | Little Endian |
| Byte 4 | Box | Box | Double | 4 | Litle Endian |
| Byte 36 | NumParts | NumParts | Integer | 1 | Little Endian |
| Byte 40 | NumPoints | NumPoints | Integer | 1 | Litle Endian |
| Byte 44 | Parts | Parts | Integer | NumParts | Little Endian |
| Byte X | Points | Points | Point | NumParts | Litle Endian |

*Part: 한 Arc 개체에 몇 개의 PolyLine이 존재하는지

Byte 4의 Box 역시 MBR을 의미

Byte 36의 NumParts는 Arc 개체에 속한 PolyLine 개체의 수를 나타내며, Byte 40의 NumPoints는 해당 Arc 개체에 포함된 전체 점의 수를 의미.

Byte 40의 NumPoints는 개체에 있는 점의 수를 의미, 위치나 Part와 관계없이 저장되며 점의 개수가 몇 개인지 지정하는 것.

Byte 44의 Parts는 각각의 PolyLine 개체가 몇 개의 점으로 이루어져 있는지를 나타내며, 이를 통해 어느 부분이 어떤 PolyLine에 속하는지를 파악할 수 있음.

Byte X의 Points는 각 점의 위치를 나타내며, PolyLine 개체의 경우는 순서대로 각 점의 위치가 저장되며, Arc 개체의 경우는 여러 개의 PolyLine으로 이루어져 있으므로 Parts 정보를 통해 어느 PolyLine에 속하는지를 파악한 후 해당 PolyLine에 속한 각 점의 위치가 순서대로 저장됨.

```c
struct PolyLine
{
  double Box[4];
  int NumParts;
  int NumPoints;
  int Parts[NumParts];
  Point[NumPoints] Points;
}
```

ShapeType - Polygon

Polygon은 PolyLine 혹은 Arc의 구조와 같지만 파트별로 시작점과 끝점이 같아서 닫혀있다는 것을 강조.

| Position | Field | Value | Type | Number | Order |
| --- | --- | --- | --- | --- | --- |
| Byte 0 | Shape Type | 5 | Integer | 1 | Little Endian |
| Byte 4 | Box | Box | Double | 4 | Litle Endian |
| Byte 36 | NumParts | NumParts | Integer | 1 | Little Endian |
| Byte 40 | NumPoints | NumPoints | Integer | 1 | Litle Endian |
| Byte 44 | Parts | Parts | Integer | NumParts | Little Endian |
| Byte X | Points | Points | Point | NumParts | Litle Endian |

```c
struct Polygon
{
  double Box[4];
  int NumParts;
  int NumPoints;
  int Parts[NumParts];
  Point[NumPoints] Points;
}
```

### 출처 및 참고
---
[[GIS] 공간 데이터 결합(Spatial Join)](https://yganalyst.github.io/spatial_analysis/spatial_analysis_4/)

[코딩교육 티씨피스쿨](http://www.tcpschool.com/c/c_refer_endian)

[QGIS 알아가기 - shapefile이란 무엇일까?(.shp)](https://velog.io/@cha-suyeon/QGIS-알아가기-shapefile이란-무엇일까.shp)

[[SHPViewer 강좌 1] SHP 포맷 분석 1](https://endofcap.tistory.com/3)

[SHP - ESRI Shapefile](https://docs.fileformat.com/gis/shp/)