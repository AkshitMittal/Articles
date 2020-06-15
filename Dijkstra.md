---


---

<h2 id="dijkstra-algorithm">Dijkstra algorithm</h2>
<p><strong>Problem</strong></p>
<p>Given a graph and a source vertex in the graph, find the shortest paths from source to all vertices in the given graph, producing a shortest-path tree. Commonly known as single-source shortest path problem.</p>
<p>Dijkstra’s algorithm solves the single-source shortest path problem with non-negative edge weight.</p>
<p>Formally, for a given weighted graph G=(V, E) having V vertices and E edges having non-negative weights with source vertex s ∈ V Dijkstra’s algorithm finds the shortest path from s to all vertices v ∈ V.</p>
<p>Got stuck! No worries let’s make it more clear. Imagine, you have to travel to N cities and you wish to save some time and fuel? Then simply apply Dijkstra’s algorithm to get the best path.</p>
<p>Dijkstra’s algorithm can be applied to both directed and undirected graphs.<br>
Graph G must have non-negative weights.</p>
<p><strong>Optimal substructure of the shortest path</strong></p>
<p>Shortest-paths algorithms typically rely on the property that a shortest path between two vertices contains other shortest paths within it. The optimal substructure is one of the key indicators that dynamic programming and the greedy method might apply. Dijkstra’s algorithm is a greedy algorithm, and the Floyd- Warshall algorithm, which finds the shortest paths between all pairs of vertices is a dynamic-programming algorithm.</p>
<p><strong>Algorithm</strong>:<br>
For each vertex v ∈ G.V, we maintain an attribute v.d, which is an upper bound on the weight of a shortest path from source s to v. We call v.d a shortest-path estimate. We initialize the shortest-path estimates and predecessors(parent) by the following θ(V) time function :</p>
<p><strong>INITIALIZE-SINGLE-SOURCE(G, s)</strong></p>
<ol>
<li>
<p>for each vertex v ∈ G.V</p>
</li>
<li>
<p>v.d = ∞ // Initialize every vertex as infinite distance reachable.</p>
</li>
<li>
<p>v.parent = NIL</p>
</li>
<li>
<p>s.d=0</p>
</li>
</ol>
<p>After initialization, we have v.parent = NIL for all v ∈ G.V , s.d=0 , and v.d = ∞ for v ∈ G.V - {s}</p>
<p>The process of relaxing an edge (u,v) consists of testing whether we can improve the shortest path to v found so far by going through u and, if so, updating v.d and v.parent. A relaxation step may decrease the value of the shortest-path estimate v.d and update v’ s predecessor(parent) attribute. The following code performs a relaxation step on edge (u,v) in O(1) time :</p>
<p><strong>RELAX(u,v,w)</strong></p>
<ol>
<li>
<p>If v.d &gt; u.d + w(u,v)</p>
</li>
<li>
<p>v.d &gt; u.d + w(u,v)</p>
</li>
<li>
<p>v.parent = u</p>
</li>
</ol>
<p>Dijkstra’s algorithm calls INITIALIZE-SINGLE-SOURCE and then repeatedly relaxes edges using RELAX(u,v,w). Moreover, relaxation is the only means by which shortest-path estimates and predecessors change. Dijkstra’s algorithm relax each edge exactly once.</p>
<p><strong>DIJKSTRA(G,w, s)</strong></p>
<ol>
<li>
<p>INITIALIZE-SINGLE-SOURCE (G, s)</p>
</li>
<li>
<p>S = Φ ;</p>
</li>
<li>
<p>Q = G.V</p>
</li>
<li>
<p>while Q != Φ</p>
</li>
<li>
<p>u = EXTRACT-MIN ( Q )</p>
</li>
<li>
<p>S = S ∪ {u}</p>
</li>
<li>
<p>for each vertex v ∈ G.Adj[u]</p>
</li>
<li>
<p>RELAX (u,v,w)</p>
</li>
</ol>
<p>Dijkstra’s algorithm maintains a set S of vertices whose final shortest-path weights from the source s have already been determined. The algorithm repeatedly selects the vertex u ∈  V - S with the minimum shortest-path estimate, adds u to S, and relaxes all edges leaving u. In the following implementation, we use a min-priority queue Q of vertices, keyed by their d values.</p>
<p>Line 1 initializes the d and parent values in the usual way using INITIALIZE-SINGLE-SOURCE(G, s) function, and line 2 initializes the set S to the empty set. The algorithm maintains the invariant that Q = V - S at the start of each iteration of the while loop of lines 4–8. Line 3 initializes the min-priority queue Q to contain all the vertices in V; since S = Φ; at that time, the invariant is true after line 3. Each time through the while loop of lines 4–8, line 5 extracts a vertex u from Q = V - S and line 6 adds it to set S, thereby maintaining the invariant. (The first time through this loop, u = s.) Vertex u, therefore, has the smallest shortest-path estimate of any vertex in V - S. Then, lines 7–8 relax each edge (u,v) leaving u, thus updating the estimate v.d and the predecessor v.parent if we can improve the shortest path to v found so far by going through u. Observe that the algorithm never inserts vertices into Q after line 3 and that each vertex is extracted from Q and added to S exactly once so that the while loop of lines 4–8 iterates exactly | V | times.</p>
<p>Below is a C++ implementation.</p>
<pre><code>#define pi pair&lt;int,int&gt; 
const  int  N  =100005;  
vector&lt;pi&gt;  g[N];  
int  vis[N];  
const  int  inf=2000000000;

void make_graph(vector&lt;vector&lt;int&gt; &gt; &amp;edges)
{
   for(auto &amp;it:edges)
   {
	        int x=it[0];
	        int y=it[1];
	        int w=it[2];
	        g[x].push_back(make_pair(w,y));
	        g[y].push_back(make_pair(w,x));
  }
}    

