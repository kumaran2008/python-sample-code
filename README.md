/**
 * Dijkstra's Shortest Path Algorithm
 * -----------------------------------
 * Finds shortest distances from a source vertex to all other vertices
 * in a weighted graph with non-negative edge weights.
 *
 * Time Complexity : O((V + E) log V)   using a min-heap (priority_queue)
 * Space Complexity: O(V + E)
 *
 * Approach:
 *  1. Maintain a min-priority-queue of (distance, vertex) pairs.
 *  2. Greedily pop the vertex with the smallest tentative distance.
 *  3. Relax all its outgoing edges — if a shorter path is found,
 *     update the distance and push the new pair into the heap.
 *  4. A `visited` array prevents reprocessing a finalized vertex
 *     (handles stale/duplicate heap entries efficiently).
 */

#include <bits/stdc++.h>
using namespace std;

const long long INF = LLONG_MAX;

vector<long long> dijkstra(int n, int src, vector<vector<pair<int, int>>>& adj) {
    // adj[u] = list of {v, weight} representing edge u -> v with given weight
    vector<long long> dist(n, INF);
    vector<bool> visited(n, false);

    // min-heap of {distance, vertex}
    priority_queue<pair<long long, int>,
                    vector<pair<long long, int>>,
                    greater<>> pq;

    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (visited[u]) continue;  // skip stale entries
        visited[u] = true;

        for (auto& [v, w] : adj[u]) {
            if (!visited[v] && dist[u] != INF && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }

    return dist;
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, src;
    cout << "Enter number of vertices, edges, and source vertex: ";
    cin >> n >> m >> src;

    vector<vector<pair<int, int>>> adj(n);

    cout << "Enter each edge as: u v weight (0-indexed, directed)\n";
    for (int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        adj[u].push_back({v, w});
        // For an undirected graph, also add: adj[v].push_back({u, w});
    }

    vector<long long> dist = dijkstra(n, src, adj);

    cout << "\nShortest distances from vertex " << src << ":\n";
    for (int i = 0; i < n; i++) {
        cout << "Vertex " << i << ": ";
        if (dist[i] == INF) cout << "unreachable\n";
        else cout << dist[i] << "\n";
    }

    return 0;
}

/**
 * Sample Input:
 * 5 6 0
 * 0 1 4
 * 0 2 1
 * 2 1 2
 * 1 3 1
 * 2 3 5
 * 3 4 3
 *
 * Expected Output:
 * Vertex 0: 0
 * Vertex 1: 3
 * Vertex 2: 1
 * Vertex 3: 4
 * Vertex 4: 7
 */