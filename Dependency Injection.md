# What is Dependency Injection (DI)?

 DI provides a way for a class to get its required objects (dependencies) from the outside, rather than directly instantiating them.



## 📚 Why Are We Using Dependency Injection?

1. **Loose Coupling**  
    **Problem:**
    Without Dependency Injection, classes are tightly coupled to their dependencies. This means that classes create and manage their own dependencies, which makes the code difficult to maintain and extend. If you need to change or swap a dependency, you’ll need to modify the class directly.
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

3. **O: Open/Closed Principle**  
   Classes should be open for extension but closed for modification.

4. **L: Liskov Substitution Principle**  
   Subtypes must be substitutable for their base types.

5. **I: Interface Segregation Principle**  
   Classes should not be forced to implement interfaces they do not use.

6. **D: Dependency Inversion Principle**  
   High-level modules should depend on abstractions, not concrete implementations.
