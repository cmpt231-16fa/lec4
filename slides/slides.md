<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" -->
# CMPT231
## Lecture 4: ch8, 11
### Linear-time Sort and Hash Tables

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" -->
## Devotional

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" -->
## Outline for today
+ Proving all **comparison** sorts are *&Omega;(n lg n)*
+ **Linear-time** non-comparison sorts:
  + **Counting** sort
  + **Radix** sort and analysis
  + **Bucket** sort and probabilistic proof
+ **Hash tables**:
  + Collision handling by **chaining**
  + Hash **functions** and **universal** hashing
  + Collision handling by **open addressing**

---
## Summary of sorting algorithms
+ **Comparison** sorts *(ch2, 6, 7)*:
  + **Insertion** sort:  *&Theta;(n^2)*, easy to program, slow
  + **Merge** sort: *&Theta;(n lg n)*, out-of-place copy (slow)
  + **Heap** sort: *&Theta;(n lg n)*, in-place, max-heap
  + **Quicksort**: *&Theta;(n^2)* worst-case, *&Theta;(n lg n)* average
    + and small (fast) constant factors
+ **Linear**-time non-comparison sorts *(ch8)*:
  + **Counting** sort: *k* distinct values: *&Theta;(k)*
  + **Radix** sort: *d* digits, *k* values: *&Theta;(d(n+k))*
  + **Bucket** sort: uniform distribution: *&Theta;(n)*

---
## Comparison sorts are &Omega;(n lg n)
+ **Decision tree** model of computation:
  + **Leaves** are possible *outputs*
    + i.e., **permutations** of the input
  + **Nodes** are *decision* points
    + i.e., when **comparisons** are made
  + A **path** through the tree is one *run* of algorithm
+ Num **leaves** = num **permutations** = *n!*
+ Num **comparisons** = num **nodes** along path
+ So **worst-case** complexity = **depth** of tree:
  + = &Omega;( *lg(num leaves)* ) = &Omega;( *lg(n!)* )
  + = &Omega;( *n lg n* ) (by **Stirling**)

---
## Outline

---
## Linear-time sorts
+ Use **assumptions** on input: e.g., known *range*
  + or known *distribution* of values
+ For real-world arrays, *&Theta;(n)* and *&Theta;(n lg n)* are very similar
  + Up to *n* = \`10^6\`, *lg n &lt; 21*, a smallish factor
  + A fast *n lg n* sort like **Quicksort** may have <br/>
    smaller *constants* than a linear-time sort
+ **Hybrid** algorithms (e.g., *#7.4-5*):
  + One pass with **Quicksort**, stop when length &lt; *c*
  + Second pass with **insertion** sort
    + Few shuffles when input is "nearly sorted"
+ **Recursion** is expensive (function calls)

---
## Counting sort
+ **Assume**: values are **integers** in *{0, ..., k}*
+ **Out-of-place** sort:
  + **Census** array (size *k*) tallies a *histogram*
  + Items *copied* into **output** array
+ **Stable**: preserves order of duplicates
+ **Complexity**: *&Theta;(n+k)* (watch out if *k* gets too big!)

```
def counting_sort(A, n, k):
  out[ 1 .. n ]                 # new output array
  census[ 0 .. k ]              # new array for census
  for j in 1 .. n:              # take census
    census[ A[ j ] ]++
  for i in 1 .. k:              # cumulative sums
    census[ i ] += census[ i-1 ]
  for j in n .. 1:              # copy (in reverse)
    out[ census[ A[ j ] ] ] = A[ j ]
    census[ A[ j ] ]--
```

---
## Radix sort
+ (How **IBM** made its fortune! Punch cards, ca 1900)
+ **Assume**: values have at most *d* digits
+ Sort one **digit** at a time, **least**-significant first (**why**?)
+ Use a **stable** sort, e.g., **counting** sort (**why**?)

<div class="imgbox"><div data-markdown>

```
def radix_sort( A, n, d ):
  for i in 1 .. d:
    stable_sort( A on digit i )
