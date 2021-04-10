---
layout: post
title: "BOJ #9019 : DSLR (BFS의 최단경로 트래킹)"
date:   2019-02-15 17:17:32
author: 변준석
categories: 알고리즘
comments: true
---

### DSLR
 * 링크 : https://www.acmicpc.net/problem/9019

#### 입력받기
프로그램 입력은 T 개의 테스트 케이스로 구성된다. 테스트 케이스 개수 T 는 입력의 첫 줄에 주어진다. 각 테스트 케이스로는 두 개의 정수 A와 B(A ≠ B)가 공백으로 분리되어 차례로 주어지는데 A는 레지스터의 초기 값을 나타내고 B는 최종 값을 나타낸다. A 와 B는 모두 0 이상 10,000 미만이다.

```c++
    for (int i = 0; i < n * m; i++)
    {   
        int * ptr = &node[i];
        scanf("%1d", ptr);
    }
```
#### BFS 최단경로 구하기. 
```c++
int parent[10000];
int history[10000];
```
`history`는 `pos`에 이동할 때 필요했던 노드를 갖는다. `parent`는 `pos`에 이동하기 직전의 노드의 값을 갖는다.


####  전체 코드
```c++
#include <cstdio>
#include <vector>
#include <cstring>
#include <queue>

using namespace std;

int num_testcase = 0;
int visited[10000];
int parent[10000];
int history[10000];
int n_before, n_after;
int size = 0;
int step = 0;

int InitVariables();
int bfs(int pos);
int function_d(int pos);
int function_s(int pos);
int function_l(int pos);
int function_r(int pos);

int main()
{
    scanf("%d", &num_testcase);
    while(num_testcase--)
    {
        InitVariables();    
        scanf("%d %d", &n_before, &n_after);
        bfs(n_before); 
        char answer[step];
        for (int i = 1; i <= step; i++)
        {
            answer[step - i] = history[n_after];
            n_after = parent[n_after];
        }
        for (int i = 0; i < step; i++)
        {
            if (answer[i] != 'X')
            {
                printf("%c", answer[i]);
            }
        }
        printf("\n");
    }
}

int InitVariables()
{
    step = 0;
    size = 0;
    memset(parent, 0, sizeof(parent));
    memset(history, 'X', sizeof(history));
    memset(visited, 0, sizeof(visited));
}

int bfs(int pos)
{
    queue<int> q;
    q.push(pos);
    visited[pos] = 1;
    while(!q.empty())
    {
        if (size == 0)
        {
            size = q.size();
        }
        // visit
        int now = q.front();
        q.pop();
        size--;
        if (size == 0)
        {
            step++;
        }
        if (now == n_after)
        {
            return 0;
        }
        int next[4] = {function_d(now), function_s(now), function_l(now), function_r(now)};
        char next_action[4] = {'D', 'S', 'L', 'R'};
        for (int i = 0; i < 4; i++)
        {
            if (visited[next[i]] == 0)
            {
                visited[next[i]] = 1;
                q.push(next[i]);
                history[next[i]] = next_action[i];
                parent[next[i]] = now;
            }
        }
    }
}

int function_d(int pos)
{
    if (pos * 2 >= 10000)
    {
        return (pos * 2) % 10000;
    }
    else
    {
        return pos * 2;
    }
}

int function_s(int pos)
{
    if (pos == 0)
    {
        return 9999;
    }
    else
    {
        return pos - 1;
    }
}

int function_l(int pos)
{
    if (pos == 0)
    {
        return 0;
    }
    else
    {
        int first = pos / 1000;
        int rem = pos % 1000;
        return rem * 10 + first;
    }
}

int function_r(int pos)
{
    if (pos == 0)
    {
        return 0;
    }
    else
    {
        int last = pos % 10;
        return pos / 10 + last * 1000;
    }
    
}
```