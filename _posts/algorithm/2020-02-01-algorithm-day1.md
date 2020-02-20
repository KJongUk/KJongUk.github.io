---
title:  "백준 알고리즘 문제 - 2170번 선긋기"
excerpt: "스위핑 알고리즘"

categories:
  - algorithm
use_math: true
---
스위핑 알고리즘
---

<https://www.acmicpc.net/problem/2170>
---

1.문제 설명
---
문제를 설명하면 이렇다. 큰 도화지에 여러 선을 그리게 되는데,

이 선들은 시작점과 끝점이 주어지며 원하는 것은 그려진 선들의 총 길이이다.

그리고 선을 그은 횟수 N($1≦N≦10^5$),점의 범위는$-10^9≦p≦10^9$ 이다.

2.문제 풀이
---
 이 문제에서 주어지는 시작점과 끝점의 범위가 너무 커서 배열을 이용하여 선이 그어진 부분을 단순히 표시하기에는 어려움이 있으므로 다른 방식으로 접근해야한다.

 따라서 이 문제를 해결하기 위하여서는 오늘의 문제 주제인 **스위핑 알고리즘**을 이용하여 해결 할 수 있다.
**스위핑 알고리즘**이란 간단히 이야기해서 왼쪽에서 오른쪽 또는 오른쪽에서 왼쪽으로 스캔하면서 문제에서 요구하는 결과를 얻기 위해 특정한 자료구조를 이용하거나 처리를 해주는 것이라 할 수 있다.
 
**-알고리즘 적용-**

1. 왼쪽점을 기준으로 정렬한 선들을 왼쪽부터 오른쪽으로 스캔하면서, 선을 합쳐준다.
1. 스캔 하기 전 sum, left, right 변수를 만들어 준다. 
- 처음 스캔할 경우: left=첫번째 원소의 왼쪽점, right=첫번째 원소의 오른쪽 점
- sum 변수에 right-left(처음 선분의 길이)을 저장하여 준다.

1. 다음 배열의 원소들을 스캔 할 때 변수들을 업데이트 시켜준다.
- 원소가 현재 선에 완전히 포함이 되는 경우=> 업데이트X
- 원소가 현재 선의 일부분에 포함이 되는 경우=> 업데이트O
- 원소가 현재 선에 포함이 되지 않는 경우=> 업데이트O

3.전체 코드
---
``` cpp
#include <iostream>
#include <algorithm>
#include <string>
#include <queue>
#include <vector>
#include <string.h>
#include <math.h>
using namespace std;

int main(){
    ios_base::sync_with_stdio(false); 
    cin.tie(NULL); 
    cout.tie(NULL);
    
    int n;
    cin>>n;
    vector<pair<int,int>> v(n);//라인을 저장할 벡터 선언
    for(int i=0;i<n;i++){
        cin>>v[i].first>>v[i].second;//라인들을 입력 받음
    }
    sort(v.begin(),v.end());//라인의 왼쪽점들을 기준으로 정렬.
    
    int sum=0;//선의 총 길이를 저장할 변수
    int left=v[0].first,right=v[0].second;//left, right선언
    sum+=right-left;
    for(int i=1;i<n;i++){
        if(v[i].second<=right) continue;//완전히 포함이 되는 경우
        else if(v[i].first<right&&right<v[i].second){//일부만 포함이 되는 경우
            sum+=(v[i].second-right);
            right=v[i].second;
        }
        else if(right<v[i].first){//포함이 되지 않는 경우
            left=v[i].first;
            right=v[i].second;
            sum+=(right-left);
        }
    }
    cout<<sum<<"\n";
}
```

4. 단계별 코드 설명
---

5. 출력 예시
---
