# iOS-Coding-Standards-Guide-By-Shobhakar
This repository aims to establish and promote standardized iOS coding practices for developers. It serves as a reference guide to write clean, maintainable, and scalable Swift code for iOS applications.

### 1. Extension that allows tasks to leverage AnyCancellable, enabling them to be automatically cancelled when their caller is deinitialized.
```swift
import Combine
extension Task {
    func eraseToAnyCancellable() -> AnyCancellable {
        AnyCancellable(cancel)
    }
    
    func store(in set: inout Set<AnyCancellable>) {
        set.insert(AnyCancellable(cancel))
    }
}
```
### 2. Write documention for each Parameter, Throws, and Returns.
```swift
/*Repeats a string `times` times.

- Parameter str:   The string to repeat.
- Parameter times: The number of times to repeat `str`.

- Throws: `MyError.InvalidTimes` if the `times` parameter 
    is less than zero.

- Returns: A new string with `str` repeated `times` times.
*/
func repeatString(str: String, times: Int) throws -> String {
    guard times >= 0 else { throw MyError.InvalidTimes }
    return Repeat(count: 5, repeatedValue: "Hello").joinWithSeparator("")
}
```
### 3. Structure of a Class file:
- Using MARK: comment is a great way to group your methods, especially in view controllers.
```swift

import SomeExternalFramework

//MARK: Protocols declarations
//Protocol name should be end purpose of protocol
//ex: FooDelegate , FooDataSource
protocol ProtocolNameDelegate {
    func foo(param1: String, param2: Bool)
}

/**
Documentation for the class/file
*/

class MyViewcontroller : UIViewController{

    //MARK: Delegate initialization
    var delegate: ProtocolNameDelegate?
    
    //MARK: Outlets
    @IBOutlet weak var tableView: UITableView!
    @IBOutlet weak var btnSubmit: UIButton!
    
    // Custom initializers go here
    private let fooStringConstant = "FooConstant"
    private let floatConstant = 1234.5

    // MARK: View Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()
        // ...
    }
    
     override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        // ...
    }

    // MARK: User Interaction - Actions & Targets
    
    //Add OnClick as prefix along with same outlet name for button actions
    @IBAction func btnSubmitOnClick(_ sender: UIButton) {
        // ...
    }
    
    func foobarButtonTapped() {
        // ...
    }
    
    // MARK: Additional Helpers
    private func displayNameForFoo(foo: Foo) {
        // ...
    }
}

// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // Table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // Scroll view delegate methods
}

//MARK: Extension - Name of extension class
/**
 - Documentation for purpose of extension
*/
extension SomeOtherClass: UIViewController {
    func foobar(foobar: Foobar, somethingWithFoo foo: Foo) {
        // ...
    }
}
```

### 4. Error Handling:
- **Forced-try Expression**
    - **Avoid using the forced-try expression**: `try!`
        **Incorrect**
        ```swift
        // This will crash at runtime if there is an error parsing the JSON data!
        let json = try! JSONSerialization.jsonObject(with: data, options: .allowFragments)
        print(json)
        ```
        **Correct**
        ```swift
        do {
            let json = try JSONSerialization.jsonObject(with: data, options: .allowFragments)
            print(json)
            } catch {
                print(error)
            }
        ```
    - **Let vs. Var**
        - Whenever possible use let instead of var.
        - Declare properties of an **object** or **struct** that shouldn't change over its lifetime with `let`.
        
    - **Access Control**
        - Prefer `private` properties and methods whenever possible to encapsulate and limit access to internal object state.
        - For `private` declarations at the top level of a file that are outside of a type, explicitly specify the declaration as `fileprivate`. This is functionally the same as marking these declarations private, but clarifies the scope:
        
            **Incorrect**
            ```swift
            import Foundation

            // Top level declaration
            private let foo = "bar"

            struct Baz {
            ...
            ```
            **Correct**
            ```swift
            import Foundation

            // Top level declaration
            fileprivate let foo = "bar"

            struct Baz {
            ...
            ```
        - If you need to expose functionality to other modules, prefer `public` classes and class members whenever possible to ensure functionality is not accidentally overridden. Better to expose the class to `open` for subclassing when needed.

