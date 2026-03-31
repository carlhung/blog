# [WIP] Generic Programming

###### Place holder:

`T` is the place holder. It will be contained in `<>`.

```swift
func p<T>(value: T) {
    // ...
}
```

We can even add constraint after the place holder. Within the place holder, only allows to use `:` and `&` to constraint the generic type. You can also use `where` which you can use `==` or `:` to constraint to types. using `==` to denote the associated type from a type must equal the associated type from the other type.

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
```
Pplace holder can only constrain to one class, even if the other protocol has no class requirement.
```swift
class C1 {}
class C2 {}

// ❌, place holder can only constrain to one class, even if the other protocol has no class requirement
func generic<T: C2 & C1> (_ value: T) {
`- error: protocol-constrained type cannot contain class 'C1' because it already contains class 'C2'
}

// ✅
func generic<T: C2 & CustomStringConvertible> (_ value: T) {
// ...
}
```
Using `==` to constraint the same type.
```swift
func hasSameValue<C1, C2, V>(_ c1: C1, _ c2: C2, value: V) -> Bool where C1: Collection, C2: Collection, C1.Element == C2.Element, V == C1.Element,C1.Element: Equatable {
    return c1.contains(value) && c2.contains(value)
}
// The `Element` is Int. So, different `Collection`, as long as the `Element` are the same. it can pass the Collection in the function. But the `Element` also needs to be `Equatable`
let s: Set<Int> = Set([0, 2, 7])
let a1: [Int] = [1, 2, 10]
let a2: [Int] = [1, 3, 5]   
let b1 = hasSameValue(a1, a2, value: 2) // ✅, return false
let b2 = hasSameValue(s, a1, value: 2) // ✅, return true
```