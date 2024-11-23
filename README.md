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
