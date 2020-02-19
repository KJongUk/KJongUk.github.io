---
title:  "백준 알고리즘 문제 - 17131번 여우가 정보섬에 올라온 이유"
excerpt: "세그먼트 트리"

categories:
  - algorithm
use_math: true
---

<https://www.acmicpc.net/problem/17131>
---

1.문제 설명
---
 2차원 공간에 좌표들이 주어지는데, 이 좌표들 중 3개를 선택하여 v형을 이루는 좌표들의 쌍의 개수를 구하는
문제이다. 좌표의 개수는 N($1≦N≦200000$)개이고, 좌표의 범위는 $-200000≦x,y≦200000$이다.이 때
좌표들의 쌍의 개수가 커질 수 있으므로 나머지 연산을 이용해 $10^9+7$로 나눈 값을 출력 해준다.


2.문제 풀이
---
문제는 세그먼트 트리를 이용하여 풀 수 있는 문제였다. 주어진 좌표의 범위가 -200000~200000이므로, 구간
 합을 구하는 세그먼트 트리를 이용하여 x좌표를 이용하여 좌표상의 구간에서 있는 좌표들의 개수를 구할 수 있는
 세그먼트 트리를 만들어 준다. 그리고 좌표들의 y좌표를 기준으로 배열을 오름차순으로 정렬해준다. 그 후 아래의
 방법을 따라 반복문을 수행해주었더니 문제가 해결이 되었다.

 **-알고리즘 적용-**

1. 아래 그림과 같이, 현재 보고 있는 점의 y좌표와 변수 l에 저장된 값이 다른 경우, l을 y좌표로 갱신한 후 현재 l과 같은 값을
가진 배열의 점들의 x좌표의 위치를 update를 이용하여 세그먼트 트리를 갱신해준다.
![title](/assets/images/17131_1.png){: .align-center width="80%" height="80%"}

2. 현재 보고 있는 점의 x좌표의 오른쪽에 있는 점들의 개수를 구간의 점의 개수를 구하는 query를 이용하여
구해주고, 왼쪽에 있는 점들의 개수도 구해준다.
![title](/assets/images/17131_2.png){: .align-center width="80%" height="80%"}

3. answer에 오른쪽에 있는 점들의 개수와 왼쪽에 있는 점들의 개수를 곱한 값을 더해준다.

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
#include <map>
using namespace std;

vector<int> tree(400001*4);

//점들의 y좌표를 기준으로 비교하는 함수.
bool cmp(const pair<int,int> &p1,const pair<int,int> &p2){
    if(p1.second!=p2.second) return p1.second<p2.second;
    return p1.first<p2.first;
}

//세그먼트 구간합 트리 업데이트
void update(int idx,int value,int node,int left,int right){
    if(idx>right || idx<left) return;
    tree[node]+=value;
    int mid=(left+right)/2;
    if(left!=right){
        update(idx,value,node*2,left,mid);
        update(idx,value,node*2+1,mid+1,right);
    }
}

//구간에 점들의 개수를 얻는 질의
int query(int s,int e,int node,int left,int right){
    if(s>right || e<left) return 0;
    if(s<=left&&right<=e) return tree[node];
    int mid=(left+right)/2;
    return query(s,e,node*2,left,mid)+query(s,e,node*2+1,mid+1,right);
}

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);

    int n;
    cin>>n;
    vector<pair<int,int>> v(n);

    //점들을 입력받고, 해당 x좌표 위치의 값을 업데이트 해준다.
    for(int i=0;i<n;i++){
        cin>>v[i].first>>v[i].second;
        update(v[i].first+200000,1,1,0,400000);
    }

    //y좌표를 기준으로 오름차순 정렬
    sort(v.begin(),v.end(),cmp);

    long long answer=0;
    int l=-400001;
    for(int i=0;i<n;i++){

        int idx=v[i].first+200000;
        //현재 보고 있는 점과 같은 값을 지닌 y좌표들의 x좌표들을 -1로 업데이트.
        if(l!=v[i].second){
            l=v[i].second;
            for(int j=i;l==v[j].second && j<n;j++) update(v[j].first+200000,-1,1,0,400000);
        }
        //현재 점의 오른쪽 점의 개수 * 왼쪽 점들의 개수
        answer+=(long long)query(0,idx-1,1,0,400000)*(long long)query(idx+1,400000,1,0,400000);
        answer%=1000000007;
    }
    cout<<answer<<'\n';
}
```
