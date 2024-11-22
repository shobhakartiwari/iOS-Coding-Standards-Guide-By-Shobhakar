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
