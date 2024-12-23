## 1. Reference Counting

**Concept:**  
- Each reference increments the count of the object in memory
- Each dereference decrements the count of the object in memory
- When the object's count drops to 0, it is removed from memory

**Example:**
```swift
class Developer {
  var name: String

  init(name: String) { self.name = name ; print("Developer initialized") }

  deinit { print("Developer deinitialized") }
}

// To make it easier to deinitialize the object, I set it as optional (= nil)
var developer: Developer?
// I create the first reference, so the object's count is +1
developer = Developer(name: "Lucas")

// Let's create other variables to increment the count
var developer1 = developer // +1 ––> 2
var developer2 = developer // +1 ––> 3
var developer3 = developer // +1 ––> 4

// Now let's deinitialize the variables
developer = nil // -1 ––> 3
developer2 = nil // -1 ––> 2
developer1 = nil // -1 ––> 1

// I can still access the `name` property of developer3
developer3?.name
developer3 = nil // -1 ––> 0 ––> The object is removed from memory, triggering `deinit`

print("–––––––––")                             // Reference count = 0, Alice is deallocated


OUTPUT: 
Developer initialized
Developer deinitialized
–––––––––
```
## 2. Strong Reference Counting

**Concept:**  
- By default, all references to an object are STRONG
- ARC can often cause issues, especially when two classes reference each other and make **Retain cycle**

**Example:**
```swift
class Job {
  var person: Person?

  deinit { print("Job deallocated") }
}

class Person {
  var job: Job?

  deinit { print("Person deallocated") }
}

var joe: Person? = Person() // Person: +1 ––> 1
var deve: Job? = Job() // Job: +1 ––> 1

joe?.job = deve // Job: +1 ––> 2
deve?.person = joe // Person: +1 ––> 2

// Now let's deinitialize the objects & observe the `deinit`
joe = nil // Person: -1 ––> 1
deve = nil // Job: -1 ––> 1

// Both objects remain in memory because their count is still 1 (hence no `deinit` triggered)
// However, the variables are nil, so the objects' properties are no longer accessible
joe?.job
deve?.person

// This phenomenon is called a retain cycle
// It keeps objects in memory even though they are inaccessible
// It happens because both classes have STRONG references to each other

print("––––––––––––––––")

```
## 3. weak Reference 

**Concept:**  
- By default, all references to an object are STRONG
- ARC can often cause issues, especially when two classes reference each other and make **Retain cycle**

| **Scenario**                  | **Use Weak Reference?**       | **Example**                                         |
|-------------------------------|-------------------------------|---------------------------------------------------|
| **Delegate pattern**          | Yes                           | A `Timer` class notifying a `ViewController`.      |
| **Parent-child relationships**| Yes (child → parent)          | A `Child` referencing its `Parent`.                |
| **Closures capturing self**   | Yes                           | A closure inside `Downloader` referencing `ViewController`. |
| **Child owned by parent**     | No                            | A `UIViewController` owning its `UIView`.          |


**Example1: Delegate Pattern:**
```swift
// Protocol that defines the delegate's responsibilities
protocol TimerDelegate: AnyObject {
    func timerDidFinish()
}

// Timer class uses a delegate to notify when the timer finishes
class Timer {
    // Delegate is declared as a weak reference to avoid a strong retain cycle
    weak var delegate: TimerDelegate? 

    func startTimer() {
        print("Timer started")
        // Simulate timer completion after some work
        DispatchQueue.main.asyncAfter(deadline: .now() + 2.0) {
            self.delegate?.timerDidFinish() // Notify delegate when done
        }
    }
}

// The class acting as the delegate
class ViewController: TimerDelegate {
    var timer = Timer() // Strong reference to the timer

    init() {
        timer.delegate = self // Set self as the delegate
        timer.startTimer()
    }

    func timerDidFinish() {
        print("Timer finished!")
    }

    deinit {
        print("ViewController is deallocated")
    }
}

// Example usage
var viewController: ViewController? = ViewController()
viewController = nil // When the ViewController is deallocated, no retain cycle happens because delegate is weak.

```
**Concept:**  
- The Timer class has a delegate to notify when the timer is finished.
- The delegate is weak because the Timer shouldn’t keep the ViewController alive.
- When ViewController is set to nil, it’s deallocated, breaking the retain cycle.
 
