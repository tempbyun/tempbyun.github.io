---
layout: post
title: "BOJ #1697 : 숨바꼭질 (BFS의 최단경로 길이)"
date:   2019-01-15 17:21:13
author: 변준석
categories: 알고리즘
comments: true
---

### 숨바꼭질
 * 링크 : https://www.acmicpc.net/submit/1697
 * 수빈이는 동생과 숨바꼭질을 하고 있다. 수빈이는 현재 점 N(0 ≤ N ≤ 100,000)에 있고, 동생은 점 K(0 ≤ K ≤ 100,000)에 있다. 수빈이는 걷거나 순간이동을 할 수 있다. 만약, 수빈이의 위치가 X일 때 걷는다면 1초 후에 X-1 또는 X+1로 이동하게 된다. 순간이동을 하는 경우에는 1초 후에 2*X의 위치로 이동하게 된다.
수빈이와 동생의 위치가 주어졌을 때, 수빈이가 동생을 찾을 수 있는 가장 빠른 시간이 몇 초 후인지 구하는 프로그램을 작성하시오.
* 입력 : 첫 번째 줄에 수빈이가 있는 위치 N과 동생이 있는 위치 K가 주어진다. N과 K는 정수이다.
    ```bash
    5 17
    ```

#### 전체 코드
```c++
#include <cstdio>
#include <vector>
#include <queue>

using namespace std;

const int MAX_NODE = 100001;
int n, k;
int answer = 0;
int visited[MAX_NODE];
vector<int> edge[MAX_NODE];

int bfs(int pos);

int main()
{
    scanf("%d %d", &n, &k);
    for(int i = 0; i < MAX_NODE - 1; i++)
    {
        edge[i].push_back(i + 1);
        edge[i + 1].push_back(i);
    }
    for(int i = 1; i <= MAX_NODE/2; i++)
    {
        edge[i].push_back(2 * i);
    }
    printf("%d", bfs(n));
}   


int bfs(int pos)
{   
    queue<int> q;
    q.push(pos);
    visited[pos] = 1;
    int size = 0;

    while(!q.empty())
    {
        if (size == 0) size = q.size();
        int now  = q.front();
        q.pop();
        size -= 1;

        if (now == k)
        { 
            return answer;
        }

        if (size == 0){
            answer += 1;
            }

        for (auto next : edge[now])
        {
            if (visited[next] == 0)
            {   
                visited[next] = 1;
                q.push(next);
            }
        }
    }
}
```