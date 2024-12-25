# Understanding O(log n) Time Complexity in Swift
Time complexity counts how many times critical operations are executed.

**Understanding O(1)**
Time Complexity: O(1) (constant time).

## Case 1: O(1)

```swift
func printFirstNumber() {
    print(1)  // This is always 1 operation, no matter the input size.
}
```

**Understanding O(n))**
The total number of operations depends linearly on n.Time complexity is O(n) (linear time), because the number of operations grows directly with the size of the input.

## Example: O(n)

Here’s an example which demonstrates how O(n) works:

```swift
func printNumbers(_ n: Int) {
    for i in 1...n {  // Loop runs n times
        print(i)      // Printing is a single operation
    }
}

```

**Understanding O(n²)**
O(n²) (quadratic time), because for every iteration of the outer loop, the inner loop runs n times.

## Example:O(n²)

Here’s an example which demonstrates how O(n²) works:

```swift
func printPairs(_ n: Int) {
    for i in 1...n {         // Outer loop runs n times
        for j in 1...n {     // Inner loop also runs n times
            print("\(i), \(j)")  // Print happens n * n times
        }
    }
}
```
**Understanding O(log n)**
O(log n) time complexity means the size of the problem reduces very quickly, typically by **cutting it in half at every step**. A common example of an O(log n) algorithm is **binary search**, which repeatedly divides the search range into two parts.

## Example: Binary Search in Swift

Here’s an example of binary search, which demonstrates how O(log n) works:

```swift
func binarySearch(_ array: [Int], target: Int) -> Bool {
    var low = 0
    var high = array.count - 1

    while low <= high {
        let mid = (low + high) / 2  // Find the middle index

        if array[mid] == target {
            return true  // Target found
        } else if array[mid] < target {
            low = mid + 1  // Search the right half
        } else {
            high = mid - 1  // Search the left half
        }
    }

    return false  // Target not found
}
```
