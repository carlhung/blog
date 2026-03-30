# Optional Chaining Trick

Prepare some code, it isn't important. Just copy and paste:

```swift
@propertyWrapper
enum Indirect<T> {
    indirect case next(T)
    init(wrappedValue: T) {
        self = .next(wrappedValue)
    }
    var wrappedValue: T {
        switch self {
        case .next(let value): return value
        }
    }
}

struct Person {
    var name: String
    var age: Int
    @Indirect var contactPerson: Person?
}

class Model {
    let p: Person = Person(name: "Mark", age: 30, contactPerson: Person(name: "mary", age: 20, contactPerson: nil))
}
```



When trying to unwrap it like that:

```swift
let model: Model? = Model()
if let m = model, let p = m.p {
                    `- error: initializer for conditional binding must have Optional type, not 'Person'
}
```



what you can do is:

```swift
let model: Model? = Model()
if let m = model {
    let p = m.p
    if let name = p.contactPerson?.name {
        xxx
    }
}
```



There is a trick to make it chain together:

```swift
let model: Model? = Model()
if let m = model, let p = .some(m.p), let name = p.contactPerson?.name {
    xxx
}
```
