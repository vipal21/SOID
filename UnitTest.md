# Unit Testing Rulebook with Examples

This document provides a comprehensive guide to unit testing different types of methods in Swift. It outlines rules, test case examples, and explanations for ensuring robust and reliable code.

---

## **1. Initialization Testing**

### **Rules for Testing Initializers**
1. **Default Property Values**: Verify that all properties are initialized with the correct default values.
2. **Dependency Injection**: Ensure dependencies are injected correctly and the object behaves as expected.
3. **Edge Cases**: Test for edge cases, such as passing `nil` or invalid inputs.

### **Example**
```swift
class UnitTestingBootcampViewModel: ObservableObject {
    @Published var isPremium: Bool
    @Published var dataArray: [String] = []
    @Published var selectedItem: String? = nil
    let dataService: NewDataServiceProtocol

    init(isPremium: Bool, dataService: NewDataServiceProtocol = NewMockDataService(items: nil)) {
        self.isPremium = isPremium
        self.dataService = dataService
    }
}

// Tests
func testInit_ShouldSetPropertiesCorrectly() {
    // Given
    let mockService = NewMockDataService(items: ["Test1", "Test2"])

    // When
    let viewModel = UnitTestingBootcampViewModel(isPremium: true, dataService: mockService)

    // Then
    XCTAssertTrue(viewModel.isPremium)
    XCTAssertEqual(viewModel.dataArray, [])
    XCTAssertNil(viewModel.selectedItem)
}
```

---

## **2. Simple Method Testing**

### **Rules for Simple Methods**
1. **Correct Behavior**: Verify that the method produces the expected outcome for valid inputs.
2. **Edge Cases**: Test for invalid or edge-case inputs.
3. **State Changes**: Ensure the method updates properties or states as expected.
4. **No Side Effects**: Verify that unrelated properties remain unchanged.

### **Example**: `addItem(item:)`
```swift
func addItem(item: String) {
    guard !item.isEmpty else { return }
    self.dataArray.append(item)
}

// Tests
func testAddItem_ShouldAddNonEmptyItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)

    // When
    viewModel.addItem(item: "NewItem")

    // Then
    XCTAssertEqual(viewModel.dataArray, ["NewItem"])
}

func testAddItem_ShouldNotAddEmptyItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)

    // When
    viewModel.addItem(item: "")

    // Then
    XCTAssertEqual(viewModel.dataArray, [])
}
```

---

## **3. Selection Method Testing**

### **Rules for Selection Methods**
1. **Valid Selection**: Verify the method selects the correct item from the data source.
2. **Invalid Selection**: Ensure the method handles non-existent items correctly.
3. **Edge Cases**: Test with empty strings, special characters, or unexpected inputs.
4. **State Updates**: Confirm that the selected state is updated appropriately.

### **Example**: `selectItem(item:)`
```swift
func selectItem(item: String) {
    if let x = dataArray.first(where: { $0 == item }) {
        selectedItem = x
    } else {
        selectedItem = nil
    }
}

// Tests
func testSelectItem_ShouldSetSelectedItemForExistingItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)
    viewModel.dataArray = ["Item1", "Item2", "Item3"]

    // When
    viewModel.selectItem(item: "Item2")

    // Then
    XCTAssertEqual(viewModel.selectedItem, "Item2")
}

func testSelectItem_ShouldSetSelectedItemToNilForNonExistentItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)
    viewModel.dataArray = ["Item1", "Item2", "Item3"]

    // When
    viewModel.selectItem(item: "NonExistent")

    // Then
    XCTAssertNil(viewModel.selectedItem)
}
```

---

## **4. Error-Throwing Method Testing**

### **Rules for Error-Throwing Methods**
1. **Successful Execution**: Verify the method executes without errors for valid inputs.
2. **Error Conditions**: Test that the correct errors are thrown for invalid inputs.
3. **Edge Cases**: Test for edge-case inputs like empty strings or extreme values.

### **Example**: `saveItem(item:)`
```swift
enum DataError: Error {
    case noData
    case itemNotFound
}

func saveItem(item: String) throws {
    guard !item.isEmpty else {
        throw DataError.noData
    }

    if let x = dataArray.first(where: { $0 == item }) {
        print("Save item here!!! \(x)")
    } else {
        throw DataError.itemNotFound
    }
}

// Tests
func testSaveItem_ShouldNotThrowForExistingItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)
    viewModel.dataArray = ["Item1", "Item2"]

    // When & Then
    XCTAssertNoThrow(try viewModel.saveItem(item: "Item1"))
}

func testSaveItem_ShouldThrowNoDataForEmptyItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)

    // When & Then
    XCTAssertThrowsError(try viewModel.saveItem(item: "")) { error in
        XCTAssertEqual(error as? DataError, DataError.noData)
    }
}

func testSaveItem_ShouldThrowItemNotFoundForNonExistentItem() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)
    viewModel.dataArray = ["Item1", "Item2"]

    // When & Then
    XCTAssertThrowsError(try viewModel.saveItem(item: "NonExistent")) { error in
        XCTAssertEqual(error as? DataError, DataError.itemNotFound)
    }
}
```

---

## **5. Performance Testing**

### **Rules for Performance Testing**
1. **Large Data Handling**: Ensure methods perform efficiently with large datasets.
2. **Measure Execution Time**: Use `measure` blocks to confirm acceptable execution time.

### **Example**
```swift
func testSaveItem_Performance() {
    // Given
    let viewModel = UnitTestingBootcampViewModel(isPremium: true)
    viewModel.dataArray = (1...100000).map { "Item\($0)" }
    let targetItem = "Item99999"

    // When & Then
    measure {
        XCTAssertNoThrow(try viewModel.saveItem(item: targetItem))
    }
}
```

---

## **Conclusion**

This rulebook provides structured guidelines and examples for testing a variety of methods in Swift. By adhering to these rules, you can ensure your unit tests are comprehensive, maintainable, and reliable.

