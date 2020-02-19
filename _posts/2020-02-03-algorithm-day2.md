---
title:  "백준 알고리즘 문제 - 3392번 화성지도"
excerpt: "스위핑 알고리즘,세그먼트 트리"

categories:
  - algorithm
use_math: true
---

<https://www.acmicpc.net/problem/3392>
---

스위핑 알고리즘과 세그먼트 트리를 사용하여 푸는 문제입니다. 처음에 이해가 어려워 아래의 글을 참고하여 해결하였습니다.

<https://jason9319.tistory.com/58>

1.문제 설명
---
 문제에서 요구하는건 생각보다 간단하였다. 쉽게 말해 여러 개의 직사각형이 주어지고, 해당 직사각형들은 겹칠 수가 있는데,
이 때 직사각형들의 넓이의 합을 구하는 문제이다. 그러나 직사각형의 꼭지점의 범위가 $1≦p≦30000$이고 직사각형의 개수 N이
 $1≦N≦10000$이다. 간단한 그림은 아래와 같다.

![title](/assets/images/3392_1.png){: .align-center width="50%" height="40%"}

2.문제 풀이
---
 역시나 직사각형의 꼭지점 범위가 크므로 $30000^2$크기의 배열을 선언하여 문제를 푼다면 시간복잡도와 공간복잡도가 문제에서 
 요구하는 것을 만족하지 못한다. 따라서 선긋기 문제에서 배운 **스위핑 알고리즘**과 부분 합을 빠르게 구할 수 있는 **세그먼트 트리**를 
 활용하여 문제를 해결 할 수 있다. 

**-알고리즘 적용-**

1. 직사각형의 선분들 중 세로방향의 선분들만을 배열에 저장해준다. 이 때 변수 E를 추가하여 선분이 직사각형의 왼쪽 세로 선은 E=1,오른쪽 세로 선은 E=-1로 저장해준다.
![title](/assets/images/3392_2.png){: .align-center width="80%" height="80%"}
2. 배열에 저장한 선분들을 x좌표를 기준으로 오름차순으로 정렬해준다.
3. 변수 last를 선언한 후 배열의 첫번째 선분의 x좌표 값을 저장해준다. 세그먼트 트리를 이용하여 업데이트 작업을 반복해준다.
  - 업데이트를 이용하여 가져올 정보는 현재 보고 있는 세로 선분까지 업데이트된 y좌표의 개수이다.
  - 따라서 세그먼트 트리 레이지 프로퍼게이션을 이용하거나 카운트 배열을 만들고 카운트 배열이 1인 부분은 right-left+1을 리턴하고 아닌 경우 자식 노드들의 합을 리턴해준다.
4. 다음 세로 선분을 볼 때 dx=(현재 보고 있는 선분의 x좌표-last)라 하고, $dx * seg[1]$ 을 이용하면 직사각형의 면적을 구할 수 있다.
![title](/assets/images/3392_3.png){: .align-center width="80%" height="80%"}

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

struct line{
    int x,y1,y2,e;
};

bool operator<(const line& p1, const line& p2){
    return p1.x<p2.x;
}

vector<int> seg(30000*4+1);
vector<int> cnt(30000*4+1);

void update(int start,int end,int val,int node,int left,int right){
    if(right < start || end < left) return;
    if(start <= left&& right<=end) cnt[node]+=val;
    else{
        int mid=(left+right)/2;
        update(start,end,val,node*2,left,mid);
        update(start,end,val,node*2+1,mid+1,right);
    }
    if(cnt[node]) seg[node]=right-left+1;
    else{
        if(left==right) seg[node]=0;//leaf 노드인 경우
        else seg[node]=seg[node*2]+seg[node*2+1];//아닌 경우는 오른쪽+왼쪽
    }
    return;
}

int main(){
    ios_base::sync_with_stdio(false); 
    cin.tie(NULL); 
    cout.tie(NULL);
    
    int n;
    cin>>n;
    
    vector<line> v(2*n);
    for(int i=0;i<n;i++){
        int x1,x2,y1,y2;
        cin>>x1>>y1>>x2>>y2;
        v[i].x=x1; v[i].y1=y1; v[i].y2=y2-1; v[i].e=1;
        v[n+i].x=x2; v[n+i].y1=y1; v[n+i].y2=y2-1; v[n+i].e=-1;
    }
    
    sort(v.begin(),v.end());
    
    int last=v[0].x;
    int answer=0;
    update(v[0].y1,v[0].y2,v[0].e,1,0,30000);
    for(int i=1;i<v.size();i++){
        answer+=(v[i].x-last)*seg[1];
        update(v[i].y1,v[i].y2,v[i].e,1,0,30000);
        
        last=v[i].x;
    }
    cout<<answer<<'\n';
}
```

4. 단계별 코드 설명
---

5. 출력 예시
---
