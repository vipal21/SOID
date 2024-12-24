1. **What is a protocol in Swift?**  
   A protocol defines a blueprint of methods, properties, and other requirements that can be adopted by classes, structs, or enums. It sets rules that any type following it must follow.

 **Example:**
   
    ```swift
    // Without Dependency Injection: Tight coupling
    
    protocol DataManagerProtocol {
      func fetchData() -> [String]
    }
    ```
3. **What is protocol conformance in Swift?**  
   Protocol conformance means a class, struct, or enum agrees to follow the rules and implement the features defined by a protocol.
   
**Example:**
   
    ```swift
    // Without Dependency Injection: Tight coupling
    
    protocol DataManagerProtocol {
      func fetchData() -> [String]
    }
     class MockDataManager: DataManagerProtocol {
      func fetchData() -> [String] {
          return ["Mock Data"]  // Mocked data
      }
   }
    ```
  5. **Can a Swift class conform to multiple protocols**  
   Yes, a Swift class can conform to multiple protocols by listing them separated by commas.
   
  **Example:**
    
   ```swift
    protocol UITableViewDelegate{}
    protocol UITableViewDataSource{}
    class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
      // TableView DataSourece and Delegate methodes goes here
    }
   ```
  4. **What is a protocol extension in Swift?**  
   A protocol extension allows you to provide default implementations for methods in a protocol. 
   
  **Example:**
   ```swift
        protocol MyProtocol {
       func myMethod()
    }
    
    extension MyProtocol {
       func myMethod() {
         print("Default implementation")
       }
    }
   ```
  5. **What is a protocol extension in Swift?**  
   A protocol extension allows you to provide default implementations for methods in a protocol. 
  
  **Example:**
  
   ```swift
      protocol Drivable {
          func drive()
      }
      
      class Car: Drivable {
          func drive() {
              print("The car is driving.")
          }
      }
      
      
      let myCar = Car()
      
      // Check if instances conform to the Drivable protocol
      if myCar is Drivable {
          print("myCar conforms to Drivable.")  // Output: "myCar conforms to Drivable."
      } else {
          print("myCar does not conform to Drivable.")
      }

   ```
  6. **What is a protocol composition in Swift?**  
   Protocol composition allows you to combine multiple protocols into a single requirement. It’s done using the `&` operator.
  
  **Example:**
  
   ```swift
        protocol Protocol1 {
     // protocol 1 requirements
     }
    
    protocol Protocol2 {
     // protocol 2 requirements
     }
    // Protocol composition
    typealias CombinedProtocol = Protocol1 & Protocol2
        }
   ```
  7. **How do you create a generic protocol in Swift?**  
   You can create a generic protocol in Swift by defining associated types.
  
  **Example:**
  
   ```swift
     protocol MyGenericProtocol {
       associatedtype MyType
       func processValue(_ value: String)
    }
    
    // Conforming to MyProtocol with String as MyType
    struct StringProcessor: MyProtocol {
        typealias MyType = String
        
        func processValue(_ value: String) {
            // Implementation for processing a String value
        }
    }
   ```
  8. **How can you achieve delegation using protocols in Swift**  
  The delegate pattern is like asking someone (the delegate) to do a specific job for you. You (the delegator) don’t do the task yourself but give the responsibility to someone else who knows how to do it.

**Delegate Pattern in Swift**

**Real-World Analogy:**

Imagine you’re the manager at a restaurant. You might have a **waiter** (the **delegate**) who handles specific tasks for you, like taking orders and delivering food to the customers. You don't do everything yourself; you delegate the responsibility to the waiter.

**How It Works in Swift:**

 1. **The Protocol**:
   The **manager** defines what tasks need to be done, but doesn’t know how they will be done. This is done using a **protocol**. 
   
   The protocol specifies the rules or methods that the delegate must follow to complete the task.

2. **The Delegator**:
   The **manager** class is responsible for delegating tasks. It doesn’t perform the actual work but expects someone else (the **delegate**) to handle it.

   The delegator asks the delegate to perform the task, but it doesn't care how the task is done.

3. **The Delegate**:
   The **waiter** (the **delegate**) will perform the task as instructed by the manager. The waiter knows how to handle the task and fulfills the job as required.
   
   The delegate conforms to the protocol and implements the required methods to carry out the task.
  
  ```swift
  import Foundation
  
  // Step 1: Define the Protocol (defines what tasks the delegate must perform)
  // This is the blueprint that the delegate must follow.
  protocol TaskDelegate {
      func completeTask(task: String)  // The delegate is responsible for completing this task
  }
  
  // Step 2: Define the Delegator (Manager) Class
  // The manager is responsible for assigning tasks but does not perform them.
  class Manager {
      var taskDelegate: TaskDelegate?  // The delegator (Manager) assigns tasks to the delegate (Waiter)
      
      // The delegator assigns a task to the delegate
      func assignTask(task: String) {
          print("Manager: Assigning task '\(task)' to the delegate.")  // Delegator assigns task
          taskDelegate?.completeTask(task: task)  // Delegate performs the task
      }
  }
  
  // Step 3: Define the Delegate (Waiter) Class
  // The delegate (Waiter) conforms to the protocol and performs the assigned task.
  class Waiter: TaskDelegate {
      func completeTask(task: String) {
          print("Waiter: Task '\(task)' is being completed!")  // The delegate completes the task
      }
  }
  
  // Step 4: Use the Delegation Pattern
  let manager = Manager()   // Create the Manager (Delegator)
  let waiter = Waiter()     // Create the Waiter (Delegate)
  
  manager.taskDelegate = waiter  // Set the waiter (delegate) for the manager (delegator)
  
  manager.assignTask(task: "Serve Food")  // Manager assigns the task, and the waiter performs it
  

   ```
