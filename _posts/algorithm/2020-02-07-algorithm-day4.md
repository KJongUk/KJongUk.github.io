---
title:  "백준 알고리즘 문제 - 1321번 군인"
excerpt: "세그먼트 트리"

categories:
  - algorithm
use_math: true
---

<https://www.acmicpc.net/problem/1321>
---

1.문제 설명
---
 문제는 N($1≦N≦500000$)개의 군부대에 군인을 배치하고, 질의가 주어지면 올바른 출력을 나오게 하면 되었다.
 질의는 "1 i a"형태로 주어질 경우 i번 군부대의 인원을 a명 감원/증원하고 "2 a"로 주어지는 경우는 a번째 병사가 
 몇 번 군부대에 위치하는지 출력을 해주면 된다.


2.문제 풀이
---
이 문제는 <https://www.acmicpc.net/problem/2243>과 거의 똑같았다. 구간합을 구하는 세그먼트 트리를 만들어 주고,
"1 i a"로 질의가 들어올 경우에는 세그먼트 트리를 업데이트 해준다. "2 a"로 들어올때는 1번째~k번째 군부대의 군인수의 합≦ a가 되는
k를 구해주면 되었다. 

예시: N=3, 1번 군부대: 3명, 2번 군부대: 4명, 3번 군부대: 7명

-세그먼트 트리-
**처음의 세그먼트 트리 상황**
![title](/assets/images/1321_1.png){: .align-center width="80%" height="80%"}

질의 : "1 1 -3" 1번 군부대의 사람수를 3명을 감원하면 된다.
**질의가 들어온 이후 세그먼트 트리 상황**
![title](/assets/images/1321_2.png){: .align-center width="80%" height="80%"}

질의: "2 6" 6번째 병사의 군부대 번호를 출력하기.
1. 세그먼트 트리의 리프노드가 아닌 경우.(left!=right)
   - 현재 세그먼트 트리의 노드의 왼쪽 자식이 6보다 큰 경우

    **세그먼트 트리의 왼쪽 자식으로 query를 진행**
  
   - 현재 세그먼트 트리의 노드의 오른쪽 자식이 6-왼쪽자식의 값보다 큰 경우

    **세그먼트 트리의 오른쪽 자식으로 query를 진행**

2. 세그먼트 트리의 리프노드인 경우(left==right)
   - left를 return 해줌 : 6번째 병사가 있는 군부대의 번호

![title](/assets/images/1321_3.png){: .align-center width="80%" height="80%"}

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

int ret=0;
vector<int> tree;

void update(int idx,int val,int node,int left,int right){
    if(idx>right || idx<left) return;
    tree[node]+=val;
    int mid=(left+right)/2;
    if(left!=right){
        update(idx,val,node*2,left,mid);
        update(idx,val,node*2+1,mid+1,right);
    }
}

int query(int key,int node,int left,int right){
    if(ret==0&&left==right){
        return left;
    }
    int mid=(left+right)/2;
    if(ret==0&& tree[node*2]>=key){
        return ret=query(key,node*2,left,mid);
    }
    key-=tree[node*2];
    if(ret==0&&tree[node*2+1]>=key){
        return ret=query(key,node*2+1,mid+1,right);
    }
}

int main(){
    ios_base::sync_with_stdio(false); 
    cin.tie(NULL); 
    cout.tie(NULL);
    
    int n;
    cin>>n;
    tree=vector<int>(n*4,0);
    for(int i=0;i<n;i++){
        int x;
        cin>>x;
        update(i,x,1,0,n-1);
    }
    
    int m;
    cin>>m;
    for(int i=0;i<m;i++){
        int a,b,c;
        cin>>a;
        if(a==1){
            cin>>b>>c;
            update(b-1,c,1,0,n-1);
        }
        else{
            cin>>b;
            cout<<query(b,1,0,n-1)+1<<'\n';
            ret=0;
        }
    }
}
```


