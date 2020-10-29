# Search



**Contents:**

* [Linear Search](#Linear Search)

* [Binary Search](#Binary Search)

* [Interpolation Search](#Interpolation Search)
* [Exponential Search](#Exponential Search)
* [Fibonacci Search](#Fibonacci Search)

------



### Linear Search

**Problem:**  Given an array arr[] of n elements, write a function to search a given element x in arr[].

**Method: ** A simple approach is to do a **linear search**,  

1. Start from the leftmost element of arr[] and one by one compare x with each element of arr[]
2. If x matches with an element, return the index.
3. If x doesn’t match with any of elements, return -1.

**Implementation:**

```C++
// C++ code to linearly search x in arr[].
// If x is present then return its location, 
// otherwise return -1.
int search(int arr[], int n, int x){
    for (int i = 0; i < n; ++i)
        if (arr[i] == x)
            return i;
    return -1;
}
```

**Improve:** Improve Linear Search Worst-Case Complexity.

```C++
// C++ code to improved-linearly search x in arr[].
// If x is present then return its location, 
// otherwise return -1.
int search(int arr[], int n, int x){
    int left = 0;
    int right = n - 1;
    while (left <= right){
        if (arr[left] == x)
            return left;
        if (arr[right] == x)
            return right;
        ++left;
        --right;
    }
    return -1;
}
```

------



### Binary Search

**Problem:**  Given a sorted array arr[] of n elements, write a function to search a given element x in arr[].

**Binary Search:**  

Search a sorted array by repeatedly dividing the search interval in half. Begin with an interval covering the whole array. If the value of the search key is less than the item in the middle of the interval, narrow the interval to the lower half. Otherwise narrow it to the upper half. Repeatedly check until the value is found or the interval is empty.

The idea of binary search is to use the information that the array is sorted and reduce the time complexity to O(Log n).

**Method:** 

We basically ignore half of the element just after one comparison:

1. Compare x with the middle element.
2. If x matches with middle element, we return the mid index.
3. Else If x is greater than the mid element, then x can only lie in right half sub-array after the mid element. So we recur for right half.
4. Else (x is smaller) recur for the left half.

**Recursive implementation:**

- Time Complexity: o(Log n)
- Auxiliary Space: o(Log n) recursion call stack space

```C++
// C++ program to implement recursive Binary Search 
int binarySearch(int arr[], int left, int right, int x){
    if(right >= 1eft){
        int mid = left + (right - left) / 2;
        
        if(arr[mid] == x)
            return mid;
        
        if(arr[mid] > x)
            return binarySearch(arr, left, mid-1, x);
        
        return binarySearch(arr, mid+1, right, x);
    }
    return -1;
}
```

**Iterative Implementation**

- Time Complexity: o(Log n)
- Auxiliary Space: o(1)

```c++
// C++ program to implement recursive Binary Search 
int binarySearch(int arr[], int left, int right, int x){
    while(right >= 1eft){
        int mid = left + (right - left) / 2;
        
        if(arr[mid] == x)
            return mid;
        
        if(arr[mid] > x)
            left = mid + 1;
        
        right = mid - 1;
    }
    return -1;
}
```

**Video:**

<iframe width="580" height="374" src="https://www.youtube.com/embed/T2sFYY-fT5o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

------



### Jump Search

**Problem:** Given a sorted array arr[] of n elements, write a function to search a given element x in arr[].

**Introduction:** Like [Binary Search](#binary search), Jump Search is a searching algorithm for sorted arrays. The basic idea is to check fewer elements (than [linear search](#Linear Search)) by jumping ahead by fixed steps or skipping some elements in place of searching all elements.

**What is the optimal block size to be skipped?**
In the worst case, we have to do n/m jumps and if the last checked value is greater than the element to be searched for, we perform m-1 comparisons more for linear search. Therefore the total number of comparisons in the worst case will be ((n/m) + m-1). The value of the function ((n/m) + m-1) will be minimum when m = √n. Therefore, the best step size is m = **√n.**

**Implementation:**

- Time Complexity : O(√n)
- Auxiliary Space : O(1)

```C++
// C++ program to implement Jump Search 
  
#include <bits/stdc++.h> 
using namespace std; 

int jumpSearch(int arr[], int x, int n) 
{ 
    // Finding block size to be jumped 
    int step = sqrt(n); 
  
    // Finding the block where element is 
    // present (if it is present) 
    int prev = 0; 
    while (arr[min(step, n)-1] < x) 
    { 
        prev = step; 
        step += sqrt(n); 
        if (prev >= n) 
            return -1; 
    } 
  
    // Doing a linear search for x in block 
    // beginning with prev. 
    while (arr[prev] < x) 
    { 
        prev++; 
  
        // If we reached next block or end of 
        // array, element is not present. 
        if (prev == min(step, n)) 
            return -1; 
    } 
    // If element is found 
    if (arr[prev] == x) 
        return prev; 
  
    return -1; 
} 
```

**Important points:**

- Works only sorted arrays.
- The optimal size of a block to be jumped is (√ n). This makes the time complexity of Jump Search O(√ n).
- The time complexity of Jump Search is between Linear Search ( ( O(n) ) and Binary Search ( O (Log n) ).
- Binary Search is better than Jump Search, but Jump search has an advantage that we traverse back only once (Binary Search may require up to O(Log n) jumps, consider a situation where the element to be searched is the smallest element or smaller than the smallest). So in a system where binary search is costly, we use Jump Search.

**Video:**

<iframe width="580" height="374" src="https://www.youtube.com/embed/63kS6ZkMpkA" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

------



### Interpolation Search

**Problem:** Given a sorted array of n uniformly distributed values arr[], write a function to search for a particular element x in the array.

uniformly: in a way that is the same in all cases and at all times; with equal space between each or in equal amounts; evenly.

**Interpolation Search:**

The Interpolation Search is an improvement over Binary Search for instances, where the values in a sorted array are uniformly distributed. Binary Search always goes to the middle element to check. On the other hand, interpolation search may go to different locations according to the value of the key being searched. For example, if the value of the key is closer to the last element, interpolation search is likely to start search toward the end side. 

To find the position to be searched, it uses following formula.

```markdown
// The idea of formula is to return higher value of pos
// when element to be searched is closer to arr[hi]. And
// smaller value when closer to arr[lo]
pos = lo + [ (x-arr[lo]) * (hi-lo) / (arr[hi]-arr[Lo]) ]

* arr[] ==> Array where elements need to be searched
* x     ==> Element to be searched
* lo    ==> Starting index in arr[]
* hi    ==> Ending index in arr[]
```

**Method:** 

1. In a loop, calculate the value of “pos” using the probe position formula.
2. If it is a match, return the index of the item, and exit.
3. If the item is less than arr[pos], calculate the probe position of the left sub-array. Otherwise calculate the same in the right sub-array.
4. Repeat until a match is found or the sub-array reduces to zero.

**Implementation: **

* Time Complexity:  If elements are uniformly distributed, then O (log log n)). In worst case it can take up to O(n).

* Auxiliary Space: O(1)

```C++
// C++ program to implement interpolation search 
#include<bits/stdc++.h> 
using namespace std; 
  
// If x is present in arr[0..n-1], then returns 
// index of it, else returns -1. 
int interpolationSearch(int arr[], int n, int x) 
{ 
    // Find indexes of two corners 
    int lo = 0, hi = (n - 1); 
  
    // Since array is sorted, an element present 
    // in array must be in range defined by corner 
    while (lo <= hi && x >= arr[lo] && x <= arr[hi]){ 
        if (lo == hi){ 
            if (arr[lo] == x) return lo; 
            return -1; 
        } 
        // Probing the position with keeping 
        // uniform distribution in mind. 
        int pos = lo + (((double)(hi - lo) /  (arr[hi] - arr[lo])) 
                        * (x - arr[lo])); 
  
        // Condition of target found 
        if (arr[pos] == x) 
            return pos; 
        // If x is larger, x is in upper part 
        if (arr[pos] < x) 
            lo = pos + 1; 
        // If x is smaller, x is in the lower part 
        else
            hi = pos - 1; 
    } 
    return -1; 
} 
```

**Recursive implementation:**

```C++
// C++ program to implement interpolation  
// search with recursion 
#include <bits/stdc++.h> 
using namespace std; 
  
// If x is present in arr[0..n-1], then returns  
// index of it, else returns -1.  
int interpolationSearch(int arr[], int lo, int hi, int x) { 
    int pos; 
    // Since array is sorted, an element present  
    // in array must be in range defined by corner 
    if ( lo <= hi && x >= arr[lo] && x <= arr[hi]){  
          
        // Probing the position with keeping  
        // uniform distribution in mind. 
        pos = lo + (((double)( hi - lo ) / (arr[hi] - arr[lo])) 
                                         * (x - arr[lo])); 
          
        // Condition of target found 
        if( arr[pos] == x ) 
            return pos; 
        // If x is larger, x is in right sub array 
        if( arr[pos] < x ) 
            return interpolationSearch(arr, pos + 1, hi, x); 
        // If x is smaller, x is in left sub array 
        if( arr[pos] > x ) 
            return interpolationSearch(arr, lo, pos - 1, x); 
    } 
    return -1; 
} 
```

------



### Exponential Search

**Intro:** The name of this searching algorithm may be misleading as it works in O(Log n) time. The name comes from the way it searches an element.

**Method:**

We have discussed, [linear search](#Linear Search), [binary search](#Binary Search) for this problem. Exponential search involves two steps: 

1. Find range where element is present
2. Do Binary Search in above found range.

**How to find the range where element may be present?** 
The idea is to start with subarray size 1, compare its last element with x, then try size 2, then 4 and so on until last element of a subarray is not greater. 
Once we find an index i (after repeated doubling of i), we know that the element must be present between i/2 and i (Why i/2? because we could not find a greater value in previous iteration)

**Implementation:**

* Time Complexity : O(Log n) 

* Auxiliary Space: recursive Binary Search O(Log n); iterative Binary Search O(1).

```C++
// C++ program to find an element x in a sorted array using Exponential search.

int binarySearch(int arr[], int, int, int);

int exponentialSearch(int arr[], int n, int x){
    // If x is present at firt location itself
    if (arr[0] == x)
        return 0;
 
    // Find range for binary search by repeated doubling
    int i = 1;
    while (i < n && arr[i] <= x)
        i = i*2;
 
    //  Call binary search for the found range.
    return binarySearch(arr, i/2, min(i, n-1), x);
}
```

------



### Fibonacci Search

**Problem:** Given a sorted array arr[] of size n and an element x to be searched in it. Return index of x if it is present in array else return -1.

**Fibonacci Search:** Fibonacci Search is a comparison-based technique that uses Fibonacci numbers to search an element in a sorted array.

**Similarities with Binary Search:**

1. Works for sorted arrays
2. A Divide and Conquer Algorithm.
3. Has Log n time complexity.

**Differences with Binary Search**:

1. Fibonacci Search divides given array in unequal parts
2. Binary Search uses division operator to divide range. Fibonacci Search doesn’t use /, but uses + and -. The division operator may be costly on some CPUs.
3. Fibonacci Search examines relatively closer elements in subsequent steps. So when input array is big that cannot fit in CPU cache or even in RAM, Fibonacci Search can be useful.

**Background:**

Fibonacci Numbers are recursively defined as F(n) = F(n-1) + F(n-2), F(0) = 0, F(1) = 1. First few Fibonacci Numbers are 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, …

**Observations:**

Below observation is used for range elimination, and hence for the O(log(n)) complexity.

```
F(n - 2) &approx; (1/3)*F(n) and 
F(n - 1) &approx; (2/3)*F(n).
```

**Algorithm:**

Let the searched element be x.

The idea is to first find the smallest Fibonacci number that is greater than or equal to the length of given array. Let the found Fibonacci number be fib (m’)th Fibonacci number. We use (m-2)’th Fibonacci number as the index (If it is a valid index). Let (m-2)’th Fibonacci Number be i, we compare arr[i] with x, if x is same, we return i. Else if x is greater, we recur for sub-array after i, else we recur for sub-array before i.

Below is the complete algorithm
Let arr[0..n-1] be the input array and element to be searched be x.

1. Find the smallest Fibonacci Number greater than or equal to n. Let this number be fib_M [(m’)th Fibonacci Number]. Let the two Fibonacci numbers preceding it be fib_Mm_1 [(m-1)’th Fibonacci Number] and fib_Mm_2 [(m-2)’th Fibonacci Number].
2. While the array has elements to be inspected:
   1. Compare x with the last element of the range covered by fib_Mm_2
   2. **If** x matches, return index
   3. **Else If** x is less than the element, move the three Fibonacci variables two Fibonacci down, indicating elimination of approximately rear two-third of the remaining array.
   4. **Else** x is greater than the element, move the three Fibonacci variables one Fibonacci down. Reset offset to index. Together these indicate elimination of approximately front one-third of the remaining array.
3. Since there might be a single element remaining for comparison, check if fib_Mm_1 is 1. If Yes, compare x with that remaining element. If match, return index.

**Implementation:**

```C
// C program for Fibonacci Search

// Utility function to find minimum of two elements 
int min(int x, int y) { return (x<=y)? x : y; } 

int fibMonaccianSearch(int arr[], int x, int n) {
    /* Initialize fibonacci numbers */
    int fibMMm2 = 0;   		// (m-2)'th Fibonacci No. 
    int fibMMm1 = 1;   		// (m-1)'th Fibonacci No. 
    int fibM = fibMMm2 + fibMMm1;  // m'th Fibonacci
    
    /* fibM is going to store the smallest 
       Fibonacci Number greater than or equal to n */
    while (fibM < n) 
    { 
        fibMMm2 = fibMMm1; 
        fibMMm1 = fibM; 
        fibM  = fibMMm2 + fibMMm1; 
    }
    
    // Marks the eliminated range from front 
    int offset = -1; 
    
    /* while there are elements to be inspected. 
       Note that we compare arr[fibMm2] with x. 
       When fibM becomes 1, fibMm2 becomes 0 */
    while(fibM > 1){
        // Check if fibMm2 is a valid location 
        int i = min(offset + fibMMm2, n-1); 
        
        /* If x is greater than the value at index fibMm2, 
           cut the subarray array from offset to i */
        if (arr[i] < x) { 
            fibM  = fibMMm1; 
            fibMMm1 = fibMMm2; 
            fibMMm2 = fibM - fibMMm1; 
            offset = i; 
        } 
        /* If x is greater than the value at index fibMm2, 
           cut the subarray after i+1  */
        else if (arr[i] > x){ 
            fibM  = fibMMm2; 
            fibMMm1 = fibMMm1 - fibMMm2; 
            fibMMm2 = fibM - fibMMm1; 
        } 
        /* element found. return index */
        else return i; 
    }
    /* comparing the last element with x */
    if(fibMMm1 && arr[offset+1] == x) return offset+1; 
  
    /*element not found. return -1 */
    return -1; 
}
```

**Illustration:**
Let us understand the algorithm with below example:

|   i    |  0   |  1   |  2   |  3   |  4   |  5   |  6   |  7   |  8   |  9   |  10  |  11  |
| :----: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| arr[i] |  2   |  8   |  9   |  35  |  44  |  53  |  62  |  71  |  85  |  95  |  96  |  97  |

Illustration assumption: 0-based indexing. Target element x is 85. Length of array n = 12.

Smallest Fibonacci number greater than or equal to 12 is 13. As per our illustration,  fib_Mm_2 = 5, fib_Mm_1 = 8, and fib_M = 13.

Another implementation detail is the offset variable (zero?-1 initialized). It marks the range that has been eliminated, starting from the front. We will update it time to time.

Now since the offset value is an index and all indices including it and below it have been eliminated, it only makes sense to add something to it. Since fib_Mm_2 marks approximately one-third of our array, as well as the indices it marks are sure to be valid ones, we can add fib_Mm_2 to offset and check the element at index i = min(offset + fib_Mm_2, n - 1).

| fib_Mm_2 | fib_Mm_1 | fib_M | offset |  i   | arr[i] |         Consequence         |
| :------: | :------: | :---: | :----: | :--: | :----: | :-------------------------: |
|    5     |    8     |  13   |   -1   |  4   |   44   | Move one down, reset offset |
|    3     |    5     |   8   |   4    |  7   |   71   | Move one down, reset offset |
|    2     |    3     |   5   |   7    |  9   |   95   |        Move two down        |
|    1     |    1     |   2   |   7    |  8   |   80   |          return i           |

**Time Complexity analysis:**

(In fact, i don't know what is saying as below, but i can prove i write what the author write without lost any character.)

The worst case will occur when we have our target in the larger (2/3) fraction of the array, as we proceed to find it. In other words, we are eliminating the smaller (1/3) fraction of the array every time. We call once for n, then for(2/3) n, then for (4/9) n and henceforth.
$$
fib(n) = [\frac{1}{\sqrt5}(\frac{1+\sqrt5}{2})^n] ≈ c*1.62^n
$$

```
for n ~ c*1.62^n. we make O(n') comparisons. We thus need O(log n) comparisons.
```

 

------



### 分块查找（索引顺序查找）

**前提：**算法实现除了需要查找表本身之外，还需要根据查找表建立一个索引表。建立的索引表要求按照关键字进行升序排序，查找表要么整体有序，要么分块有序。分块有序指的是第二个子表中所有关键字都要大于第一个子表中的最大关键字，第三个子表的所有关键字都要大于第二个子表中 的最大关键字，依次类推。

