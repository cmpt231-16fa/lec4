<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" -->
# CMPT231
## Lecture 4: ch8, 11
### Linear-time Sort and Hash Tables

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" -->
##

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
+ With **counting** sort, we have *d* iterations, each *&Theta;(n+k)*
  + So total complexity is *&Theta;(d(n+k))*
+ If items are *b*-bits long, we can encode them using *r*-bit digits
  + Choose *r* to optimise run time: **balance** *d* vs *n+k*
  + \` d = b/r \` and \` k = 2^r-1 \`
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
+ **Intuition**: uniform distribution &rArr; \`n\_i ~~ 1\`
  + So each **intra-bucket** sort should be roughly *O(1)*
+ To be precise, each intra-bucket sort takes \`O(n\_i^2)\`
+ **Expected** time of bucket sort:
  \` E(T(n)) = E(Theta(n) + sum\_(i=0)^(n-1) O(n\_i^2)) \`
  \` = Theta(n) = O(sum\_(i=0)^(n-1) E(n\_i^2)) \`


---
## Visualisations of sorting

>>>
TODO

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" class="empty" -->