**Key Concepts:**

- **Delegation**: The process of assigning tasks to another object (delegate).
- **Protocol**: Defines a set of methods or requirements that the delegate must implement.
- **Delegate**: An object that implements the protocol and performs the assigned tasks.
- **Delegator**: An object that assigns tasks to the delegate.

**Delegate Pattern Benefits:**

- **Separation of Concerns**: The delegator doesn’t need to know how the task is done, just that the delegate can handle it. This keeps responsibilities separate and organized.
- **Flexibility**: You can change the delegate without changing the delegator’s code. This makes the system more flexible and easier to maintain.
- **Cleaner Code**: Delegation allows you to write cleaner, more modular code by splitting responsibilities between the delegator and delegate.

9. **Difference between a protocol and an abstract class**  
   The main difference is abstract class in some languages can have both method declarations and some method implementations.

10. **How do you use protocol-oriented programming for dependency injection in Swift**  
    Defining protocols for dependencies and injecting instances conforming to those protocols. This allows for more flexible and testable code.
  
  **Example:**
  
   ```swift
    // Protocol defining a network service
    protocol NetworkService {
        func fetchData() -> String
    }
    
    // Concrete implementation of the real network service
    class RealNetworkService: NetworkService {
        func fetchData() -> String {
            // Simulate fetching data from a real network
            return "Data fetched from the real network"
        }
    }
    
    // Another concrete implementation of the mock network service for testing
    class MockNetworkService: NetworkService {
        func fetchData() -> String {
            // Simulate fetching mock data for testing
            return "Mock data fetched for testing"
        }
    }

    // Object that depends on NetworkService via dependency injection
    class DataClient {
        let networkService: NetworkService
    
        init(networkService: NetworkService) {
            self.networkService = networkService
        }
    
        func processData() -> String {
            // Use the injected network service
            return "Processed: " + networkService.fetchData()
        }
    }
    
    // Usage example
    let realNetworkService = RealNetworkService()
    let mockNetworkService = MockNetworkService()
    
    // Dependency injection using RealNetworkService
    let dataClientWithRealNetwork = DataClient(networkService: realNetworkService)
    print(dataClientWithRealNetwork.processData()) // Output: Processed: Data fetched from the real network
    
    // Dependency injection using MockNetworkService for testing
    let dataClientWithMockNetwork = DataClient(networkService: mockNetworkService)
    print(dataClientWithMockNetwork.processData()) // Output: Processed: Mock data fetched for testing
   ```
 11. **Explain the concept of a protocol’s self requirement in Swift.**  
  The Self requirement in a protocol indicates that conforming types must use their own type for certain method return types or property types. 
  
  **Example:**
  
   ```swift
  // Define the protocol
  protocol Printable {
      associatedtype DataType
      // Self requirement: The associated type DataType must be the same as the conforming type.
      func printSelf(data: Self)
  }
  
  // Implement the protocol in a struct
  struct Document: Printable {
      var content: String  // The content of the document
  
      // Implement the printSelf method, where the associated type DataType is the same as the conforming type
      func printSelf(data: Document) {
          print("Printing self (Document): \(data.content)")
      }
  }
  
  // Create an instance of Document
  let document = Document(content: "Swift Programming Language")
  
  // Call printSelf with the document instance itself
  document.printSelf(data: document)
  
          }
   ```
   12. **Explain the concept of conditional conformance or 'where' clause in Swift protocols.**  
  In Swift, conditional conformance allows a type to conform to a protocol only when certain conditions are met.
When a type conforms to a protocol conditionally, it means that the type will conform to the protocol only when certain conditions on the associated types or constraints are satisfied. The conditions are checked using the where clause.
  
  **Example:**
  
   ```swift
// Define a protocol
protocol Summable {
    func sum() -> Int
}

// Define a struct that conforms to the protocol only when the type is an array of integers
extension Array: Summable where Element == Int {
    func sum() -> Int {
        return self.reduce(0, +)
    }
}

// Example usage:
let intArray = [1, 2, 3, 4, 5]
let result = intArray.sum()  // Outputs: 15

let stringArray = ["a", "b", "c"]
// stringArray.sum()  // This would give an error because the condition (Element == Int) isn't satisfied

protocol GlobalActorProtocolOnly: GlobalActor {}
protocol ReferenceTypeProtocolOnly: AnyObject {}
protocol ActorProtocolOnly: Actor {}
protocol GlobalActorProtocolOnly: GlobalActor {}

   ```
