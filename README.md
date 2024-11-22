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
