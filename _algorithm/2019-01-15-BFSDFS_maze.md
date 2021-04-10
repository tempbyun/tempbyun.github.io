---
layout: post
title: "BOJ #2178 : 미로찾기 (BFS의 최단경로 길이)"
date:   2019-01-15 15:03:31
author: 변준석
categories: 알고리즘
comments: true
---

### 미로 탐색
 * 링크 : https://www.acmicpc.net/problem/2178
 * 미로를 입력받아서 (1, 1)부터 (m, n) 까지 가는 최단경로의 길이를 구하는 문제입니다. 시간복잡도의 문제상 **DFS**를 사용할 수가 없는 경우입니다. **BFS**를 통해 해결할 수 있습니다.

#### 입력받기
"첫째 줄에 두 정수 N, M(2 ≤ N, M ≤ 100)이 주어진다. 다음 N개의 줄에는 M개의 정수로 미로가 주어진다. 각각의 수들은 **붙어서** 입력으로 주어진다." 라는 조건이 주어진다. **붙어서** 숫자를 입력받아야 되므로 아래와 같이 하였다. 물론 `char`로 입력받았다면 `\n` 을 처리하는 것까지 생각했어야 했을 것이다.

```c++
    for (int i = 0; i < n * m; i++)
    {   
        int * ptr = &node[i];
        scanf("%1d", ptr);
    }
```
위와같이 입력받았다. 물론 `ptr`을 그 전에 선언하고 `ptr++` 해도 됐겠지만.. 

#### BFS 및 최단경로의 길이 구하기. 
```c++
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

        if (now == n * m - 1)
        { 
            return answer + 1;
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
여기서 `size` 변수가 해당 깊이에 해당되는 노드의 개수를 의미한다. 해당 깊이에 처음으로 도달할 때의 `queue` 의 사이즈를 담는다.


####  전체 코드
```c++
#include <cstdio>
#include <vector>
#include <cstring>
#include <queue>

using namespace std;

const int MAX_NODE = 10001;
int node[MAX_NODE];
int visited[MAX_NODE];
vector<int> edge[MAX_NODE];
int n, m;
int answer = 0;

int check(int pos);
int bfs(int pos);

int main()
{   
    memset(node, 0, sizeof(node));
    memset(node, 0, sizeof(visited));

    scanf("%d %d", &n, &m);
    
    for (int i = 0; i < n * m; i++)
    {   
        int * ptr = &node[i];
        scanf("%1d", ptr);
    }
    
    for (int i = 0; i < m * n; i++) check(i);
    printf("%d", bfs(0));

}


int check(int pos)
{
    int posup    = pos - m;
    int posdown  = pos + m;
    int posleft  = pos - 1;
    int posright = pos + 1;
    int mY       = pos / m;
    if (node[pos])
    {
        if (posup >= 0 && node[posup])
        {
            edge[pos].push_back(posup);
        }
        if (posleft >= 0 && posleft / m == mY && node[posleft])
        {
            edge[pos].push_back(posleft);
        }
        if (posright < m*n && posright / m == mY && node[posright])
        {
            edge[pos].push_back(posright);
        }
        if (posdown < n*m && node[posdown])
        {
            edge[pos].push_back(posdown);
        }
    }
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

        if (now == n * m - 1)
        { 
            return answer + 1;
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