vector&lt;int&gt; dijsktra(int n,vector&lt;vector&lt;int&gt; &gt; &amp;edges,int source)
{
    make_graph(edges);
    
    // Below 2 lines represent our INITIALIZE-SINGLE-SOURCE(G, s). Here, parent attribute can also be added.
    vector&lt;int&gt; distance(n,inf);
    distance[source]=0;
    
    priority_queue&lt;pi,vector&lt;pi&gt;, greater&lt;pi&gt; &gt; q;   // min-heap
    q.push(make_pair(0,source));
    
    while(!q.empty())
    {
	        pi p=q.top();
	        int x=p.second;
	        q.pop();
	        
         // Don't revisit any node.
	        if(vis[x])
	            continue;
	        vis[x]=1;
	        for(int i=0; i&lt;g[x].size(); ++i)
	         {
		            int y=g[x][i].second;
		            int w=g[x][i].first;
		            
		     // Below block reperesents our RELAX(u,v,w) method
		      
		            if(distance[x]+w&lt;distance[y])
		            {
		                distance[y]=distance[x]+w;
		                q.push(make_pair(distance[y],y));
                    }
            }
   }
   
    for(int i=0; i&lt;n; ++i)
    {
        if(distance[i]==inf)
            distance[i]=-1;
    }
    return distance;
}
</code></pre>
<p>Because Dijkstra’s algorithm always chooses the closest vertex in V - S to add to set S, we say that it uses a greedy strategy.</p>
<p><strong>Time Complexity analysis</strong><br>
Dijkstra’s algorithm maintains the min-priority queue Q by calling three priority-queue operations: INSERT (implicit in line 3), EXTRACT-MIN (line 5), and DECREASE-KEY (implicit in RELAX, which is called in line 8). The algorithm calls both INSERT and EXTRACT-MIN once per vertex. Because each vertex u ∈ V is added to set S exactly once, each edge in the adjacency list Adj[u] is examined in the for loop of lines 7–8 exactly once during the course of the algorithm. Since the total number of edges in all the adjacency lists is |E|, this for loop iterates a total of |E| times, and thus the algorithm calls DECREASE-KEY at most |E| times overall. The running time of Dijkstra’s algorithm depends on how we implement the min-priority queue.</p>
<p>Here, we implement the min-priority queue using a binary heap. Each EXTRACT-MIN operation then takes time O(log V). As before, there are |V| such operations. The time to build the binary min-heap is O|V|. Each DECREASE-KEY operation takes time O( log V ), and there are still at most |E| such operations. The total running time is, therefore, O ( V log V + E log V ).</p>
<p>Let’s dry run on a graph.<br>
<img src="https://miro.medium.com/max/832/1*-GahWsc2cRBvuaY1FJ2GTg.png" alt=""></p>
<p>1<sup>st</sup> iteration :<br>
Relaxing the smallest…A</p>

<table>
<thead>
<tr>
<th>Vertex</th>
<th>Shortest distance from A</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>4</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>INF</td>
</tr>
<tr>
<td>E</td>
<td>INF</td>
</tr>
<tr>
<td>F</td>
<td>INF</td>
</tr>
</tbody>
</table><pre><code>      A-&gt;C : 2
</code></pre>
<p>2<sup>nd</sup> iteration :<br>
Relaxing the smallest…C</p>

<table>
<thead>
<tr>
<th>Vertex</th>
<th>Shortest distance from A</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>4</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>4</td>
</tr>
<tr>
<td>E</td>
<td>INF</td>
</tr>
<tr>
<td>F</td>
<td>6</td>
</tr>
</tbody>
</table><pre><code>     A-&gt;D : 4      
</code></pre>
<p>Notice that B cannot be picked here, because we have edge weights in our queue. Here, we have minimum weight edge present in our queue CD with weight of 2. Don’t get confused if you are following from the start.</p>
<p>3<sup>rd</sup> iteration :<br>
Relaxing the smallest…D</p>

<table>
<thead>
<tr>
<th>Vertex</th>
<th>Shortest distance from A</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>4</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>4</td>
</tr>
<tr>
<td>E</td>
<td>7</td>
</tr>
<tr>
<td>F</td>
<td>5</td>
</tr>
</tbody>
</table><pre><code>   A-&gt;F : 5
</code></pre>
<p>4<sup>th</sup> iteration :<br>
Picking the smallest…F</p>

<table>
<thead>
<tr>
<th>Vertex</th>
<th>Shortest distance from A</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>4</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>4</td>
</tr>
<tr>
<td>E</td>
<td>6</td>
</tr>
<tr>
<td>F</td>
<td>5</td>
</tr>
</tbody>
</table><pre><code>    A-&gt;E : 5 
</code></pre>
<p>5<sup>th</sup> iteration :<br>
Picking the smallest…E</p>

<table>
<thead>
<tr>
<th>Vertex</th>
<th>Shortest distance from A</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>4</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>4</td>
</tr>
<tr>
<td>E</td>
<td>6</td>
</tr>
<tr>
<td>F</td>
<td>5</td>
</tr>
</tbody>
</table><pre><code>       A-&gt;B : 4 
</code></pre>
<p>Dijkstra’s algorithm finalizes the shortest path from source to any vertex in every iteration.</p>
<p><strong>Closing Remarks</strong></p>
<p>Just for your knowledge, we can, in fact, achieve a running time of O(V log V + E) by implementing the min-priority queue with a Fibonacci heap.<br>
Bellman Ford works for negative weight edges also but with more time.</p>

