# [WIP] Generic Programming

###### Place holder:

`T` is the place holder. It will be contained in `<>`.

```swift
func p<T>(value: T) {
    // ...
}
```

We can even add constraint after the place holder. Within the place holder, only allows the use of `:` and `&` to constraint the generic type. You can also use `where` which you can use `==` or `:` to constraint to types. using `==` to denote the associated type from a type must equal the associated type from the other type.

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
Place holder can only constrain to one class, even if the other protocol has no class requirement.
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
func hasSameValue<C1, C2, V>(_ c1: C1, _ c2: C2, value: V) -> Bool 
    where 
        C1: Collection, C2: Collection, 
        C1.Element == C2.Element, 
        V == C1.Element, 
        C1.Element: Equatable {
    return c1.contains(value) && c2.contains(value)
}
// The `Element` is Int. So, different `Collection`, as long as the `Element` are the same. it can pass the Collection in the function. But the `Element` also needs to be `Equatable`
let s: Set<Int> = Set([0, 2, 7, 12])
let a1: [Int] = [1, 2, 10, 12]
let a2: [Int] = [1, 3, 5, 12]   
let b1 = hasSameValue(a1, a2, value: 2) // ✅, return false
let b2 = hasSameValue(s, a1, value: 2) // ✅, return true
```
But with variadic generic(type pack) feature with `==`, You can even write like below. But `==` for variadic generic hasn't enabled in swift 6.3. The feature name is "SameElementRequirements":
```swift
func hasSameValue<each C, V>(_ collections: repeat (each C), value: V) -> Bool 
    where 
        repeat each C: Collection, 
        repeat (each C).Element: Equatable, 
        repeat (each C).Element == V { // `==` for variadic generic hasn't enabled in swift 6.3
    for collection in repeat each collections {
        if !collection.contains(value) {
            return false
        }
    }
    return true
}

// so you can:
let b = hasSameValue(s, a1, a2, value: 12) // ✅, return b = true
```
You can also use `any` and `some` as generic type. 
* `any` is a real type, `Existential type`, it will have an indirect call.
* `some`(Opaque/Opaque Parameter Type), `some` is actually syntactic sugar for a generic type.

```swift
func g1<T: CustomStringConvertible>(_ x: T) -> String {
    return String(describing: x)
}

func g2(_ x: some CustomStringConvertible) -> String {
    return String(describing: x)
}

func g3(_ x: any CustomStringConvertible) -> String {
    return String(describing: x)
}
// so you can:
let str = "hello"
let aNum = 23
let p: any CustomStringConvertible = 10.2
// all ✅
g1(str); g1(aNum); g1(p); g2(str); g2(aNum); g2(p); g3(str); g3(aNum); g3(p)
```