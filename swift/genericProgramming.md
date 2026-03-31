# [WIP] Generic Programming

###### Place holder:

`T` is the place holder. It will be contained in `<>`.

```swift
func p<T>(value: T) {
    // ...
}
```

We can even add constraint after the place holder.

```swift
func basicGeneric<T: CustomStringConvertible & Hashable>(_ value: T) {
    print("Value: \(value)")
}

// equal to `basicGeneric`
func basicGeneric1<T>(_ value: T) where T: CustomStringConvertible & Hashable {
    print("Value: \(value)")
}

// equal to `basicGeneric` and `basicGeneric1`
func basicGeneric2<T>(_ value: T) where T: CustomStringConvertible, T: Hashable {
    print("Value: \(value)")
}

func basicGeneric3<C, D>(_ value1: C, _ value2: D) where C: Collection, C.Element: CustomStringConvertible, D: Collection, D.Element == C.Element {
    print("Value1: \(value1), value2: \(value2)")
}

// equal to `basicGeneric3`
func basicGeneric4<C: Collection, D: Collection>(_ value1: C, _ value2: D) where C.Element: CustomStringConvertible, D.Element == C.Element {
    print("Value1: \(value1), value2: \(value2)")
}

class C1 {}
class C2 {}

// place holder can only constrain to one class, even if the other protocol has no class requirement
func generic<T: C2 & C1> (_ value: T) {
                     `- error: protocol-constrained type cannot contain class 'C1' because it already contains class 'C2'
}

// ok
func generic<T: C2 & CustomStringConvertible> (_ value: T) {
    // ...
}
```