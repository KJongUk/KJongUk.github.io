---
title:  "백준 알고리즘 문제 - 15686번 치킨 배달"
excerpt: "완전 탐색-난이도 골드 5"

categories:
  - algorithm
use_math: true
---

<https://www.acmicpc.net/problem/15686>
---

1.문제 설명
---
크기가 N\*N($1≦N≦50$)인 도시가 주어지고, 각 도시는 1이면 집, 2면 치킨집, 0이면 빈칸이다. 이 때 문제
에서 요구하는 것은 치킨집을 최대 M($1≦M≦13$)개를 선택했을때 선택한 치킨집들과 집까지의 거리들의 최소합을
구하는 문제였다. 이 때 도시의 치킨집은 최대 13개까지 주어질 수 있다. 어떤 집과 치킨집들의 거리는 선택한
치킨집들 중 집과 가장 짧은 거리에 있는 치킨집까지의 거리이다. 거리는 집의 좌표를 ($r_1,c_1$)이라고 하고,
치킨 집의 좌표를 ($r_2,c_2$)라고 하면 거리 d=$|r_1-r_2|+|c_1-c_2|$이다.

2.문제 풀이
---
치킨집의 개수가 최대 13개이므로, 완전 탐색 즉 브루트 포스를 이용하여 M개 이하인 도시 상의 치킨 집을 선택 해준다.
그 후 선택한 치킨집의 개수가 M개 이하면, 도시의 모든 집과의 최소 거리들의 합을 구해준 다음 결과를 갱신해준다.
시간 복잡도는 치킨집의 개수를 선택하는 것 2<sup>13</sup>, 집들의 개수는 최대 100개이므로, 총 시간 복잡도는 다음과 같다.

**시간복잡도=2<sup>13</sup>\*100\*13**
---

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

int n,m;
vector<pair<int,int>> v;//치킨 집의 위치
vector<pair<int,int>> arr;//도시의 집들의 위치
bool visited[13];//선택한 치킨집들을 표시하기위한 bool 배열

int answer=987654321;//집과 선택한 치킨 집들간의 거리의 최소합.

//재귀적인 형식으로 완전탐색을 수행하는 함수.
void dfs(int depth,int size){
    //depth: 현재 선택하고 있는 치킨 집, size: 선택한 치킨집의 수
    if(size<=m){
        int range=0;
        for(int i=0;i<arr.size();i++){
            int minr=1000000;
            for(int j=0;j<13;j++){
                if(visited[j]){//선택한 치킨집들 중 현재 집과 가장 가까운 치킨집의 거리를 갱신
                    int r=abs(v[j].first-arr[i].first)+abs(v[j].second-arr[i].second);
                    minr=min(minr,r);
                }
            }
            range+=minr;
        }
        answer=min(range,answer);
    }
    else return;
    //재귀적으로 수행 (완전 탐색)
    for(int i=depth;i<v.size();i++){
        visited[i]=true;
        dfs(i+1,size+1);
        visited[i]=false;
    }
}

int main(){
    ios_base :: sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);

    cin>>n>>m;

    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++){
            int x;
            cin>>x;
            if(x==1) arr.push_back({i,j}); //1이면 집
            else if(x==2) v.push_back({i,j}); //2면 치킨집
        }

    dfs(0,0);
    cout<<answer<<'\n';
}
```