**Example2: Parent-Child Relationship :**
```swift
class Parent {
    var child: Child? // Strong reference to the child

    init() {
        print("Parent is created")
    }

    deinit {
        print("Parent is deallocated")
    }
}

class Child {
    // Use weak reference to avoid a retain cycle
    weak var parent: Parent? 

    init() {
        print("Child is created")
    }

    deinit {
        print("Child is deallocated")
    }
}

// Example usage
var parent: Parent? = Parent() // Parent is created
var child: Child? = Child()   // Child is created

// Set references
parent?.child = child         // Parent holds a strong reference to Child
child?.parent = parent        // Child holds a weak reference to Parent

// Break references
parent = nil // Parent is deallocated, Child is also deallocated because Parent had a strong reference to it.
child = nil  // No retain cycle exists, so memory is properly cleaned up.


```
**Concept:**  
- The Parent owns the Child, so it has a strong reference.
- The Child refers back to the Parent, but as a weak reference.
- When parent is deallocated, the child is also deallocated, and no retain cycle occurs.
  
**Example3: Closures Capturing Self  :**

When a closure is defined inside an object and captures self, it creates a strong reference to self. Use [weak self] to break the cycle.

```swift
class Downloader {
    func startDownload(completion: @escaping () -> Void) {
        print("Download started")
        // Simulate a download delay
        DispatchQueue.global().asyncAfter(deadline: .now() + 2.0) {
            completion() // Call the completion handler
        }
    }
}

class ViewController {
    var downloader = Downloader() // Strong reference to Downloader

    func start() {
        // Avoid retain cycle by using [weak self]
        downloader.startDownload { [weak self] in
            self?.updateUI() // Only execute if self still exists
        }
    }

    func updateUI() {
        print("UI updated")
    }

    deinit {
        print("ViewController is deallocated")
    }
}

// Example usage
var viewController: ViewController? = ViewController()
viewController?.start()
viewController = nil // ViewController is deallocated, no retain cycle exists.

```
**Concept:**  
- Closures inside startDownload capture self (the ViewController).
- Using [weak self] ensures the closure doesn’t keep self alive if it’s no longer needed.
-If ViewController is deallocated, the closure won’t crash because it safely handles the optional self.

## 4. Unowned Reference

**Concept:**  
- Unowned references are non-optional (unowned var), which means they always assume the referenced object exists.
- Attempting to use an unowned reference after the object is deallocated causes a runtime crash.
- When the object's count drops to 0, it is removed from memory

**Example:**
```swift
class Bank {
    // Bank has a strong reference to the Account
    var account: Account?

    init() {
        print("Bank is created")
    }

    deinit {
        print("Bank is deallocated")
    }
}

class Account {
    // Unowned reference: Assumes the Bank will always exist while the Account exists
    unowned var bank: Bank 

    init(bank: Bank) {
        self.bank = bank
        print("Account is created")
    }

    deinit {
        print("Account is deallocated")
    }
}

// MARK: - Working Example
print("=== Working Example ===")
// Step 1: Create a Bank
var bank: Bank? = Bank() // Output: "Bank is created"

// Step 2: Create an Account linked to the Bank
bank?.account = Account(bank: bank!) // Output: "Account is created"

// Step 3: Remove the Bank
// When the Bank is removed, both the Bank and the Account are deallocated because
// the Bank held a strong reference to the Account.
bank = nil
// Output:
// "Bank is deallocated"
// "Account is deallocated"

print("\n=== Crash Example ===")

// MARK: - Crash Example
class AccountCrash {
    // Unowned reference assumes the Bank will always exist
    unowned var bank: Bank 

    init(bank: Bank) {
        self.bank = bank
        print("AccountCrash is created")
    }

    deinit {
        print("AccountCrash is deallocated")
    }
}

// Step 1: Create a Bank
var crashingBank: Bank? = Bank() // Output: "Bank is created"

// Step 2: Create an Account linked to the Bank
var accountCrash: AccountCrash? = AccountCrash(bank: crashingBank!) // Output: "AccountCrash is created"

// Step 3: Remove the Bank
// When the Bank is deallocated, the AccountCrash still holds an unowned reference.
// Accessing this unowned reference after the Bank is deallocated causes a crash.
crashingBank = nil // Output: "Bank is deallocated"

// Step 4: Attempt to access the unowned reference
// This will crash because the AccountCrash still tries to use the Bank,
// but the Bank no longer exists.
print(accountCrash?.bank) // CRASH: The Bank no longer exists in memory

```
