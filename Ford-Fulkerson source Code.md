
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
  

