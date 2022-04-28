## 네트워크 유량(Network Flow) 
Source(시작점)-> Sink(도착점)으로 동시에 보낼 수 있는, 데이터나 사물의 최대 양을 구하는 알고리즘이다.  
즉 그래프에서 두 정점 사이에 얼마나 많은 유량을 보낼 수 있는지 계산하는 알고리즘이다.

## 용어정리
Source = 시작점  
Sink = 도착점  
Capapcity = 용량 (c(u,v)는 정점 u에서 v로 가는 간선의 용량이다)  
Flow = 유량 (f(u,v)는 정점 u에서 v로의 간선에 실제로 흐르는 유량을 의미)  
Residual Capacity = 잔여 용량 (간선의 용량과 유량의 차이를 의미)  

## 네트워크 유량의 세가지 속성
### 1. 용량 제한 속성
각 간선의 유량은 해당 간선의 용량을 초과할 수 없다.(f(u,v)<=c(u,v)
### 2. 유량의 대칭성
u에서 v로 유량이 흘러올 경우, v의 입장에서는 u로 음수의 유량을 보내는것이라 생각한다.(f(u,v)=-f(v,u))
### 3. 유량의 보존
각 정점에 들어오는 유량과 나가는 유량의 양은 정확히 같아야 한다.  
유량의 대칭성에 의해서 정점에 들어오는 유량은 모두 음수로 표현되니, 한 정점에서 다른 모든 정점에 보내는 유량을 합하면 항상 0이 되어야 한다.

## 포드-풀커슨 알고리즘
동작원리: DFS를 통해서 sink로 flow가 흐를 수 있는 경로를 찾는다. 이때 이를 증가 경로라 한다.  
증가 경로가 없을 때까지 flow를 흘러보내주고, 마지막으로 source가 sink로 흘려보낸 flow가 최대한 흘러보낼 수 있는 플로우를 의미하게 된다.
### 작동방식
![image](https://user-images.githubusercontent.com/101376839/165795186-ae7ad653-d5fd-4f54-bca7-e64915539448.png)  
이러한 그래프가 주어졌을 때 S->1->2->T로 증가 경로를 먼저 찾는다. 이때는 flow가 1이다.  
![image](https://user-images.githubusercontent.com/101376839/165795441-fd2c5668-924c-4ca6-8d8f-6dce05638e58.png)  
flow를 흘려보낸뒤, 또 다른 경로로 flow를 흘려보낸다. 그러나 증가경로가 더 이상 존재하지 않는 것을 알 수 있다.  
이때 네트워크 유량의 속성인 유량의 대칭성을 이용하여서 반대로 flow를 흘려보낼 수 있다. 예를 들어 방금전에 1->2로 1만큼의 flow를 흘려보냈으면 2->1로 -1만큼의 flow를 흘려보내는 것도 가능하다는 이야기다.  
그런식으로 하면 아래와 같은 간선이 추가가 된다.  

![image](https://user-images.githubusercontent.com/101376839/165796217-64c53e2a-57fc-42c9-9a40-e4d1ef601994.png)  
이렇게 flow를 흘려보낸만큼 아래처럼 역 간선으로 flow를 흘러보낼 수 있다.
![image](https://user-images.githubusercontent.com/101376839/165796396-088694da-1b74-44f4-8c6b-1f358797e9ad.png)  
간선이 추가가 되었기 때문에 flow를 더 흘러보낼 수 있고 2라는 유량을 흘러보낼 수 있게된다.

### 문제점
![image](https://user-images.githubusercontent.com/101376839/165797023-5aa20789-ede5-49c7-9ad4-693403511151.png)  
그러나 위의 그래프처럼 주어진다면 증가경로 한개당 flow를 1밖에 보낼 수 없기 때문에 DFS를 flow 수 만큼 반복해야 하니 비효율적이다. 
### 해결책
의외로 해결책은 단순하게도 경로 탐색을 할때 DFS방식이 아닌 BFS 방식으로 탐색하면은 해결이 된다. 그리고 우리는 BFS 방식으로 탐색을 할때 포드-풀커슨의 에드몬드 카프 알고리즘이라고 말한다.

### 시간 복잡도
위에서 말한 것처럼 포드-풀커슨 알고리즘의 방식대로 하면 시간 복잡도는 O(V+E)F)가 나오고  
에드몬드 카프 알고리즘을 따르면 O(VE^2)이 나온다.  
그러나 항상 이 에드몬드 카프 알고리즘이 효율적인 것은 아님을 기억해야한다.

### 실생활에서의 사용
실제로 이러한 최대 유량 알고리즘은 전력,수도,통신 등 다양한 분야에서 폭넓게 쓰이고 있다.

### 실제 코드 및 실행 결과

      #include <iostream>
      #include <vector>
      #include <queue>
 
      #define MAX 100
      #define INF 1000000000
 
      using namespace std;
 
      int n = 6; //정점의 수
      int C[MAX][MAX]; //용량의 배열
      int F[MAX][MAX]; //현재 유량의 배열
      int visited[MAX]; //현재의 정점 방문 여부
      int result; //최대 유량의 결과
 
      vector<int> adj[MAX]; // 정점이 어느 정점으로부터 방문됬는지 기록하는 배열
 
      void maxFlow(int source, int sink)//최대 유량 계산하는 함수 {
 
      while (1) {
 
          fill(visited, visited + MAX, -1); // 현재 정점 방문 배열을 -1로 바꿈
 
          queue<int> q;
          q.push(source);
 
          while (!q.empty())// source에서 sink로 최단으로 도달하는 경로 찾기 {
 
              int start = q.front();
              q.pop(); // queue의 front에 위치한 정점 추출
 
              for (int i = 0; i < adj[start].size(); i++) // queue 의 front 에 위치한 정점과 인접한 정점 탐색 {
 
                  int end = adj[start][i]; // 인접 정점
 
                  if (C[start][end] - F[start][end] > 0 && visited[end] == -1) { 
                      // 인접 정점으로부터 연결된 경로들을 확인하기 위해, 인접 정점을 queue 에 삽입
                      q.push(end);
                      // 인접 정점이 어느 정점으로부터 연결되어있는지 기억
                      visited[end] = start; 
                      // 인접 정점이 도착지일 경우, 하나의 증가 경로를 찾았으므로, 더 이상 탐색하지 않고 종료
                      if (end == sink) break; 
                  }
              }
          }
 
          // sink 까지의 경로를 못 찾았다면, 더 이상의 증가 경로가 없음
          if (visited[sink] == -1) break;
 
          // 연결된 경로들을 도착지점부터 반대로 모두 확인하며, 최소 유량을 탐색
          int f = INF;
          for (int i = sink; i != source; i = visited[i]) {
              // min(최소유량, 용량(이전노드, 현재노드) - 유량(이전노드, 현재노드))
              f = min(f, C[visited[i]][i] - F[visited[i]][i]);
          }
 
          // 증가 경로는 유량 증가, 역 방향 경로는 유량 감소
          for (int i = sink; i != source; i = visited[i]) {
              // 유량(이전노드, 현재노드) += f
              F[visited[i]][i] += f;
              // 유량(현재노드, 이전노드) -= f
              F[i][visited[i]] -= f;
          }
 
          // 최대 유랑 추가
          result += f;
      }
      }
 
      int main() {
 
      adj[1].push_back(2); 
      adj[2].push_back(1);  
      C[1][2] = 14; 
 
      adj[1].push_back(4);
      adj[4].push_back(1);
      C[1][4] = 12;
 
      adj[2].push_back(3);
      adj[3].push_back(2);
      C[2][3] = 5;
 
      adj[2].push_back(4);
      adj[4].push_back(2);
      C[2][4] = 4;
 
      adj[2].push_back(5);
      adj[5].push_back(2);
      C[2][5] = 6;
 
      adj[2].push_back(6);
      adj[6].push_back(2);
      C[2][6] = 10;
 
      adj[3].push_back(6);
      adj[6].push_back(3);
      C[3][6] = 8;
 
      adj[4].push_back(5);
      adj[5].push_back(4);
      C[4][5] = 11;
 
      adj[5].push_back(3);
      adj[3].push_back(5);
      C[5][3] = 4;
 
      adj[5].push_back(6);
      adj[6].push_back(5);
      C[5][6] = 7;
 
      maxFlow(1, 6);
 
      printf("최대 유량 : %d\n", result);
      }
  
 








