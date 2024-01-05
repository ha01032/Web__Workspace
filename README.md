# 🧩(Week 07) 다익스트라 알고리즘

## - **다익스트라 알고리즘(Dijkstra Algorithm)** 정의

**음의 가중치**(음의 간선, 음의 값)**가 없는 그래프의 한 노드**에서 **각 모든 노드까지의 최단거리를 구하는 알고리즘**

cf. Floyd-Warshall: 음의 가중치가 없는 그래프의 가능한 모든 노드 쌍(pair)에 대한 최단거리

cf. Bellman-Ford: 음의 가중치가 있는 그래프의 한 노드에서 각 모든 노드까지의 최단거리

## - 매커니즘

- **기본적으로 그리디 알고리즘**이자 **다이나믹 프로그래밍 기법을 사용한 알고리즘**
    
    ⇒아래 두단계 반복해서 시작노드에서 각 모든 노드까지의 최단비용(거리) 구함
    
    (1) **방문하지 않은 노드** **중**에서 **가장 비용이 적은 노드를 선택**한다(그리디 알고리즘)
    
    (2) **해당 노드로부터 갈 수 있는 노드들의 (시작점으로 부터의) 최소비용을 갱신**한다(다이나믹 프로그래밍).
    
- 매커니즘 구현을 위해, 두단계 필요
    1. 초기화 단계
        - 최소거리이므로 최대값(INF)으로 각 노드에 대한 최단비용(거리) 배열 초기화
        - 시작노드에서 시작노드로 가는 최단거리 0으로 초기화
    2. 위에 언급한 두단계 알고리즘 모든 노드 반복할때까지 적용