## 5. Spacing
- Open curly braces on the same line as the statement and close on a new line.
- Put `else` statements on the same line as the closing brace of the previous `if` block.
- Make all colons left-hugging (no space before but a space after) except when used with the ternary operator (a space both before and after).
        
    **Incorrect**
    ```swift
    class SomeClass : SomeSuperClass
    {
        private let someString:String

        func someFunction(someParam :Int)
        {
            let dictionaryLiteral : [String : AnyObject] = ["foo" : "bar"]

            let ternary = (someParam > 10) ? "foo": "bar"

            if someParam > 10 { ... }

            else {
                    ...
            } } }
    ```
    **Correct**
    ```swift
    class SomeClass: SomeSuperClass {
        private let someString: String
        func someFunction(someParam: Int) {
            let dictionaryLiteral: [String: AnyObject] = ["foo": "bar"]

            let ternary = (someParam > 10) ? "foo" : "bar"

            if someParam > 10 {
                ...
            } else {
                ...
            }
        }
    }
    ```

    ## 6. Protocols
- **Protocol Conformance**
    - When adding protocol conformance to a type, use a separate extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a type with its associated methods.
    - Use a `// MARK: - SomeDelegate` comment to keep things well organized.
        
        **Incorrect**
        ```swift
        class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
            // All methods
        }
        ```
        **Correct**
        ```swift
        class MyViewcontroller: UIViewController {
            ...
        }

        // MARK: - UITableViewDataSource
        extension MyViewcontroller: UITableViewDataSource {
            // Table view data source methods
        }

        // MARK: - UIScrollViewDelegate
        extension MyViewcontroller: UIScrollViewDelegate {
            // Scroll view delegate methods
        }
        ```
            
- **Delegate Protocols**
    - If your protocol should have **optional methods**, it must be declared with the `@objc` attribute.
    - Declare protocol definitions near the class that uses the delegate, not the class that implements the delegate methods.
    - If more than one class uses the same protocol, declare it in its own file.
    - Use `weak` optional `var`s for delegate variables to avoid retain cycles.
    
        ```swift
        //SomeTableCell.swift
        protocol SomeTableCellDelegate: class {
            func cellButtonWasTapped(cell: SomeTableCell)
        }

        class SomeTableCell: UITableViewCell {
            weak var delegate: SomeTableCellDelegate?
            // ...
        }
        ```
        ```swift
        //SomeTableViewController.swift
        class SomeTableViewController: UITableViewController {
            // ...
        }

        // MARK: - SomeTableCellDelegate
        extension SomeTableViewController: SomeTableCellDelegate {
            func cellButtonWasTapped(cell: SomeTableCell) {
                // Implementation of cellbuttonwasTapped method
            }
        }
        ```
## 7. Arrays and Dictionaries
- **Type Shorthand Syntax**
    Use square bracket shorthand type syntax for Array and Dictionary as recommended by Apple in [Array Type Shorthand Syntax](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID107):
        
    **Incorrect**
    ```swift
    let users: Array<String>
    let usersByName: Dictionary<String, User>
    ```
    **Correct**
    ```swift
    let users: [String]
    let usersByName: [String: User]
    ```
- **Trailing Comma**
    For array and dictionary literals, unless the literal is very short, split it into multiple lines, with the opening symbols on their own line, each item or key-value pair on its own line, and the closing symbol on its own line. Put a trailing comma after the last item or key-value pair to facilitate future insertion/editing. Xcode will handle alignment sanely.
    
    **Incorrect**
    ```swift
    let anArray = [
        object1,
        object2,
        object3 //no trailing comma
    ]

    let aDictionary = ["key1": value1, "key2": value2] //how can you even read that?!
    ```
    **Correct**
    ```swift
    let anArray = [
        object1,
        object2,
        object3,
    ]

    let aDictionary = [
        "key1": value1,
        "key2": value2,
    ]
    ```

## 8. Typealiases
- Create `typealiases` to give semantic meaning to commonly used datatypes and closures.
    
    ```swift
    typealias IndexRange = Range<Int>
    typealias JSONObject = [String: AnyObject]
    typealias APICompletion = (jsonResult: [JSONObject]?, error: NSError?) -> Void
    typealias BasicBlock = () -> Void
    ```

## 9. Switch Statements
- Use multiple values on a single `case` where it is appropriate:
    
    ```swift
    var someCharacter: Character
    ...
        
    switch someCharacter {
    case "a", "e", "i", "o", "u":
        print("\(someCharacter) is a vowel")
    ...
    }
    ```
