# What is Dependency Injection (DI)?

 DI provides a way for a class to get its required objects (dependencies) from the outside, rather than directly instantiating them.
 ## Summary of Problems and Solutions

| **Problem**                | **Solution with Dependency Injection (DI)**                                                                 |
|-----------------------------|-------------------------------------------------------------------------------------------------------------|
| **Tight Coupling**          | Use DI to inject dependencies, decoupling classes from their concrete implementations.                     |
| **Difficult Unit Testing**  | DI makes it easy to inject mock or stub dependencies for testing.                                           |
| **Lack of Flexibility**     | DI allows for swapping out dependencies easily without modifying classes.                                   |
| **Violates SRP**            | DI helps adhere to SRP by moving the responsibility of creating dependencies outside the class.             |
| **Control over Object Creation** | DI inverts control over object creation, making code more flexible and decoupled.                        |


## 📚 Why Are We Using Dependency Injection?

1. **Loose Coupling**  
    **Problem:**
    Without Dependency Injection, classes are tightly coupled to their dependencies. This means that classes create and manage their own dependencies, which makes the code difficult to maintain and       extend. If you need to change or swap a dependency, you’ll need to modify the class directly.
    **Solution with DI: **  Dependency Injection allows you to inject dependencies from outside the class, making it easier to change or swap dependencies without modifying the class itself.
    **Example: **
   
    ```swift
    // Without Dependency Injection: Tight coupling
    
    class ViewModel {
        var dataManager: DataManager = DataManager()  // Direct instantiation
        func loadData() {
            dataManager.fetchData()
        }
    }
    
    ```

    In this case, ViewModel directly creates a DataManager, so if we wanted to change how data is fetched (e.g., from a network instead of a local database), we would have to modify the ViewModel  class.
   With DI:
   ```swift
       // With Dependency Injection: Loose coupling
    
    class ViewModel {
       var dataManager: DataManagerProtocol  // Dependency is injected
        // Constructor Injection
      init(dataManager: DataManagerProtocol) {
          self.dataManager = dataManager
      }

     func loadData() {
         dataManager.fetchData()
      }
    }
    ```


2. **Easier Unit Testin**  
    **Problem:**
    Without Dependency Injection, classes are tightly coupled to their dependencies. This means that classes create and manage their own dependencies, which makes the code difficult to maintain and       extend. If you need to change or swap a dependency, you’ll need to modify the class directly.
    **Solution with DI: **  Dependency Injection allows you to inject dependencies from outside the class, making it easier to change or swap dependencies without modifying the class itself.
    **Example: **
   ```swift
        // Without Dependency Injection: Difficult to mock dependencies
     class ViewModel {
         var dataManager: DataManager = DataManager()  // Direct instantiation
     
         func loadData() {
             dataManager.fetchData()
         }
     }
   ```
   In this case, it’s hard to mock DataManager for testing, especially if it interacts with a database or makes network requests.

With DI:
  ```swift
   // With Dependency Injection: Easy to mock dependencies for testing
  protocol DataManagerProtocol {
      func fetchData() -> [String]
  }
  
  class ViewModel {
      var dataManager: DataManagerProtocol
  
      // Constructor Injection
      init(dataManager: DataManagerProtocol) {
          self.dataManager = dataManager
      }
  
      func loadData() {
          dataManager.fetchData()
      }
  }
  
  // In tests, inject a mock or stub
  class MockDataManager: DataManagerProtocol {
      func fetchData() -> [String] {
          return ["Mock Data"]  // Mocked data
      }
  }
  
  let mockDataManager = MockDataManager()
  let viewModel = ViewModel(dataManager: mockDataManager)
  viewModel.loadData()  // Uses mocked data

  ```
   Now, you can inject MockDataManager or any other mock dependency in the test, allowing you to test the ViewModel without relying on real data sources.

 3. **Flexibility and Extensibility**  
    **Problem:**
    Without DI, changing the behavior of a class often requires modifying the class itself. For example, if you want to change how the data is fetched (from a database to an API), you would have to change the ViewModel class.
    **Solution with DI: **  DI allows you to easily swap out implementations without modifying the ViewModel. You can inject different dependencies based on context or environment.
    **Example: **
   ```swift
        // Without Dependency Injection: Difficult to mock dependencies
     class ViewModel {
         var dataManager: DataManager = DataManager()  // Direct instantiation
     
         func loadData() {
             dataManager.fetchData()
         }
     }
   ```
  To swap the data fetching logic, you need to modify the ViewModel directly.

