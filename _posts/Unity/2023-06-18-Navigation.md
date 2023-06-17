---
layout: post
title: "Navigation"
excerpt: "유니티 Navigation"
categories: [Unity]
tag: [unity, study]
date: 2023-06-16 01:00:00 +0300
---
## Navigation?

---

특정 영역의 길을 AI가 최단 경로를 찾아 목적지에 도달하게 도와주는 기능

## Navigation 설정

---

Navigation을 사용하기 위해서는 이동 가능한 경로를 지정하는 작업이 필요하다.

![image](/assets/img/Unity/Navigation_1.png)

Window → AI → Navigation (Obsolete)

![image](/assets/img/Unity/Navigation_2.png)

Navigation 경로를 지정할 수 있는 탭이 켜진다.

![image](/assets/img/Unity/Navigation_3.png)

이동할 바닥, 장애물을 모두 선택한 후(Mesh Renderer가 있는 오브젝트)

![image](/assets/img/Unity/Navigation_4.png)

Navigation Static을 체크 해준다.

### Object

- Scene Filter: 캐릭터가 이동할 수 있는 영역을 선택 (Mesh renderer와 Terrain이 존재하는 오브젝트만 지정 가능)
- Navigation Static: 해당 오브젝트를 캐릭터가 기을 찾을 수 있는 표면으로 지정
- Generate OffMeshLinks:  Off Mesh Link를 생성함
- Navigation Area: 해당 오브젝트의 영역 타입 설정 (Walkable, Not Walkable, Jump)

`Off Mesh Link: 걸을 수 있는 내비게이션 메시 표면 외 지나갈 수 있는 경로를 만들 수 있음 (장애물 점프, 지나가기 전 문열기 등)`

### Areas

![image](/assets/img/Unity/Navigation_5.png)

Areas는 캐릭터가 지날 수 있는 영역의 비용을 지정할 수 있다.

### Bake

오브젝트들의 설정이 끝났으면 Bake 탭에서 경로를 구워줘야 한다.

![image](/assets/img/Unity/Navigation_6.png)

하단의 Bake 버튼을 눌러주면

![image](/assets/img/Unity/Navigation_7.png)

맵 오브젝트 바닥에 파란색이 칠해지는 것을 볼 수 있다.

이는 캐릭터가 이동할 수 있는 경로가 칠해진 것이다.

## 캐릭터 이동

---

![image](/assets/img/Unity/Navigation_8.png)

캐릭터에게 Nav Mesh Agent 컴포넌트를 추가한다.

### 프로퍼티

| 프로퍼티 | 기능 |
| --- | --- |
| Agent Type | 저장된 에이전트 선택 |
| Base Offset | 캐릭터의 충돌 위치 |
| Speed | 최대 이동 속도 |
| Angular Speed | 최대 회전 속도 |
| Acceleration | 최대 가속 |
| Stopping Distance | 도착 위치에서 얼마나 떨어진 위치에서 멈출지 지정 |
| Auto Braking | 도착 위치에 다다를 때 속도를 줄임 |
| Radius | 캐릭터의 충돌 범위 |
| Height | 캐릭터의 충돌 높이 |
| Quality | 장애물 회피 품질 |
| Priority | 해당 숫자보다 큰 숫자를 가진 오브젝트는 회피 대상에서 제외 |
| Auto Traverse Off Mesh Link | Off Mesh Link 횡단 방법<br>- True: 자동으로 횡단<br>- Fasle: 특정 방법으로 횡단 |
| Auto Repath | 경로의 끝에 도달하면 경로 재탐색 |
| Area Mask | 어떠한 영역 타입을 이동할 것인지 (캐릭터 마다 이동 영역을 다르게 할 수 있음) |

코드로 바닥에 우클릭을 클릭하면 해당 위치로 이동하는 코드를 구현한다.

### 이동

마우스 우클릭을 한 위치로 플레이어가 이동하는 코드를 구현해 주었다.

![image](/assets/img/Unity/nav_gif1.gif)

지정한 위치로 이동하는 것을 볼 수 있지만 경로가 나타나지 않아 보기 어려워 경로와 도착지를 표시해 주겠다.

![image](/assets/img/Unity/nav_gif2.gif)

지정된 경로로 이동하는 것을 볼 수 있다.

## 카메라 이동

---

