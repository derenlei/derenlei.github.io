---
layout: post
comments: true
title: "Algorithm: Bucket Sort"
date: 2017-10-15
---

### 1. Introduction
Given n numbers $$X_{1}$$, $$X_{2}$$,..., $$X_{n}$$ drawn at random independently from the uniform distribution in [0,1], it is desired to sort them in **O(n)** expected time. Our model of computation allows the floor function to be performed in constant time. The following algorithm does the job.

### 2. Algorithm BUCKET-SORT
**Begin**
  - Step 1: Find $$X_{min}$$ and $$X_{max}$$, the points with minimum and maximum value.
  - Step 2: Divide the interval [$$X_{max}$$, $$X_{min}$$] into n-2 “buckets” or intervals of equal length.
  - Step 3: “Throw” the remaining n-2 points into their respective buckets using the floor function.
  - Step 4: For each bucket that contains more than one point sort them with any method that runs in at most quadratic worst-case time.
  - Step 5: Scan through the buckets and concatenate the sorted lists in each bucket.

**End**

### 3. Simple demonstration
An unsorted array: [6 2 4 1 5 9]   
1. Prepare 10 empty buckets   
[6 2 4 1 5 9]----------------unsorted array   
[0 0 0 0 0 0 0 0 0 0]------empty buckets   
[0 1 2 3 4 5 6 7 8 9]--------index of the bucket   

2. Pick up the frist number from the array which is 6, and put it inside the 6th bucket.   
Empty Bucket[unsorted array[0]] = unsorted array[0]   
[**6** 2 4 1 5 9]----------------unsorted array   
[0 0 0 0 0 0 **6** 0 0 0]------empty buckets   
[0 1 2 3 4 5 **6** 7 8 9]--------index of the bucket   

3. Get the next number from the unsorted array which is 2, and put it inside the 2nd bucket. The bucket index equals to the number.   
[**6** **2** 4 1 5 9]----------------unsorted array   
[0 0 **2** 0 0 0 6 0 0 0]------empty buckets   
[0 1 **2** 3 4 5 6 7 8 9]--------index of the bucket   

4. Follow the same step above and put all the number from the unsorted array to the empty buckets like this:   
[**6** **2** **4** **1** **5** **9**]----------------unsorted array   
[0 **1** **2** 0 **4** **5** **6** 0 0 **9**]------empty buckets   
[0 **1** **2** 0 **4** **5** **6** 0 0 **9**]--------index of the bucket   

0 means empty bucket, jump and pick up the numbers in order   

### 4. C++ Implementation
```sh
// Function to sort arr[] of size n using bucket sort
void bucketSort(float arr[], int n)
{
    // Create n empty buckets
    vector<float> b[n];
    
    // Put array elements in different buckets
    for (int i=0; i<n; i++)
    {
       int bi = n*arr[i]; // Index in bucket
       b[bi].push_back(arr[i]);
    }
 
    // Sort individual buckets
    for (int i=0; i<n; i++)
       sort(b[i].begin(), b[i].end());
 
    // Concatenate all buckets into arr[]
    int index = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < b[i].size(); j++)
          arr[index++] = b[i][j];
}
```

### 5. Time Complexity Analysis
Once \\(X_{min}\\) and \\(X_{max}\\) are found the algorithm processes the remaining n-2 points which are themselves uniformly distributed in [$$X_{min}$$, $$X_{max}$$]. Since we have n-2 buckets it follows that the probability that a remaining point falls in the i-th bucket is pi = 1/(n-2). In other words, the number of points that falls in bucket i is a binomial random variable, denoted by Ni, with parameters (n-2) and pi, i = 1, 2,..., n-2. If we sort each Ni using a quadratic time algorithm the total time taken by bucket sort is given by
\begin{equation}
T(n) = X_{1}N_{1}^{2} + X_{2}N_{2}^{2} + ... + X_{n-1}N_{n-2}^{2} = c\sum_{i=1}^{n-2}N_{i}^{2}
\tag{1}
\label{1}
\end{equation}
where c is a positive constant.

To find the expected time we need to take the expected value, denoted by E{.}, of \eqref{1}
\begin{equation}
E[T(n)] = c\sum_{i=1}^{n-2}E[N_{i}^{2}]
\tag{2}
\label{2}
\end{equation}

Thus we need to know the expected value of the square of a random variable. Now, for any random variable X we have
\begin{equation}
E[X^{2}] = \mu^{2} + Var(X)
\tag{3}
\label{3}
\end{equation}

This is easy to see from the definition of the variance since   
\begin{equation}
E[X^{2}] = E[(X - \mu)^{2}] \newline
= E[X^{2} -2X\mu + \mu^{2}] \newline
= E[X^{2}] -2E[X]\mu + \mu^{2} \newline
= E[X^{2}] - \mu^{2}
\end{equation}

Furthermore, for a binomial random variable Ni with parameters (n-2) and pi we have that:
\begin{equation}
\mu = (n - 2)p_{i}
\tag{4}
\label{4}
\end{equation}
and   
\begin{equation}
Var(X) = (n - 2)p_{i}(1 - p_{i})
\tag{5}
\label{5}
\end{equation}

Substituting \eqref{4} and \eqref{5} into \eqref{3} and using $$p_{i} = \frac{1}{n - 2}$$ yields
\begin{equation}
E[N_{i}^{2}] = 2 - \frac{1}{n - 2}
\tag{6}
\label{6}
\end{equation}

Substituting \eqref{6} into \eqref{2} we have
\begin{equation}
E[T(n)] = c\sum_{i=1}^{n-2}(2 - \frac{1}{n - 2})   
= 2cn - 5c   
= O(n) - O(1)   
= O(n)   
\end{equation}

Therefore, for points uniformly distributed in the unit interval, algorithm bucket sort runs in linear expected time.