With DI:
  ```swift
   // With Dependency Injection: High flexibility
protocol DataManagerProtocol {
    func fetchData() -> [String]
}

class ViewModel {
    var dataManager: DataManagerProtocol

    // Constructor Injection
    init(dataManager: DataManagerProtocol) {
        self.dataManager = dataManager
    }

    func loadData() {
        dataManager.fetchData()
    }
}

// Inject a new implementation as needed
class APIManager: DataManagerProtocol {
    func fetchData() -> [String] {
        return ["Data from API"]
    }
}

let apiManager = APIManager()
let viewModel = ViewModel(dataManager: apiManager)
viewModel.loadData()  // Now fetches data from the API


  ```
  Now, you can easily inject APIManager, DataManager, or any other class that conforms to DataManagerProtocol, making the ViewModel more flexible and extendable.
  
 4. **Single Responsibility Principle (SRP)**  
    **Problem:**
    Without Dependency Injection, classes may have to manage both their own functionality and the instantiation of their dependencies, violating the Single Responsibility Principle. This increases the complexity of the class.
    **Solution with DI: **  DI helps to separate the responsibility of creating dependencies from the class that uses them. This keeps the class focused on its primary responsibility, adhering to the SRP.
    **Example: **
   ```swift
        // Without Dependency Injection: Violating SRP
class ViewModel {
    var dataManager: DataManager = DataManager()  // Responsible for both its logic and creating the dependency

    func loadData() {
        dataManager.fetchData()
    }
}

   ```
 In this case, the ViewModel has the extra responsibility of creating the DataManager, which could lead to unnecessary complexity.

With DI:
  ```swift
// With Dependency Injection: Adheres to SRP
class ViewModel {
    var dataManager: DataManagerProtocol

    // Constructor Injection
    init(dataManager: DataManagerProtocol) {
        self.dataManager = dataManager
    }

    func loadData() {
        dataManager.fetchData()
    }
}

  ```
 Now, the ViewModel is only responsible for managing its own logic, and the responsibility for creating the DataManager is moved outside the class.
 
 5. **Inversion of Control (IoC))**  
    **Problem:**
   Without Dependency Injection, a class controls the instantiation of its own dependencies. This leads to tight coupling and makes it harder to manage how dependencies are created or replaced.
    **Solution with DI: **   In Dependency Injection, the control over object creation is inverted. An external component (often a dependency injector or container) is responsible for creating and injecting dependencies, making the system more flexible.
    **Example: **
   ```swift
// With Dependency Injection: Inversion of Control
class ViewModel {
    var dataManager: DataManagerProtocol

    // Constructor Injection
    init(dataManager: DataManagerProtocol) {
        self.dataManager = dataManager
    }

    func loadData() {
        dataManager.fetchData()
    }
}

   ```
The ViewModel is in control of creating its own dependencies, leading to tight coupling.

With DI:
  ```swift
// With Dependency Injection: Adheres to SRP
class ViewModel {
    var dataManager: DataManagerProtocol

    // Constructor Injection
    init(dataManager: DataManagerProtocol) {
        self.dataManager = dataManager
    }

    func loadData() {
        dataManager.fetchData()
    }
}

  ```
Now, the ViewModel no longer controls the creation of DataManager. This responsibility is moved to an external source, allowing more flexibility in how dependencies are created.
 