고정된 카메라로 경로를 지정하려니 불편하여 카메라를 이동하는 코드를 적용하였다.

![image](/assets/img/Unity/nav_gif3.gif)

지정된 키를 눌러 이동 키를 눌러 카메라를 이동할 수 있다..

## Nav Mesh Obstacle

---

경로 위 장애물을 추가할 수 있다.

![image](/assets/img/Unity/Navigation_9.png)

장애물을 하나 만든 후 Nav Mesh Obstacle 컴포넌트를 추가해 주었다.

### 프로퍼티

- Shpe: 장애물의 형태를 지정. (Capsule, Cube)
- Center: 장애물의 충돌 범위 위치를 지정.
- Size: 장애물의 충돌 범위 크기를 지정.
- Carve: 경로상에 장애물이 있을 시 장애물을 피해 경로를 지정할 여부를 지정.

![image](/assets/img/Unity/Navigation_10.png)

Carve를 True로 지정하면 다시 Bake를 하지 않아도 캐릭터의 이동 경로가 제한되는 것을 볼 수 있다.

### Carve: False

![image](/assets/img/Unity/nav_gif4.gif)

### Carve: True

![image](/assets/img/Unity/nav_gif5.gif)

Carve의 여부에 따라 이동 경로가 차이나는 것을 볼 수 있다.

## 캐릭터, 카메라 이동 코드

---

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.AI;

public class Move : MonoBehaviour
{
    ////////////////// Player ///////////////////////////////////
    NavMeshAgent agent;
    Animator anime;
    LineRenderer lr;
    [SerializeField] Transform point;
    Coroutine path;

    ////////////////// Camera ///////////////////////////////////
    [SerializeField] KeyCode camMoveSwap = KeyCode.Space;
    Vector3 camMoveDir;
    float camSpeed;

    float viewDirX;
    float viewDirY;
    float rotSpeed;
    bool camMove;

    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
        anime = GetComponent<Animator>();
        lr = GetComponent<LineRenderer>();

        rotSpeed = 3.0f;
        camSpeed = 1.5f;
        camMove = false;

        lr.startWidth = 0.1f;
        lr.endWidth = 0.1f;
        lr.material.color = Color.green;
        lr.enabled = false;
    }

    void Update()
    {
        PlayerMove();
        CameraMove();
    }

    float h => Input.GetAxis("Horizontal");
    float v => Input.GetAxis("Vertical");
    void CameraMove()
    {
        if (Input.GetKeyDown(camMoveSwap))
            camMove = !camMove;
        if (camMove)
        {
            camMoveDir = new Vector3(h, 0f, v);
            camMoveDir = Camera.main.transform.TransformDirection(camMoveDir);
            Camera.main.transform.position += camMoveDir * camSpeed;
            viewDirX += Input.GetAxis("Mouse X") * rotSpeed;
            viewDirY += Input.GetAxis("Mouse Y") * -rotSpeed;
            Camera.main.transform.rotation = Quaternion.Euler(viewDirY, viewDirX, 0f);
        }
    }

    void PlayerMove()
    {
        if (Input.GetMouseButtonDown(1))
        {
            Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

            if (Physics.Raycast(ray, out RaycastHit hit))
            {
                agent.SetDestination(hit.point);
                anime.SetFloat("Speed", 2.0f);
                point.gameObject.SetActive(true);
                point.position = hit.point;

                if (path != null)
                    StopCoroutine(path);
                path = StartCoroutine(DrawPath());
            }
        }
        else if (agent.remainingDistance < 0.1f)
        {
            anime.SetFloat("Speed", 0.0f);
            point.gameObject.SetActive(false);
            lr.enabled = false;

            if (path != null)
                StopCoroutine(path);
        }
    }

    IEnumerator DrawPath()
    {
        lr.enabled = true;
        yield return null;
        while (true)
        {
            int cnt = agent.path.corners.Length;
            lr.positionCount = cnt;
            for (int i = 0; i < cnt; i++)
            {
                lr.SetPosition(i, agent.path.corners[i]);
            }
            yield return null;
        }
    }
}
```

## 참고

---

[내비메시 에이전트 - Unity 매뉴얼](https://docs.unity3d.com/kr/2021.3/Manual/class-NavMeshAgent.html)

[오늘코딩-Navigation](https://www.youtube.com/watch?v=ILefNAZGVFY)