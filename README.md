# SOLID Principles in Swift - Example Project

This repository demonstrates the **SOLID** principles using a simple example in Swift. Each principle is explained and applied in the context of an iOS project. 



## 📚 SOLID Principles Overview

1. **S: Single Responsibility Principle**  
   Each class or module should have only one responsibility.

2. **O: Open/Closed Principle**  
   Classes should be open for extension but closed for modification.

3. **L: Liskov Substitution Principle**  
   Subtypes must be substitutable for their base types.

4. **I: Interface Segregation Principle**  
   Classes should not be forced to implement interfaces they do not use.

5. **D: Dependency Inversion Principle**  
   High-level modules should depend on abstractions, not concrete implementations.

This document demonstrates the **Single Responsibility Principle (SRP)** and the **Open/Closed Principle (OCP)** with practical examples in Swift.


## 🧩 **S: Single Responsibility Principle (SRP)**

**Definition:**  
Every class should have a single responsibility or reason to change.

### **Example**  
In this example, we separate concerns into three distinct classes to adhere to SRP:

1. **WebServiceManager**: Handles API calls.  
2. **DataManager**: Manages local database interactions.  
3. **DataFormatter**: Formats raw data for the UI.

By separating these responsibilities, changes in one functionality (e.g., modifying the API format) won't impact the others.

### **Implementation**

```swift
// Handles API calls
class WebServiceManager {
    func fetchDataFromAPI() -> String {
        return "Raw API Data"
    }
}

// Manages local database interactions
class DataManager {
    func fetchLocalData() -> String {
        return "Local Data"
    }
}

// Formats raw data for the UI
class DataFormatter {
    func formatData(rawData: String) -> String {
        return "Formatted Data: \(rawData)"
    }
}
let webService = WebServiceManager()
let dataManager = DataManager()
let dataFormatter = DataFormatter()

let apiData = webService.fetchDataFromAPI()
let localData = dataManager.fetchLocalData()

let formattedAPIData = dataFormatter.formatData(rawData: apiData)
let formattedLocalData = dataFormatter.formatData(rawData: localData)

print(formattedAPIData) // Output: Formatted Data: Raw API Data
print(formattedLocalData) // Output: Formatted Data: Local Data
```
## 🚀 Open/Closed Principle (OCP)

**Definition:**  
Classes should be open for extension but closed for modification.


### **Example**

To follow the Open/Closed Principle (OCP), we define a `BaseViewModel` with reusable functionality. Derived classes extend this functionality without altering the base class.

- **`BaseViewModel`**: Provides shared logic.  
- **`UserViewModel`**: Extends `BaseViewModel` to add specific behavior.


### **Implementation**

```swift
// Base ViewModel with shared logic
class BaseViewModel {
    func fetchData() {
        print("Fetching data from base logic")
    }
}

// Derived ViewModel for user-specific functionality
class UserViewModel: BaseViewModel {
    func fetchUserDetails() {
        print("Fetching user-specific details")
    }
}
```
## 🧩 Liskov Substitution Principle (LSP)

**Definition:**  
Subtypes must be substitutable for their base types without altering the correctness of the program.


### **Example**

To adhere to the Liskov Substitution Principle, we define a protocol that specifies a contract. Both concrete implementations (`DataManager` and `MockDataProvider`) conform to the protocol and can be used interchangeably.


### **Protocol Definition**

```swift
protocol DataProvider {
    func fetchData() -> [String]
}
// Real data provider
class DataManager: DataProvider {
    func fetchData() -> [String] {
        return ["Real Data"]
    }
}

// Mock data provider for testing
class MockDataProvider: DataProvider {
    func fetchData() -> [String] {
        return ["Mock Data"]
    }
}
func displayData(dataProvider: DataProvider) {
    print(dataProvider.fetchData())
}

// Instantiate real and mock data providers
let realManager = DataManager()
let mockManager = MockDataProvider()

// Both can be used interchangeably
displayData(dataProvider: realManager) // Output: ["Real Data"]
displayData(dataProvider: mockManager) // Output: ["Mock Data"]

```
### **Why It Works**

