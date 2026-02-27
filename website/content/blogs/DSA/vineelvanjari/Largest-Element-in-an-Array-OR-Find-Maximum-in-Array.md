##  Problem
Given an array of integers, find the largest element present in it.

---
##  Example 1
Input:  
`[8, 2, 10, -3, 15, 12]`
Output:  
`15`

---
##  Example 1 – Normal Explanation
The array contains several numbers: 8, 2, 10, -3, 15, and 12.
To determine the output, we identify the number that is greater than all other elements in the array.
Among these values, **15** is numerically larger than 8, 2, 10, -3, and 12.
Since no element exceeds 15, the largest element in the array is **15**, which becomes the final output.

---
## 💻 Code (Complete Program)

```cpp
#include <iostream>
using namespace std;

int findMax(int a[], int n) {
    int max = a[0];
    for(int i = 1; i < n; i++) {
        if(a[i] > max)
            max = a[i];
    }
    return max;
}

int main() {
    int arr[] = {8, 2, 10, -3, 15, 12};
    int n = sizeof(arr) / sizeof(arr[0]);

    int result = findMax(arr, n);
    cout << "Largest Element: " << result;

    return 0;
}
```

### ⏱ Time Complexity
**O(n)**  
We traverse the array once, comparing each element.
### 📦 Space Complexity
**O(1)**  
We use only one extra variable (`max`), regardless of input size.

---
## Example 1 – Coding Explanation
The program defines a function `findMax` that accepts an array and its size.
Inside the function, the first element is stored as the initial maximum value. This ensures correct handling even if all numbers are negative.
The loop starts from index 1 and continues until the last element. For each iteration, the current element is compared with the stored maximum. If a larger value is found, the maximum is updated.
After checking all elements, the function returns the final maximum value.
In `main()`, the array is declared and passed to the function. The returned result is printed as the largest element.

---

## 🔗 Other Platforms

- LeetCode (Related – Kth Largest Element)  
    [https://leetcode.com/problems/kth-largest-element-in-an-array/](https://leetcode.com/problems/kth-largest-element-in-an-array/)
    
- GeeksforGeeks  
    [https://www.geeksforgeeks.org/problems/largest-element-in-array4009/1](https://www.geeksforgeeks.org/problems/largest-element-in-array4009/1)
    
- CodeChef  
    [https://www.codechef.com/practice/course/arrays/ARRAYS/problems/UWCOI20A](https://www.codechef.com/practice/course/arrays/ARRAYS/problems/UWCOI20A)
    

---