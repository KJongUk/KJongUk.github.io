---
title:  "백준 알고리즘 문제 - 15816번 퀘스트 중인 모험가"
excerpt: "좌표 압축,오프라인 쿼리,세그먼트 트리"

categories:
  - algorithm
use_math: true
---

<https://www.acmicpc.net/problem/15816>
---

1.문제 설명
---
 간단히 설명하면 문제에서 요구하는 것은 $-10^9~10^9$의 정수번호에 퀘스트가 부가 되어있다. N($1≦N≦10^6$)개의 미리 성공한 퀘스트의
 수가 주어지고 성공한 퀘스트의 정수 번호들을 입력 받는다. 그 후 M($1≦M≦10^6$)개의 질의를 받는다. 이때 질의의 타입이 1인 경우 X 정수번호의
 퀘스트를 성공했다는 걸 알려주는 질의이고, 타입이 2인 경우 L~R까지 성공하지 못한 퀘스트의 개수를 출력하는 질의이다.

2.문제 풀이
---
 문제는 성공한 질의 타입 2인 경우에는 L~R 안의 성공한 퀘스트의 수를 구한후 L-R+1-성공한 퀘스트의 수 를 출력 한다면 쉽게 풀릴 문제였다.
 하지만 퀘스트의 정수 범위가 너무 커서 좌표압축이 필요한 문제였고, 좌표 압축을 하기 위해서 앞으로 성공할 퀘스트들을 미리 처리 해주는 즉
  질의들을 미리처리 해주는 오프라인 쿼리를 이용해야하는 문제였다. **좌표 압축**이란 간단히 이야기하면 1차원 좌표상의 점을 왼쪽부터 보았을 때
  맨 처음 나오는 점의 index를 0으로 하고, 그다음 점의 index를 1로 하고...즉 점들의 순서에 번호를 매긴다고 생각하면 된다. 이 때 점들의 순서에
  번호를 매겼으면 당연히 번호를 통해 원래 점의 좌표값을 알 수 있는 과정도 필요하다.

- 압축 하는 방법(점의 좌표 -> index)
``` cpp
    for(int i=0;i<n;i++){
        int x;//점의 좌표값
        cin>>x;
        point.push_back(x);
   }
``` 

- 압축을 푸는 방법(점의 좌표를 통해 압축된 index를 얻는 방법)
``` cpp
  int idx=lower_bound(point.begin(),point.end(),x)-point.begin();
```

**-알고리즘 적용-**

1. 미리 성공한 퀘스트와 질의에서 앞으로 성공할 퀘스트들을 point배열에 미리 담아 둔다.
2. 미리 성공한 퀘스트들의 좌표에 해당하는 index에서 구간합을 구하는 세그먼트 트리를 업데이트 해준다.
3. 질의를 처리해준다.
  - 질의 타입이 1인 경우: updated 되어있는 좌표인 경우는 건너띄고, 해당 좌표의 index를 구한후 세그먼트 트리를 업데이트
  - 질의 타입이 2인 경우: 예외처리가 필요하므로 예외처리를 해주고 R-L+1-세그먼트 트리의 구간합.

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

struct quer{
    int n,a,b;
    quer(int _n,int _a,int _b=0):n(_n),a(_a),b(_b){}
};

vector<int> tree;
bool updated[2000001];

void update(int idx,int val,int node,int left,int right){
    if(idx<left || idx>right) return;
    tree[node]+=val;
    int mid=(left+right)/2;
    if(left!=right){
        update(idx,val,node*2,left,mid);
        update(idx,val,node*2+1,mid+1,right);
    }
}

int query(int start,int end,int node,int left,int right){
    if(start>right || end<left) return 0;
    if(start<=left && right<=end) return tree[node];
    int mid=(left+right)/2;
    return query(start,end,node*2,left,mid)+query(start,end,node*2+1,mid+1,right);
}

int main(){
    ios_base::sync_with_stdio(false); 
    cin.tie(NULL); 
    cout.tie(NULL);
    
    int n,m;
    cin>>n;
    
    vector<int> point;
    vector<int> xxx;
    int minquest=1000000001;
    int maxquest=-1000000001;
    for(int i=0;i<n;i++){
        int x;
        cin>>x;
        point.push_back(x);
        xxx.push_back(x);
        minquest=min(minquest,x);
        maxquest=max(maxquest,x);

    }
    
    cin>>m;
    vector<quer> q;
    for(int i=0;i<m;i++){
        int a,b,c;
        cin>>a;
        if(a==1){
            cin>>b;
            q.push_back(quer(a,b));
            point.push_back(b);
        }
        else{
            cin>>b>>c;
            q.push_back(quer(a,b,c));
        }
    }
    sort(point.begin(),point.end());
    point.erase(unique(point.begin(), point.end()), point.end());
    
    tree=vector<int>(point.size()*4,0);
    int size=point.size();
    
    for(int i=0;i<xxx.size();i++){
        int idx=lower_bound(point.begin(),point.end(),xxx[i])-point.begin();
        update(idx,1,1,0,size-1);
        updated[idx]=1;
    }
    
    for(int i=0;i<q.size();i++){
        if(q[i].n==1){
            int idx=lower_bound(point.begin(),point.end(),q[i].a)-point.begin();
            
            minquest=min(minquest,q[i].a);
            maxquest=max(maxquest,q[i].a);
            
            if(updated[idx]) continue;
            
            update(idx,1,1,0,size-1);
        }
        else{
            
            int l=lower_bound(point.begin(),point.end(),q[i].a)-point.begin();
            int h=upper_bound(point.begin(),point.end(),q[i].b)-point.begin();            
            if(q[i].b < minquest || q[i].a > maxquest) cout<<q[i].b-q[i].a+1<<'\n';
            else cout<<q[i].b-q[i].a+1-query(l,h-1,1,0,size-1)<<'\n';
        }
    }
}
```