- The `DataManager` and `MockDataProvider` conform to the `DataProvider` protocol.  
- Any function expecting a `DataProvider` works correctly with both implementations.  
- This substitution ensures flexibility, making the code robust and testable.  

By following the **Liskov Substitution Principle**, this approach ensures:  
- **Correctness**: Subtypes replace base types without altering the program's behavior.  
- **Integrity**: The code remains maintainable and adheres to expected contracts.  

## 🔧 Interface Segregation Principle (ISP)

**Definition:**  
Classes should not be forced to implement interfaces they do not use.

### **Problem**

Having a single protocol with many responsibilities can lead to unnecessary dependencies. For example:

```swift
protocol ComprehensiveDataProvider {
    func fetchRemoteData()
    func saveToDatabase()
    func formatData()
}
```
### **Problem**

If a class only needs one of these responsibilities, it is forced to implement methods it doesn't use, violating the **Interface Segregation Principle**.


### **Solution**

Break the protocol into smaller, more focused protocols. Each protocol represents a specific responsibility, ensuring that classes implement only the methods they actually need.

```swift
protocol RemoteDataFetcher {
    func fetchRemoteData()
}

protocol DatabaseSaver {
    func saveToDatabase()
}

protocol DataFormatter {
    func formatData()
}

```
### **Example Implementation**

Classes can now implement only the protocols they need:.

```swift
// Class responsible for fetching remote data
class WebServiceManager: RemoteDataFetcher {
    func fetchRemoteData() {
        print("Fetching remote data...")
    }
}

// Class responsible for saving data to a database
class DatabaseManager: DatabaseSaver {
    func saveToDatabase() {
        print("Saving data to the database...")
    }
}

// Class responsible for formatting data
class DataFormatterManager: DataFormatter {
    func formatData() {
        print("Formatting data...")
    }
}

```
### **Example Implementation**

```swift
let webService = WebServiceManager()
webService.fetchRemoteData() // Output: Fetching remote data...

let databaseManager = DatabaseManager()
databaseManager.saveToDatabase() // Output: Saving data to the database...

let formatter = DataFormatterManager()
formatter.formatData() // Output: Formatting data...
```

### **Why It Works**

- Each class implements only the methods relevant to its functionality.  
- Reduces unnecessary dependencies, improving maintainability and clarity.  
- Adheres to the Interface Segregation Principle, ensuring focused and efficient interfaces.


## 🔧  Dependency Inversion Principle (DIP)

**Definition:**  
High-level modules should not depend on low-level modules. Both should depend on abstractions.

### **Example**

- The ViewModel depends on protocols (DataManagerProtocol, WebServiceProtocol) instead of concrete implementations.  

```swift
//Define protocols:

protocol DataManagerProtocol {
    func fetchData() -> [String]
}

protocol WebServiceProtocol {
    func requestData() -> [String]
}

//Implement the protocols:

class DataManager: DataManagerProtocol {
    func fetchData() -> [String] {
        return ["Data from Database"]
    }
}

class WebServiceManager: WebServiceProtocol {
    func requestData() -> [String] {
        return ["Data from API"]
    }
}

//Inject dependencies:

class ViewModel {
    private let dataManager: DataManagerProtocol
    private let webService: WebServiceProtocol

    init(dataManager: DataManagerProtocol, webService: WebServiceProtocol) {
        self.dataManager = dataManager
        self.webService = webService
    }

    func loadData() {
        let localData = dataManager.fetchData()
        let remoteData = webService.requestData()
        print("Local: \(localData), Remote: \(remoteData)")
    }
}


```
This approach ensures loose coupling and flexibility.

##  Single cohesive example demonstrating the SOLID principles

