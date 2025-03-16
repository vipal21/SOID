# Property Wrapper
A Property Wrapper is a feature that enables you to add custom behavior to properties.It allows you to define reusable logic for property access and modification, making your code cleaner and more maintainable."

## Example

```swift
@propertyWrapper
struct UserDefaultsBacked<T> {
    private let key: String // The key used to store/retrieve the value in UserDefaults
    private let defaultValue: T // A fallback value if the key doesn’t exist

    var wrappedValue: T {
        get {
            // Retrieves the stored value from UserDefaults.
            // If it doesn't exist, return the default value.
            UserDefaults.standard.object(forKey: key) as? T ?? defaultValue
        }
        set {
            // Saves the new value to UserDefaults.
            UserDefaults.standard.set(newValue, forKey: key)
        }
    }

    // Initializer assigns the key and default value
    init(wrappedValue: T, _ key: String) {
        self.key = key
        self.defaultValue = wrappedValue
    }
}
struct Settings {
    // Uses @UserDefaultsBacked to persist dark mode preference
    @UserDefaultsBacked("darkMode") var isDarkMode: Bool = false

    // Uses @UserDefaultsBacked to save username
    @UserDefaultsBacked("username") var username: String = "Guest"
}

var settings = Settings() 

// Change values
settings.isDarkMode = true // This will automatically store `true` in UserDefaults
settings.username = "JohnDoe" // This saves "JohnDoe" in UserDefaults

// Fetch stored values directly from UserDefaults
print(UserDefaults.standard.bool(forKey: "darkMode"))  // Output: true
print(UserDefaults.standard.string(forKey: "username") ?? "No username")  // Output: "JohnDoe"


```

**  Explanation**

## Property Wrapper Purpose
 Customize property behavior—@UserDefaultsBacked changes how values are stored and retrieved without altering the way the property is accessed or modified in your code.
 @UserDefaultsBacked Behavior: It encapsulates UserDefaults logic, adding getter and setter logic that ensures values are persisted across app sessions automatically.

## Example 2 

```swift
@propertyWrapper
struct Trimmed {
    private var value: String = ""

    var wrappedValue: String {
        get { value } // Returns the stored string
        set { value = newValue.trimmingCharacters(in: .whitespacesAndNewlines) } 
        // Trims spaces when a new value is assigned
    }

    init(wrappedValue: String) {
        self.wrappedValue = wrappedValue // Uses setter to ensure trimming happens at initialization
    }
}
struct User {
    @Trimmed var username: String = "  John Doe  " // Automatically removes spaces

    func showUsername() {
        print("Username: '\(username)'") // Outputs: 'John Doe' (without spaces)
    }
}

let user = User()
user.showUsername()

```

**  Explanation**
This property wrapper modifies a property’s behavior by automatically trimming leading and trailing spaces.
@Trimmed ensures consistent formatting by removing unnecessary spaces automatically.

## Example 3 

```swift
@propertyWrapper
struct UpperCased {
    private var value: String = ""

    var wrappedValue: String {
        get { value }
        set { value = newValue.uppercased() } // Convert to uppercase when set
    }

    init(wrappedValue: String) {
        self.wrappedValue = wrappedValue // Apply uppercase in the initializer
    }
}

// Usage
struct Person {
    @UpperCased var name: String
    
    init(name: String) {
        self.name = name
    }
}

let person = Person(name: "john doe")
print(person.name) // Output: "JOHN DOE"


```

**  Explanation**
Whenever we set a string, it is automatically converted to uppercase.