## 10. Loops
- Use the `enumerated()` function if you need to loop over a Sequence and use the index:
    
    ```swift
    for (index, element) in someArray.enumerated() {
        ...
    }
    ```
    - Use `map` when transforming Arrays (`flatMap` for Arrays of Optionals or Arrays of Arrays):
    ```swift
    let array = [1, 2, 3, 4, 5]
    let stringArray = array.map { item in
        return "item \(item)"
    }

    let optionalArray: [Int?] = [1, nil, 3, 4, nil]
    let nonOptionalArray = optionalArray.flatMap { item -> Int? in
        guard let item = item else {
            return nil
        }

        return item * 2
    }

    let arrayOfArrays = [array, nonOptionalArray]
    let anotherStringArray = arrayOfArrays.flatmap { item in
        return "thing \(item)"
    }
    ```
- If you have an Array of Arrays and want to loop over all contents, consider a `for in` loop using `joined(separator:)` instead of nested loops:
    ```swift
    let arraysOfNames = [["Moe", "Larry", "Curly"], ["Groucho", "Chico", "Harpo", "Zeppo"]]
    ```
    **Recommended**
    ```swift
    for name in arraysOfNames.joined() {
        print("\(name) is an old-timey comedian")
    }
    ```
    **Discouraged**
    ```swift
    for names in arraysOfNames {
        for name in names {
            print("\(name) is an old-timey comedian")
        }
    }
    ```
## 11. Closures
- **Trailing Closure Syntax**
    - Use trailing closure syntax when the only or last argument to a function or method is a closure.
        
        ```swift
        //a function that has a completion closure/block
        func registerUser(user: User, completion: (Result) -> Void)
        ```
        **Incorrect**
        ```swift
        UserAPI.registerUser(user, completion: { result in
            if result.success {
                ...
            }
        })
        ```
        **Correct**
        ```swift
        UserAPI.registerUser(user) { result in
            if result.success {
                ...
            }
        }
        ```
    - Omit the empty parens () when the only argument is a closure.
        
        **Incorrect**
        ```swift
        let doubled = [2, 3, 4].map() { $0 * 2 }
        ```
        **Correct**
        ```swift
        let doubled = [2, 3, 4].map { $0 * 2 }
        ```
## 12. Constants
- Prefer declaring constants outside the scope of a class to give them static storage.
- When creating a shared constants file (ex. Constants.swift), use `struct`s to group related constants together. The name of the `struct` should be singular, and each field should be written using camelCase.
- Be wary of large constants files as they can become unmanageable over time. Refactor related parts of the main constants file into separate files for that situation.
    
    ```swift
    struct SegueIdentifier {
        static let onboarding = "OnboardingSegue"
        static let login = "LoginSegue"
        static let logout = "LogoutSegue"
    }

    struct StoryboardIdentifier {
        static let main = "Main"
        static let onboarding = "Onboarding"
        static let settings = "Settings"
    }

    print(SegueIdentifier.login) // "LoginSegue"
    ```
- Where appropriate, constants can also be grouped using an `enum` with a `rawValue` type that is relevant to the type you need to work with
    
    ```swift
    enum UserJSONKeys: String {
        case username
        case email
        case role
        // Explicitly defined rawValue
        case identifier = "id"
        ...
    }

    print(UserJSONKeys.username.rawValue) // "username"
    print(UserJSONKeys.identifier.rawValue) // "id"

    guard let url = URL(string: "http://www.example.com") else {
        return
    }

    let mutableURLRequest = NSMutableURLRequest(url: url)
    mutableURLRequest.HTTPMethod = HTTPMethods.POST.rawValue
    print(mutableURLRequest.httpMethod) // "POST"
    ```
## 13. Implicit return

- In single-line closures, implicit getters, and other code blocks where the opening {, inner statement, and closing } are all on one line, omit return.

**Incorrect
```swift
let doubled = [2, 3, 4].map { return $0 * 2 }

var someProperty: Int { return 4 * someOtherProperty }

func helloWorld() -> String { return "Hello, world!" }
```
**Correct**
```swift
let doubled = [2, 3, 4].map { $0 * 2 }

var someProperty: Int { 4 * someOtherProperty }

func helloWorld() -> String { "Hello, world!" }
```
## 14. Closures

- Avoid unnecessary parentheses around closure parameters.

**Incorrect**
```swift
functionWithAClosure { (result) in
    ...
}
functionWithAClosure { (result) -> Int in
    ...
}
```

**Correct**
```swift 
functionWithAClosure { result in
    ...
}
functionWithAClosure { result -> Int in
    ...
}
functionWithAClosure { (result: String) in
    ...
}
```