```

</div><div data-markdown>

| 3 | 7 | 4 | 5 |
|---|---|---|---|
| 2 | 9 | 1 | 3 |
| 1 | 0 | 1 | 6 |
| 2 | 0 | 1 | 6 |
| - | 9 | 1 | 3 |

</div></div>

---
## Radix sort: complexity
+ **Input**: *n* items of *d* digits, each with *k* values (e.g., k=*10*)
+ e.g., using **counting** sort as the stable sort:
  + *d* iterations, each *&Theta;(n+k)*
  + So total complexity is *&Theta;(d(n+k))*
+ **Digits** need not be base *k=10*!

---
## Radix: choosing digit size
+ *b*-bit items can be **split** into *r*-bit digits:
  + Then \` d = b/r \` and \` k = 2^r-1 \`
  + e.g., *b*=32-bit items in *r*=8-bit digits &rArr; *d*=4, *k*=255
+ **Choose** r = *lg n*:
  \` Theta((b/r)(n+2^r)) \`
  \` = Theta((b/(text(lg)n))(2n)) \`
  \` = Theta((bn)/(text(lg)n)) \`
+ e.g., to sort *n*=\`2^16\` integers of *b*=64-bits, use *r*=16-bit digits

---
## Outline

---
## Bucket sort
+ **Assume**: values **uniformly** distributed over *[0,1)*
+ Divide range *[0,1)* into *n* equal-size **buckets**
  + Each bucket could be **array** or **linked list**
+ **Distribute** input into buckets *(&Theta;(n))*
+ **Sort** each (small) bucket (e.g., with **insertion** sort)
+ **Pull** from each bucket in order *(&Theta;(n))*

>>>
TODO: viz of buckets?

---
## Bucket sort: complexity
+ Let \`n\_i\` be number of **items** in the *i*-th bucket
  + Sorting a bucket with **insertion** sort takes \`O(n\_i^2)\`
+ **Intuition**: uniform distribution &rArr; \`n\_i ~~ 1\`
+ **Expected** time of bucket sort:
  \` E[T(n)] = E[Theta(n) + sum O(n\_i^2)] \`
  \` = Theta(n) + O(sum E[n\_i^2]) \`
  \` = Theta(n) + O(sum (2 - 1/n)) \` *(lemma)*
  \` = Theta(n) + O(2n-1)\` = *O(n)*

---
## Lemma: \`E[n\_i^2]\` = 2 - 1/n
+ Use an **indicator var** \`X\_(ij)\` = 1 iff *j*-th **item**
  falls in *i*-th **bucket**
  + Hence the *i*-th bucket has \`n\_i=sum\_(j=0)^(n-1) X\_(ij)\` items
+ So \` E[n\_i^2] = E[ (sum\_(j=0)^(n-1) X\_(ij))^2 ] \`
  \` = sum\_(j=0)^(n-1) E[ X\_(ij)^2 ] + 2sum\_(j=0)^(n-1) sum\_(k=0)^(j-1) E[ X\_(ij)X\_(ik) ] \`

---
## Lemma, continued
+ \` E[n\_i^2] = sum\_(j=0)^(n-1) E[ X\_(ij)^2 ] + 2sum\_(j=0)^(n-1) sum\_(k=0)^(j-1) E[ X\_(ij)X\_(ik) ] \`
+ For **first** term: \`E[X\_(ij)^2] = 0^2 P(X\_(ij)=0) + 1^2 P(X\_(ij)=1) \`
  \` = 1^2 (1/n) = 1/n \`
+ For **second** term, note items *j* &ne; *k* are **independent**:
  \` E[ X\_(ij)X\_(ik) ] = E[X\_(ij)]E[X\_(ik)] \`
  \` = (1/n)(1/n) = 1/n^2 \`

---
## Lemma, continued
+ So \`E[n\_i^2] \`
  \` = sum\_(j=0)^(n-1) E[ X\_(ij)^2 ] + 2sum\_(j=0)^(n-1) sum\_(k=0)^(j-1) E[ X\_(ij)X\_(ik) ] \`
  \` = sum\_(j=0)^(n-1) (1/n) + 2sum\_(j=0)^(n-1) sum\_(k=0)^(j-1) (1/n^2) \`
  \` = (n)(1/n) + 2((n(n-1))/2)(1/n^2) \`
  \` = 2 - 1/n \`
+ This proves the **lemma**, proving **bucket sort** is *&Theta;(n)*
+ **Assumptions**: input values **uniformly** distributed

---
## Outline

---
## Hash tables
+ **Dictionary** of *key*-*value* pairs, with this **interface**:
  + `insert(T, k, x)`: **add** item *x* with key *k*
  + `search(T, k)`: **find** an item with key *k*
  + `delete(T, x)`: **remove** specific item *x*
+ Better than regular array (**direct addressing**) when:
  + **Range** of possible keys too huge to allocate
  + Actual keys are only **sparse** subset of possible keys
+ e.g., only have items at keys *0*, *2*, *40201300*:
  + Direct addressing would allocate 40,201,300 entries!

---
## Hashing
+ Hash **function** \`h(k): U -> bbb Z\_m\` (i.e., *{0, ..., m-1}*)
  maps from **universe** *U* of possible keys
  to a set of *m* **buckets**
  + Use *h(k)* as **key** instead of *k*
+ Hash **collision**: two keys hash to **same** bucket:
  \`h(k\_i)=h(k\_j)\`
  + Want hash function to **minimise** collisions
  + Collision **handling**: e.g., chain via **linked list**
+ Similar to **bucket sort**, but
  + Hash function maps **distribution** of keys in *U*
    to **uniform** distrib on buckets

---
## Hash table operations
+ Assuming collision handling via **linked lists**:
+ `insert(T, k, x)`: insert *x* at **head** of list at bucket *h(k)*
  + *O(1)* complexity; assumes *x* **not already** in list
+ `search(T, k)`: **linear search** through bucket *h(k)*
  + \`O(n\_(h(k)))\`, where \`n\_(h(k))\` = **num items** in bucket *h(k)*
+ `delete(T, x)`: if arg is a **pointer** directly to item, then *O(1)*
  + if arg is a **key**, then need to **search** for it first:
  \`O(n\_(h(k)))\`

---
## Load factor
+ **Efficiency** of `search()` depends on how **full** buckets are: \`n\_(h(k))\`
+ **Load factor** *&alpha;* = *n*/*m*:
  + *n* = total number of **items** stored in hash table
  + *m* = number of **buckets**
  + *&alpha;* is **average** num items per bucket: \`E[n\_(h(k))]\`
+ **Unsuccessful** search takes average *&Theta;(1+&alpha;)*
  + Computing the **hash function** takes *&Theta(1)*
  + **Linear search** goes through entire bucket
  + Expected **length** of bucket's linked list is *&alpha;*
+ **Successful** search is also *&Theta;(1+&alpha;)*:

---
## Hash table search: &Theta;(1+&alpha;)
+ Num **items** searched = **position** of *x* in linked list at *h(k)*
  + = Number of **collisions** after *x* was inserted
+ Use an **indicator**: \`X\_(ij)\` = 1 iff \`h(k\_i)=h(k\_j)\`
  + P(*i* and *j* collide) = \`E[X\_(ij)] = 1/m\`
+ Expected num **items** searched:
  \` E[ (1/n) sum\_i (text(num items)) ] \`
  \` = E[ (1/n) sum\_i (1 + sum\_j X\_(ij)) ] \`
  \` = (1/n) sum\_i (1 + sum\_j E[X\_(ij)]) \`
  \` = (1/n) sum\_i (1 + sum\_j (1/m)) \`
  \` = 1 + (1/n)sum\_i sum\_j (1/m) \`
  \` = 1 + (1/(nm))((n(n-1))/2) \`
  \` = 1 + alpha/2 - alpha/(2n) \`

---
## Outline

---
## Hash functions
+ Assume \`U=bbb N\` (i.e., convert keys to **natural numbers**)
+ Want *h(k)* **uniformly** distributed on \`bbb Z\_m\`
  + But distribution of keys *k* is **unknown**
  + Also, keys \`k\_i\` and \`k\_j\` might not be **independent**
+ **Division** hash: h(k) = *k mod m*
  + **Fast**, but if \`m=2^p\` (i.e., a power of 2), <br/>
    this is just selecting the *p* **least-significant** bits
  + If *k* is a **string** using a radix-\`2^p\` representation,
    then **permuting** the string gives **same** hash *(#11.3-3)*
  + So need to choose *m* **prime** and not too close to a power of 2

---
## Multiplication hash
+ h(k) = \`|\_ m(kA mod 1) \_|\`, where 0 &lt; *A* &lt; 1 is a chosen **constant**
+ Fast **implementation** using \`m=2^p\`:
  + Let *w* be the native machine **word size** (num bits)
  + **Choose** a *w*-bit integer *s*, 0 &lt; s &lt; \`2^w\`
    + Let *A* = \`s/2^w\`
  + **Multiply** *s*&lowast;*k*: product has *2w* bits in two *words*
    \`r\_0, r\_1\`
  + **Select** the *p* most-significant bits of lower word \`r\_0\`

>>>
TODO: figure

---
## Universal hashing
+ For any **fixed** choice of hash function,
  can always find **bad input** resulting in lots of hash **collisions**
+ Why not **randomly** select from a **pool** *H* of hash functions?
+ Want pool to have **universal hash** property:
  + For any two keys *j* &ne; *k*, the **number** of hash functions
    in *H* that cause a **collision** *h(j)* = *h(k)* is &le; *|H|/m*
  + i.e., P( *h(j)* = *h(k)* ) &le; *1/m*
+ Then expected **bucket size** is still *O(1+&alpha;)*
  + So average complexity of **search** is still *O(1)*

---
## Outline

---
## Open addressing
+ Another method of **collision** handling:
  + Store keys **directly** in table, no linked lists
+ Hash **function** \`h: U xx bbb Z\_m -> bbb Z\_m\`
  + A **probe sequence** is *h(k,0)*, *h(k,1)*, *h(k,2)*, ...
+ To **insert** an item in table, first try *h(k,0)*
  + If already **occupied**, try next in sequence: *h(k,1)*
  + Will eventually try **all** slots (full **coverage**)
    if probe sequence is a **permutation** of \`bbb Z\_m\`
+ **Search** is similar: check if found desired **key**
+ Hash table will still **overflow** if *n* &gt; *m*

---
## Probe sequencing
+ Choose a hash function that gives us **uniform hashing**:
  + Each **permutation** of \`bbb Z\_m\` equally likely to be
    the probe sequence for a given key
+ **Linear** probing: h(k,i) = *h(k) + i*
  + Try *h(k)*, then *h(k)+1*, etc. (modulo *m*)
  + But: long **runs** get **longer** (more likely to hit)
+ **Quadratic** probing: h(k,i) = \`h(k) + c\_1 i + c\_2 i^2\`
  + Must choose \`c\_1, c\_2\` to get full **coverage**
  + But: a **collision** on initial *h(k)* means a full **sequence collision**

---
## Double hashing
+ Use **two** hash functions: h(k, i) = \`h\_1(k) + i h\_2(k)\`
  + Try \`h\_1(k)\` **first**, then use \`h\_2\` to **jump** around
+ To get full **coverage**, choose \`h\_2(k)\` and *m* **relatively prime**
  + i.e., no common **factors** other than 1
  + e.g., let m=\`2^p\` and ensure \`h\_2(k)\` always **odd**
  + e.g., let m be **prime**, and ensure 1 &lt; \`h\_2(k)\` &lt; *m*
+ Each combination of \`h\_1(k)\` and \`h\_2(k)\` yields
  a **different** probe sequence:
  + Total **number** of sequences is \`Theta(n^2)\`

---
## Outline

---
## Visualisations of sorting

>>>
TODO

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" class="empty" -->

