# Types of Closures in Swift

In Swift, closures are self-contained blocks of functionality that can be passed around and used in your code. There are different types of closures, each with its own use case. Below is an explanation of **global closures**, **unnamed closures**, **escaping closures**, and **auto closures**.

---

## 1. Global Closure
A **global closure** is a closure that doesn't capture any values from the surrounding context. It's not tied to any specific object or context, making it independent of where it is defined.

### Characteristics:
- It doesn't capture values.
- It's defined globally or at the file level.

### Example:
```swift
let globalClosure: () -> Void = {
    print("This is a global closure.")
}

globalClosure()  // Output: "This is a global closure."
```
## 2. Unnamed Closure
An **Unnamed Closure** is simply a closure without a name. It's often used inline.

### Characteristics:
- Short closure.
- The closure is not stored in a variable.

### Example:
```swift
// Using an unnamed closure directly as an argument
let numbers = [1, 2, 3, 4]
let squaredNumbers = numbers.map { number in
    return number * number
}

print(squaredNumbers)  // Output: [1, 4, 9, 16]

```
## 3.  Escaping Closure

A **Escaping Closure** is a closure that is passed to a function, but it may be executed after the function has returned. The closure "escapes" the function’s scope and is called at a later time, usually asynchronously.

### Characteristics:
- Often used in async functions or completion handlers.

### Example:
```swift
func fetchData(completion: @escaping (String) -> Void) {
    DispatchQueue.global().async {
        // Simulating a network call
        Thread.sleep(forTimeInterval: 2)
        completion("Data fetched")
    }
}

fetchData { result in
    print(result)  // Output: "Data fetched"
}
```
## 4.  Auto Closure
An **Auto Closure**  is a Swift attribute that automatically wraps an expression in a closure so that the expression is not evaluated immediately but only when the closure is explicitly called inside the function.

### Characteristics:
- It allows you to pass expressions without writing {}, resulting in cleaner and more readable code.
  
| Type               | When Expression is Evaluated          | Syntax                                | Notes                                         |
|--------------------|----------------------------------------|---------------------------------------|----------------------------------------------|
| Without Closure     | Immediately, before calling the function. | `checkCondition(condition: number > 10)` | Works with the result, not the expression.    |
| With Closure        | Inside the function, when `condition()` is called. | `checkCondition(condition: { number > 10 })` | You manually wrap the expression in `{}`.     |
| With `@autoclosure` | Inside the function, when `condition()` is called. | `checkCondition(condition: number > 10)` | Simplifies syntax by auto-wrapping the expression. |


### Example:
```swift
func checkCondition(condition: Bool) {
    // Here, `condition` is already a true or false value (Boolean).
    if condition {
        print("Condition is true!")  // Executes if `condition` is true.
    } else {
        print("Condition is false!")  // Executes if `condition` is false.
    }
}

let number = 5

// The expression `number > 10` is evaluated immediately.
// It checks if 5 is greater than 10, which is false.
// The result (`false`) is passed to the `checkCondition` function.
checkCondition(condition: number > 10)  
// Output: "Condition is false!"

func checkCondition(condition: () -> Bool) {
    // The `condition` is not evaluated when passed.
    // Instead, it is stored as a block of code (a closure) to be executed later.
    if condition() {  // The closure is called and evaluated here.
        print("Condition is true!")  // Executes if the result is true.
    } else {
        print("Condition is false!")  // Executes if the result is false.
    }
}

let number = 5

// The expression `number > 10` is wrapped in a closure.
// The function `checkCondition` receives the closure, not the result.
// The closure is executed inside the function when condition() is called.
checkCondition(condition: { number > 10 })  
// Output: "Condition is false!"

func checkCondition(condition: @autoclosure () -> Bool) {
    // The `condition` is not evaluated when passed.
    // It's evaluated here when condition() is explicitly called.
    if condition() {
        print("Condition is true!")  // Executes if the result is true.
    } else {
        print("Condition is false!")  // Executes if the result is false.
    }
}

let number = 5

// With @autoclosure, you don't need to wrap the expression manually in `{}`.
// The compiler automatically converts `number > 10` into a closure.
checkCondition(condition: number > 10)  
// Output: "Condition is false!"

```
## 5.  Trailing Closure

If the last parameter to a function is a closure, Swift lets you use special syntax called  **Trailing closure**   syntax. Rather than pass in your closure as a parameter, you pass it directly after the function inside braces.

### Characteristics:
- Often used in async functions or completion handlers.

### Example:
```swift
func travel(action: () -> Void) {
    print("I'm getting ready to go.")
    action()
    print("I arrived!")
}
travel() {
    print("I'm driving in my car")
}
```
## 6.  Capturing values and variables in closures

Closures can "remember" and use variables or constants from the place where they were created, even if they're used later in a different part of the code.

### Characteristics:
- Often used in async functions or completion handlers.

### Example:
```swift
// This function returns a closure that increments a value by a specified amount.
func incrementFunction(value: Int) -> () -> Int {
    // `total` is a variable that will be captured by the closure.
    var total = 0
    
    // The closure captures `total` and `value`.
    let incrementer: () -> Int = {
        total += value  // Each time the closure is called, it adds `value` to `total`.
        return total    // Returns the updated `total`.
    }
    
    return incrementer  // Returns the closure.
}

// Create a closure that increments by 5.
let incrementFive = incrementFunction(value: 5)

// Call the closure for the first time.
let result1 = incrementFive()  // `total` is 0 initially. After this call, `total` becomes 5.
// `incrementFive` remembers `total` from its surrounding context.

// Call the closure for the second time.
let result2 = incrementFive()  // Now, `total` is 5. After this call, `total` becomes 10.

// Print the results.
print("Result 1: \(result1)") // Output: 5
print("Result 2: \(result2)") // Output: 10

```
