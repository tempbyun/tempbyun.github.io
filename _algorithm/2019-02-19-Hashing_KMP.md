---
layout: post
title: "BOJ #1786 : 찾기"
date:   2019-02-19 11:00:54
author: 변준석
categories: 알고리즘
comments: true
---


### Hashing
많은 양의 값을 해시 함수 H(x)에 대입하여 나온 결과를 인덱스로 하여 해시테이블에 저장하는 방식으로 값을 저장하는 자료구조.

* Collision : 서로 다른 값들의 해시값이 같게 되는 경우를 이야기함.
  * 다른 해시값에 대해서도 모두 저장가능하도록 각 칸이 BST나 Linked List 등의 형태를 띄게 됩니다.
* 해시테이블은 다른 자료구조들과 마찬가지로 값을 삽입, 삭제, 탐색하기 위해 존재합니다.
  * 이상적인 경우 삽입, 삭제, 탐색이 O(1)로 가능 ; 해시함수를 연산하는 시간복잡도
  * 인덱스를 찾기 위한 해시함수 연산 시간이 전체 시간복잡도를 반정도 지배함. 따라서 해시함수는 매우 빠른 시간 내에 작동하는 것이 좋음.
  * 충돌이 없는 경우 인덱스만 계산하면 바로 값을 바로 참조할 수 있으므로 O(1)이 실현되지만, 충돌하는 값이 많다면 해당 인덱스 값을 모두 뒤져보아야하니 시간이 늘어납니다.
* 충돌이 한 군데에만 편향적으로 심각하게 발생하여 효율을 떨어뜨리는 현상을 군집화 (Clustering)이라고 함. 심한 충돌을 피하기 위해서 좋은 해시함수를 설계하는 것이 중요함.



#### 라빈 카프 알고리즘(Rabin-Karp Algorithm)

KMP와는 작동방식이 완전히 달라서 응용하는 곳도 다름. 해싱을 직접 사용하여 일대일 매칭을 찾는다. 

* `T`에서 `P` 를 찾는다고 했을 때

  * 단순한 방법으로 찾는다고 한다면 O(NM)의 시간복잡도를 갖게 됨.

  * 라빈카프 알고리즘은 문자열을 모든 위치에서 비교해보기는 하지만, **먼저 해당 위치의 해시값을 비교하고 같다면 그제서야 단순비교를 함**

    

  ```c++
  #include <cstdio>
  #include <iostream>
  #include <string>
  #include <vector>
  
  using namespace std;
  
  const int MAX_LEN = 1000000;
  
  int main()
  {
      string T;
      string P;
      getline(cin, T);
      getline(cin, P);
      int len_T = T.size();
      int len_P = P.size();
      int G = 0;
      int H = 0;
      int power = 1;
      vector<int> result;
      for (int i = 0; i <= len_T; i++)
      {
          if (i == 0)
          {
              for (int j = 0; j < len_P; j++)
              {
                  G = G + 1LL * T[len_P - 1 - j] * power;
                  H = H + 1LL * P[len_P - 1 - j] * power;
                  if (j < len_P - 1)
                  {
                      power = mod (power * 2);
                  }
              }
          }
          else
          {
              G =2 * (G - 1LL * T[i - 1] * power) + T[i + len_P - 1];
          }
          if (G == H)
          {
              bool same = true;
              for (int j = 0; j < len_P; j++)
              {
                  if (T[i + j] != P[j])
                  {
                      same = false;
                      break;
                  }
              }
              result.push_back(i + 1);
          }
      }
      printf("%d\n", result.size());
      for (int i : result)
      {
          printf("%d ", i);
      }
  }
  ```

  

#### KMP 알고리즘(Knuth–Morris–Pratt Algorithm) 

* O(N + M)의 시간복잡도를 가진 알고리즘이다.

* 실패함수 (Failure Function) : KMP에서는 문자열을 비교하다가 찾는 문자열과 다르다는 것을 알게 되었을 때 실패함수라는 값을 참고하여 다음 비교 위치를 정합니다.

  * pi[x] : 검색하려는 문자열 P의 0 번째 ~ x 번째 위치까지 볼 때 접두사(prefix) == 접미사(suffix)가 되는 최대 접두사(or 접미사) 길이입니다.

    T =  `ABABBABABCABB`, P = `ABABC` 라고 가정.

  | X    | P(i ~ X)    | pi[X] |
  | ---- | ----------- | ----- |
  | 0    | A           | 0     |
  | 1    | AB          | 0     |
  | 2    | **A**B**A** | 1     |
  | 3    | **ABAB**    | 2     |
  | 4    | ABABC       | 0     |

  > 예를들어 P가 `AKAKA`였을 때 pi[4]는 1이 아니라 3이다. 길이의 반을 넘는 경우도 카운트하기 때문이다.

  ```c++
  #include <cstdio>
  #include <vector>
  #include <string>
  #include <iostream>
  using namespace std;
  
  int fail[1000004];
  string T;
  string P;
  int lenT, lenP;
  
  int main() 
  {
  	getline(cin, T);
  	getline(cin, P);
  	lenT = T.size();
  	lenP = P.size();
  	int cmp = 0;
  	
  	for (int i = 1; i < lenP; i++) 
  	{
  		while (cmp > 0 && P[i] != P[cmp])
  		{
  			cmp = fail[cmp-1];
  		}
  		if (P[i] == P[cmp])
  		{
  			fail[i] = ++cmp;
  		}
  	}
  	vector<int> ans;
  	cmp = 0;
  	
  	for (int i = 0; i < lenT; i++) 
  	{
  		while (cmp > 0 && T[i] != P[cmp])
  		{
  			cmp = fail[cmp - 1];
  		}
  		if (T[i] == P[cmp]) 
  		{
  			if (cmp == lenP - 1) 
  			{
  				ans.push_back(i - cmp + 1);
  				cmp = fail[cmp];
  			}
  			else
  			{
  				cmp++;
  			}
  		}
  	}
  	// print answer
  	printf("%d\n", ans.size());
  	for (auto i : ans)
  	{	
  		printf("%d ", i);	
  	}
  }
  ```

  