```swift
import Foundation

// MARK: - Protocol Definitions (Liskov Substitution Principle & Dependency Inversion Principle)

// Abstraction for data fetching
protocol DataManagerProtocol {
    func fetchData() -> [String]
}

// Abstraction for web service interactions
protocol WebServiceProtocol {
    func requestData() -> [String]
}

// MARK: - Concrete Implementations

// Handles local data storage (Single Responsibility Principle)
class DataManager: DataManagerProtocol {
    func fetchData() -> [String] {
        return ["Local Data Item 1", "Local Data Item 2"]
    }
}

// Handles API calls (Single Responsibility Principle)
class WebServiceManager: WebServiceProtocol {
    func requestData() -> [String] {
        return ["Remote Data Item A", "Remote Data Item B"]
    }
}

// MARK: - Base ViewModel (Open/Closed Principle)

// A base ViewModel providing common functionality
class BaseViewModel {
    func logData(_ data: [String]) {
        print("Logging data: \(data)")
    }
}

// MARK: - Derived ViewModel

// A specific ViewModel to handle data display logic
class DataViewModel: BaseViewModel {
    private let dataManager: DataManagerProtocol
    private let webService: WebServiceProtocol

    // Injecting dependencies via initializer (Dependency Inversion Principle)
    init(dataManager: DataManagerProtocol, webService: WebServiceProtocol) {
        self.dataManager = dataManager
        self.webService = webService
    }

    // Fetches data from both local and remote sources
    func fetchAndDisplayData() {
        let localData = dataManager.fetchData() // Using DataManagerProtocol abstraction
        let remoteData = webService.requestData() // Using WebServiceProtocol abstraction
        
        // Combining and displaying data
        let combinedData = localData + remoteData
        print("Combined Data: \(combinedData)")

        // Using base class functionality
        logData(combinedData)
    }
}

// MARK: - Example Usage

// Create concrete implementations for the dependencies
let dataManager = DataManager()
let webServiceManager = WebServiceManager()

// Inject dependencies into the ViewModel
let viewModel = DataViewModel(dataManager: dataManager, webService: webServiceManager)

// Fetch and display data
viewModel.fetchAndDisplayData()

```
This section demonstrates how the **SOLID** principles can be applied in Swift through a simple example.


### **Single Responsibility Principle (SRP)**

- **DataManager** is responsible for managing local data.
- **WebServiceManager** is responsible for managing API calls.

Each class has a single responsibility, ensuring that changes in one area (e.g., changing the way API calls are handled) don’t affect other areas (e.g., managing local data).


### **Open/Closed Principle (OCP)**

- The **BaseViewModel** provides reusable functionality (e.g., logging).
- The **DataViewModel** extends this functionality without modifying the **BaseViewModel**.

By extending `BaseViewModel`, new functionality can be added without altering its original structure, adhering to the **Open/Closed Principle**.


### **Liskov Substitution Principle (LSP)**

- Both **DataManager** and **WebServiceManager** conform to their respective protocols.
- The **ViewModel** depends on abstractions (`DataManagerProtocol` and `WebServiceProtocol`), allowing any class that implements these protocols to be used interchangeably.

This ensures that subclasses or alternative implementations can replace their parent types without affecting the behavior of the program.


### **Interface Segregation Principle (ISP)**

- Protocols **DataManagerProtocol** and **WebServiceProtocol** are small and focused, each defining only the specific methods needed.
- This ensures that classes only implement the methods they actually need, avoiding unnecessary dependencies.

By splitting large protocols into smaller, more specific ones, we ensure cleaner, more maintainable code.


### **Dependency Inversion Principle (DIP)**

- The high-level **ViewModel** depends on abstractions (protocols), not concrete implementations.
- This decouples the **ViewModel** from specific classes like `DataManager` and `WebServiceManager`, ensuring flexibility and easier testing.

By depending on abstractions rather than concrete classes, we create more modular and flexible code.


This approach demonstrates how the **SOLID** principles promote clean, maintainable, and flexible code.