![https://blog.aos.sh/img/DAGIF.gif](https://blog.aos.sh/img/DAGIF.gif)

## - 매커니즘 구현

![Uploading 표.png…]()

**⇒ 웬만하면 우선순위큐 방식 무조건 쓰자!!**

### 1. 인접행렬 활용 방식

: 모든 노드들을 둘씩 비교해서 시작점으로부터의 최소비용 갱신

```java
package graph;

import java.util.Arrays;
import java.util.Scanner;

public class Dijkstra {

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int V = sc.nextInt();
		int E = sc.nextInt();
		int[][] G = new int[V][V];
		for (int i = 0; i < E; i++) {
			G[sc.nextInt()][sc.nextInt()] = sc.nextInt();
		}
		int[] D = new int[V];
		Arrays.fill(D, Integer.MAX_VALUE);
		boolean[] visited = new boolean[V];

		D[0] = 0;
		for (int i = 0; i < V - 1; i++) {
			int min = Integer.MAX_VALUE;
			int index = -1;
			for (int j = 0; j < V; j++) {
				// 아직 방문하지 않았으면서, 최단 거리라면
				if (!visited[j] && min > D[j]) {
					index = j;
					min = D[j];
				}
			}

			for (int j = 0; j < V; j++) {
				// 아직 방문하지 않았으면서 경로가 존재하고, index까지의 거리 + index부터 j까지의 거리가 D[j]보다 작다면
				if (!visited[j] && G[index][j] != 0 && D[index] != Integer.MAX_VALUE
						&& D[index] + G[index][j] < D[j]) {
					D[j] = D[index] + G[index][j];
				}
			}
			visited[index] = true;
		}
		System.out.println(Arrays.toString(D));
	}

}
```

### 2. 순차탐색 그리디 방식

:  방문 안했으면서 최소비용배열에서 가장 작은 값 가진 노드 탐색하는 로직을 모든 노드 탐색하면서 수행→ 따라서 그리디를 활용했지만 시간복잡도가 줄어들지 않음.

```java
package com.algorithm.dijkstra;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.lang.reflect.Array;
import java.util.*;

public class DijkstraEx01 {
    static int n; // 노드 개수
    static int m; // 간선 개수

    static int[] D; // 최소비용 저장
    static boolean[] visited; // 방문 여부 저장
    static List<int[]>[] graph; // [간선, 가중치] 저장

    public static void main(String[] args) throws IOException {
        // 가장 빠른 길 찾기

        int max = Integer.MAX_VALUE;
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st;

        st = new StringTokenizer(br.readLine());
        n = Integer.parseInt(st.nextToken()); //노드
        m = Integer.parseInt(st.nextToken()); //간선
        int start = Integer.parseInt(st.nextToken()); //시작 노드

        G = new ArrayList[n + 1];
        D = new int[n + 1];
        visited = new boolean[n + 1];

        Arrays.fill(D, Integer.MAX_VALUE);
        for(int i = 0; i < n + 1; i++){
            G[i] = new ArrayList();
        }

        for (int i = 0; i < m; i++) {
            st = new StringTokenizer(br.readLine());

            int node = Integer.parseInt(st.nextToken());
            int a = Integer.parseInt(st.nextToken()); // a : node와 연결된 노드
            int b = Integer.parseInt(st.nextToken()); // b로 가는 비용

            G[node].add(new int[]{a, b});
        }

		// 다익스트라 알고리즘 시작
        dijkstra(start);
		
        // 출력
        for (int i : D) {
            if(i == Integer.MAX_VALUE)
                System.out.println("inf");
            else
                System.out.println(i);
        }
    }
    public static int getSmallestNode(){
        int min = Integer.MAX_VALUE;
        int idx = 0;

        for (int i = 1; i < n + 1; i++) {
            if(D[i] < min && !visited[i]){
                min = D[i];
                idx = i;
            }
            System.out.println(Arrays.toString(D));
        }
        return idx;
    }

    public static void dijkstra(int start){
    
        // 시작 노드에 대해서 초기화
        D[start] = 0;
        visited[start] = true;
        List<int[]> info = G[start];
        
        Iterator iterator = info.iterator();
        while(iterator.hasNext()){
            int[] v = (int[]) iterator.next();
            System.out.println(Arrays.toString(v));
            D[v[0]] = v[1]; //[0] : 노드, [1] : 거리
        }

        for (int i = 0; i < n - 1; i++) {
            int now = getSmallestNode();
            visited[now] = true;

            int vertexSIZE = G[now].size();
            for (int j = 0; j < vertexSIZE; j++) {
                int cost = D[now] + G[now].get(j)[1]; // 현재 노드를 거쳐갈 경우
				
                // 현재 노드를 거쳐거 갈 경우와 다른 경로로 왔을 경우 값 비교
                // 현재 노드를 거쳐갈 경우가 더 적으면 노드의 가중치값을 변경
                if(cost < D[G[now].get(j)[0]])
                    D[G[now].get(j)[0]] = cost;
            }
        }
    }
}
```

### 3. 우선순위큐 그리디 방식

: 우선순위큐에 각 노드 인덱스와 최소비용값을 넣어주고, 최소비용이 적은 배열이 먼저나오도록 기준을 만들어줌으로서 그리디 구현

: 우선순위큐 삽입과 추출로 인해서 NlogN의 시간복잡도가 발생하였으며, 주변노드를 무차별적으로 우선순위큐에 넣고 검사를 하면 삽입과 추출이 많이 발생할 수 있으므로(NlogN 보장 안됨) 주의

최소 비용으로 뽑은 노드의 **방문 체크를 하지 않는 경우**와 **갱신이 이루어 지지 않는 노드까지 우선 순위큐에 넣는 경우** 모두 **중복된 노드를 재 방문 하게 되는 문제가 발생** 

```java
package graph;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.Scanner;

public class Dijkstra_pq {
	
	static class Edge implements Comparable<Edge> {
		int v, weight;

		public Edge(int v, int weight) {
			this.v = v;
			this.weight = weight;
		}

		@Override
		public int compareTo(Edge o) {
			// TODO Auto-generated method stub
			return Integer.compare(this.weight, o.weight);
		}

		@Override
		public String toString() {
			return weight + "";
		}

	}

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int V = sc.nextInt();
		int E = sc.nextInt();
		List<Edge>[] G = new ArrayList[V];
		for (int i = 0; i < V; i++)
			G[i] = new ArrayList<>();
		for (int i = 0; i < E; i++) {
			// 첫번째가 출발지, 두번째가 도착지, 세번째가 가중치ㅋ
			G[sc.nextInt()].add(new Edge(sc.nextInt(), sc.nextInt()));
		}
		//
		// dijkstra
		PriorityQueue<Edge> pq = new PriorityQueue<>();
		boolean[] visited = new boolean[V];
		Edge[] D = new Edge[V];
		// 0번에서 출발하는걸로.
		for (int i = 0; i < V; i++) {
			// 원하는 출발지
			if (i == 0) {
				D[i] = new Edge(i, 0);
			} else {
				D[i] = new Edge(i, Integer.MAX_VALUE);
			}
			pq.add(D[i]);
		}
		while (!pq.isEmpty()) {
			Edge edge = pq.poll();

			for (Edge next : G[edge.v]) {
				// visited되지 않았으면서, D[next.v]가 D[edge.v] + next.weight 보다 더 크다면 갱신
				if (!visited[next.v] && D[next.v].weight > D[edge.v].weight + next.weight) {
					D[next.v].weight = D[edge.v].weight + next.weight;
					// decrease key
					pq.remove(D[next.v]);
					pq.add(D[next.v]);
				}
			}
			visited[edge.v] = true;
		}
		System.out.println(Arrays.toString(D));
	}
}
```

-방문체크 배열(visited) 안쓰고도 가능

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.Scanner;

class Edge implements Comparable<Edge> {
  public int vex;
  public int cost;

  Edge(int vex, int cost) {
    this.vex = vex;
    this.cost = cost;
  }

  @Override
  public int compareTo(Edge ob) { // 길이를 오름차순으로 정렬
    return this.cost - ob.cost;
  }
}

public class Dijkstra {
  static int n, m;
  static ArrayList<ArrayList<Edge>> G;
  static int[] D;

  public static void main(String[] args) {
    Dijkstra T = new Dijkstra();
    Scanner sc = new Scanner(System.in);
    n = sc.nextInt();
    m = sc.nextInt();
    G = new ArrayList<ArrayList<Edge>>();
    for (int i = 0; i <= n; i++) {
      G.add(new ArrayList<Edge>());
    }
    D = new int[n + 1];
    Arrays.fill(D, Integer.MAX_VALUE);
    for (int i = 0; i < m; i++) { // 그래프를 입력
      int a = sc.nextInt();
      int b = sc.nextInt();
      int c = sc.nextInt();
      G.get(a).add(new Edge(b, c)); // 정점 번호는 a, 이동 정점과 간선 길이는 b,c
    }
    T.solution(1); // 1번 정점에서 출발
    for (int i = 2; i <= n; i++) {
      if (D[i] != Integer.MAX_VALUE) System.out.println(i + " : " + D[i]);
      else System.out.println(i + " : impossible");
    }
  }

  public void solution(int v) {
    PriorityQueue<Edge> pQ = new PriorityQueue<>();
    pQ.offer(new Edge(v, 0));
    D[v] = 0; // 거리를 체크
    while (!pQ.isEmpty()) {
      Edge tmp = pQ.poll(); // 정점을 하나 꺼냄
      int now = tmp.vex; // 이동할 정점의 위치와
      int nowCost = tmp.cost; // 정점까지의 거리를 저장
      if (nowCost > D[now]) continue; // 기존에 조사한 뻗어나간 값보다 더 큰 값이 나온다면 굳이 검사할 필요가 없다
      for (Edge ob : G.get(now)) { // 현재 정점과 연결된 정점과 길이를 검사
        // D 저장된 정점까지의 거리와 현재 정점에서 조사한 다음 정점까지 이동 거리의 합을 비교하여 D에 저장된 값보다 더 작다면 바꿔줘야함
        if (D[ob.vex] > nowCost + ob.cost) {
          D[ob.vex] = nowCost + ob.cost; // 새로운 거리 값을 D에 저장해주고
          pQ.offer(new Edge(ob.vex, nowCost + ob.cost)); // 해당 정점을 저장한 큐에 구한 거리 값을 넣어준다
        }
      }
    }
  }
}
출처: https://nack1400.tistory.com/entry/AlgorithmJava-알고리즘-자바-다익스트라-Dijkstra-코딩테스트-Greedy-Algorithm-탐욕-알고리즘-Priority-Queue-우선순위-큐 [TIL로 프로 개발자 되기:티스토리]
```
