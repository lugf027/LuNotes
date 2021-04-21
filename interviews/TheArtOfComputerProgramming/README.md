# The Art of Computer Programming



## Chapter 1 Basic Concepts



## 1.1 Algorithms 

##### 1.1.E Eclid's algorithm

Def: Given two positive integers *m* and *n*, find their greater common divisor, i.e., the largest positive integer which evenly divides both m and n. 

```
E_0 [Ensure m > n.] If m < n, exchange m <-> n.
E_1 [Find remainder.] Divide m by n and let r be the remainder.
E_2 [Is it zero?] If r==0, the algorithm terminates; n be the answer.
E_3 [Interchange.] Set m <- n, n<- r, and go back to step E_1. 
```



##### Five Features

1) Finiteness. 

2) Definiteness.

3) Input.

4) Output.

5) Effectiveness.



## 1.2 Mathematical Preliminaries

 ### 1.2.1 Mathematical Induction



### 1.2.2 Numbers, Powers, and Logarithms

### 1.2.3 Sums and Products