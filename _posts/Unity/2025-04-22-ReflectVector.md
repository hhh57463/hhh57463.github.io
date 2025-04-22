---
layout: post
title: "Reflect Vector"
excerpt: "Reflect Vector (반사 벡터)"
categories: [Unity]
tag: [unity, study]
date: 2025-04-22 01:00:00 +0300
---
## 개요
요즘 회사를 다니면서 유니티를 소홀히 하는것 같아 유니티로 시뮬레이터를 만들어보기로 했다.<br>
기능 구현 중 오랜만에 반사벡터를 구하게 돼 복습할 겸 기록한다.

## 반사 벡터

---

Reflect Vector: 입선벡터, 법선벡터를 이용하여 특정 사물에 부딪혔을때 반사각 계산하는 벡터

수학 공식은 아래와 같음<br>
`R = P + 2n(-P*n)`<br>
R: 반사벡터<br>
P: 입선벡터<br>
n: 법선벡터<br>

## 코드

---
```C#
    public float speed = 5f;
    private Rigidbody2D rb;
    Vector2 direction;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        rb.freezeRotation = true;

        direction = Random.insideUnitCircle.normalized;
        rb.linearVelocity = direction * speed;
    }

    void OnCollisionEnter2D(Collision2D collision)
    {
        Vector2 incomingVec = direction;
        incomingVec = incomingVec.normalized;
        Vector2 normalVec = collision.contacts[0].normal;
        Vector2 reflectVec = Vector2.Reflect(incomingVec, normalVec);
        reflectVec = reflectVec.normalized;
        direction = reflectVec;
        rb.linearVelocity = direction * speed;
    }
```
일단 초기값으로 Rigidbody를 사용하여 랜덤한 방향으로 이동하는 코드를 Start에 구현하였다<br>
방향은 충돌할때마다 입선벡터로 사용해야하기에 변수로 지정하여 관리했다<br><br>
- Vector2 incomingVec: 입사각을 저장한다, 정규화하여 크기를 1로 한다
- Vector2 normalVec: 법선벡터를 가져온다 (충돌면 수직 방향을 의미한다)
- Vector2 reflectVec: 반사벡터를 구한다, 정규화하여 크기를 1로 한다

## 결과
---
![Image](/assets/img/Unity/RV.gif)<br>
~~고놈참 시원하게 잘 튀는구나~~<br><br>
뭔가 멍때리고 보게되는 장면이 나왔다.<br>
벽 뿐만 아닌 구체들끼리도 반사벡터를 구해서 잘 반영하는 모습을 볼 수 있다.