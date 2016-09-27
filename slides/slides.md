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
## Visualisations of sorting

>>>
TODO

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-URmkfvtK3Qw-freeway.jpg" class="empty" -->

