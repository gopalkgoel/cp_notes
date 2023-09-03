## Range sum with sqrt(n) trick, 2023-08-11

Originally heard about this from Dev but then forgot about it before doing this LeetCode problem.


You're given an array of numbers `nums` and you'll make multiple queries `left,right` and you want the range sum from `nums[left]+...+nums[right]`. You also want to be able to update any value in nums with only $O(1)$ time used.


We're only interested in cost per range sum, precompute $o(N^2)$ should be allowed.

Naive solution is obviously $O(N)$ per query, but turns out you can make it $O(\sqrt{N})$. Idea is that you break `nums` into $N/k$ blocks of size $k$, and store block sum for each block. Update is obviously $O(1)$. Now for each query, you add up the block sums, which takes $O(N/k)$ time, and the extra stuff is $O(k)$ time. Taking $k=\sqrt{N}$ yields $O(\sqrt{N})$ time per range query. Pretty neat!

There is a better solution using segment trees, but idk for now. Here's my code for [LC307](https://leetcode.com/problems/range-sum-query-mutable/description/):

    class NumArray:

        def __init__(self, nums: List[int]):
            n = len(nums)
            k = int(n**0.5) + 1
            self.k = k
            self.nums = nums + [0]*(k*(k+1)-n) #always has length k*(k+1), so there are k+1 blocks
            self.blockSums = []

            for i in range(k+1):
                temp = 0
                for j in range(k):
                    temp += self.nums[k*i+j]
                self.blockSums.append(temp)

        def update(self, index: int, val: int) -> None:
            i = index//self.k
            self.blockSums[i] += (val-self.nums[index])
            self.nums[index] = val

        def sumRange(self, left: int, right: int) -> int:
            ans = 0
            k = self.k
            lk = left//k
            rk = right//k

            for i in range(lk,rk + 1): ans += self.blockSums[i]
            for j in range(lk*k,left): ans -= self.nums[j]
            for j in range(right+1,(rk+1)*k): ans -= self.nums[j]

            return ans
			
## Prefix Tree, 2023-08-11

Here's my implementation for [LC208](https://leetcode.com/problems/implement-trie-prefix-tree/).
I found [this](https://www.freecodecamp.org/news/how-to-validate-user-input-with-automated-trie-visualization/) explanation to be useful.

    class Trie:

        def __init__(self, inputChar=""):
            self.inputChar = inputChar
            self.end = False
            self.children = {}
            

        def insert(self, word: str) -> None:
            curr = self
            for c in word:
                if c in curr.children:
                    curr = curr.children[c]
                else:
                    curr.children[c] = Trie(c)
                    curr = curr.children[c]
            curr.end = True
            

        def search(self, word: str) -> bool:
            curr = self
            for c in word:
                if c not in curr.children: return False
                curr = curr.children[c]

            return curr.end
            

        def startsWith(self, prefix: str) -> bool:
            curr = self
            for c in prefix:
                if c not in curr.children: return False
                curr = curr.children[c]

            return True
        

## Computing min(A-B) in linear time if A and B are sorted -- two pointer, 2023-08-11

Maintain pointers `a,b` for `A,B` respectively, both initialized to `0`. At any point, we're only searching for $\displaystyle\min_{\substack{i\ge a \\ j\ge b}}|A[i]-B[j]|$.

If `A[a]==B[b]`, we're just done.

If `A[a]<B[b]`, then note that any solution $(i,j)$ with $i=a$ is strictly dominated by $(a,b)$, so we just compare `ans` against `B[b]-A[a]` and then move on with `a+=1`.

Similarly if `A[a]>B[b]`, compare `ans` against `A[a]-B[b]` and then move on with `b+=1`.

This solves it in $O(|A|+|B|)$ time or something.

## Dijkstra, 2023-08-13

Given a graph $G=(V,E)$ with non-negative edge weights and a source node $s$, the goal is to find $d(s,v)$ for all $v\in V$.

Maintain an array $d:V\to\mathbb{R}$ which holds distance estimates. Initialize `d[s]=0` and `d[x]=Infinity` for other `x`. Maintain a set `visited`.

	while visited:
		v = element of V\visited with min distance estimate
		add v to visited
		for u adj to v:
			alt = w(u,v) + d[v]
			if(alt < d[u]):
				d[u] = alt

Actually implement the finding `v` step with a min-heap. Here's some code, where the vertex set is `V=[0,1,...,n-1]`.

		Q = [(0,s)]
        d = [float("infinity") for _ in range(n)]
        d[s]=0

        visited = [False]*n

        while(bool(Q)):
            dd,u = heapq.heappop(Q)
            if(visited[u]): continue
            visited[u]=True
            for w,v in adj[u]:
                alt = d[u] + w
                if(alt<d[v]):
                    d[v]=alt
                    heapq.heappush(Q,(alt,v))

[This](https://web.stanford.edu/class/archive/cs/cs161/cs161.1176/Lectures/CS161Lecture11.pdf) is a good link for a proof of Dijkstra. 




















