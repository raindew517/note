# Foundamental

## Variable

`let` is for immutable variables

`var` is for mutable variables

```swift
let names = ["hello", "world"]
// names.append("what") // wrong!

// simple value will be copied
let a = "what"
var b = a
b = "the"

// array will be copied
let moreNames = [
  "Foo",
  "Bar"
]
var copyNames = moreNames
copyNames.append("Baz")
moreNames //  ["Foo", "Bar"]
copyNames //  ["Foo", "Bar", "Baz"]

// copy reference
let oldArray = NSMutableArray(
    array: ["foo", "bar"]
)
oldArray.add("baz")
var newArray = oldArray
newArray.add("qux")
oldArray // ["foo", "bar", "baz", "qux"]
newArray
```

## Operator

```swift
// 1. unary prefix
let foo = !true
// 2. unary postfix
let name = Optional("Vanda")
type(of: name) // Optional<String>.Type
let unaryPostFix = name!
type(of: unaryPostFix) // String.Type
```

## Function

```swift
func customMinus(
  _ lhs: Int,
  _ rhs: Int
) -> Int { lhs - rhs }
customMinus(20, 10) // can not add labels

func customMinus(
  lhs: Int,
  rhs: Int
) -> Int { lhs - rhs }
customMinus(lhs: 20, rhs: 10) // can not remove labels

// usually a value must be used at somewhere
// add this decorator its result can ignore this rule
@discardableResult
func customAdd(
  lhs: Int,
  rhs: Int
) -> Int { lhs + rhs }

// function inside another function
func doSomethingComplicated(
    with value: Int
) -> Int {
    func mainLogic(value: Int) -> Int {
        value + 2
    }
    return mainLogic(value: value + 3)
}
doSomethingComplicated(with: 30)

// default value
func getFullName(
    firstName: String = "Foo",
    lastName: String = "Bar"
) -> String {
    "\(firstName) \(lastName)"
}
```

## Closure

```swift
let add: (Int, Int) -> Int = {
    (lhs: Int, rhs: Int) -> Int in // "in" indicates the function start
      lhs + rhs
}
add(20, 30)

func customAdd(
    _ lhs: Int,
    _ rhs: Int,
    using handler: (Int, Int) -> Int
) -> Int {
    handler(lhs, rhs)
}
customAdd(40, 50, using: {
    (lhs: Int, rhs: Int) -> Int in // "in" indicates the function start
    lhs + rhs
})
// if the function argument is the last one of arguments, we can write it like below
customAdd(40, 50) {
    (lhs: Int, rhs: Int) -> Int in // "in" indicates the function start
    lhs + rhs
}

// these are ok, too. but it will cost more time for compiler to figure out the date type, so it would be better to indicate the data type directly
customAdd(40, 50) {
    (lhs, rhs) in // "in" indicates the function start
    lhs + rhs
}
customAdd(40, 50) {$0 + $1}


let ages = [30, 20, 19, 40]
ages.sorted(by: {(lhs: Int, rhs: Int)->Bool in lhs > rhs})
ages.sorted(by: >)
ages.sorted(by: <)


func add10To (_ v: Int) -> Int {
    return v + 10
}
func add20To (v: Int) -> Int {
    return v + 20
}
func doAddition(
    on value: Int,
    using function: (Int) -> Int // `funcion` is not keyword in swift
) -> Int {
    function(value)
}
doAddition(on: 20, using: add10To) // 30
doAddition(on: 30, using: add20To(v:)) // 50
```

## Structure

```swift
struct Person {
    let name: String
    let age: Int
}
let foo = Person(name: "Foo", age: 20)

struct CommodoreComputer {
    let name: String
    let manufacturer: String
    init(name: String) { // like constructor of class in js
        self.name = name
        self.manufacturer = "comodore"
    }
}
let c64 = CommodoreComputer(name: "my commodore")

struct Person2 {
    let firstName: String
    let lastName: String
    var fullName: String {
        "\(firstName) \(lastName)"
    }
}
let fooBar = Person2(firstName: "Foo", lastName: "Bar")
fooBar.firstName
fooBar.lastName
fooBar.fullName // "Foo Bar"


struct Bike {
    let manufacturer: String
    let currentSpeed: Int
    func copy(currentSpeed: Int) -> Bike {
        Bike(manufacturer: self.manufacturer, currentSpeed: currentSpeed)
    }
}
let bike1 = Bike(manufacturer: "HD", currentSpeed: 20)
// let bike2 = bike1
// bike2.currentSpeed = 30 // this is wrong.
let bike2 = bike1.copy(currentSpeed: 30)
bike1.currentSpeed // 20
bike2.currentSpeed // 30
```

## Enumeration

```swift
// -----1-----
enum Animals {
    case cat
    case dog
    case rabbit
}

let cat = Animals.cat
switch cat {
case .cat: "This is a cat";break
case .dog: "This is a dog";break
case .rabbit: "This is a rabbit";break
}
// switch must be exhaustive

// -----2-----
enum Shortcut {
    case fileOrFolder(path: URL, name: String)
    case wwwUrl(path: URL)
    case song(artist: String, songName: String)
}
let wwwApple = Shortcut.wwwUrl(path: URL(string: "https://apple.com")!)
switch wwwApple {
case let .fileOrFolder(path, name):
    path
    name
    break
case let .wwwUrl(p):
    p
    break
case let .song(artist, songName):
    artist
    songName
    break
}

if case let .wwwUrl(path) = wwwApple {
    path
}

let withoutYou = Shortcut.song(artist: "Symphony X", songName: "Without You")
if case let .song(_, songName) = withoutYou {
    songName
}


// -----3-----
enum Vehicle {
    case car(manufacturer: String, model: String)
    case bike(manufacturer: String, yearMade: Int)
    
    func getManufacturer() -> String {
        switch self {
        case let .car(manufacturer, _):
            return manufacturer
        case let .bike(manufacturer, _):
            return manufacturer
        }
    }
    var manufacturer: String {
        switch self {
        case let .car(manufacturer, _),
            let .bike(manufacturer, _):
            return manufacturer
        }
    }
}
let car = Vehicle.car(manufacturer: "Tesla", model: "X")
car.getManufacturer() // "Tesla"
car.manufacturer // "Tesla"

// -----4-----
enum FavoriteEmoji: String, CaseIterable {
    case blush = "😊"
    case rocket = "🚀"
    case fire = "🔥"
}
FavoriteEmoji.allCases // [blush, rocket, fire]
FavoriteEmoji.allCases.map(\.rawValue) // ["😊", "🚀", "🔥"]
if let blush = FavoriteEmoji(rawValue: "😊") { // true
    "Found the blush emoji"
    blush
} else {
    "This emoji doesn't exist"
}

// -----5-----
enum Height {
    case short, medium, long
    mutating func makeLong() {
        self = Height.long
    }
}
var myHeight = Height.medium // medium
myHeight.makeLong()
myHeight // long

// -----6----- just know this exist is enough
indirect enum IntOperation {
    case add(Int, Int)
    case subtract(Int, Int)
    case freeHand(IntOperation)
    func calculateResult(of operation: IntOperation? = nil) -> Int {
        switch operation ?? self {
        case let .add(lhs, rhs):
            return lhs + rhs
        case let .subtract(lhs, rhs):
            return lhs - rhs
        case let .freeHand(operation):
            return calculateResult(of: operation)
        }
    }
}
let freeHand = IntOperation.freeHand(.add(2, 4))
freeHand.calculateResult()
```

## Class

```swift
// -----1-----
class Person {
    var name: String
    var age: Int
    init (name: String, age: Int) {
        self.name = name
        self.age = age
    }
    func increaseAge () {
        self.age += 1
    }
}
let foo = Person(
    name: "foo",
    age: 20
)
foo.age // 20
foo.increaseAge()
foo.age // 21
let bar = foo // class instance share memory
bar.increaseAge()
bar.age // 22
foo.age // 22

// -----2-----
class Person2 {
    private(set) var age: Int
    init(age: Int) {
        self.age = age
    }
    func increaseAge() {
        self.age += 1
    }
}
let baz = Person2(age: 20)
baz.age // 20
//baz.age += 1 // can not set age outside
baz.increaseAge()
baz.age // 21

let fooBar = Person2(age: 20)
func doSomething(with person: Person2) {
    person.increaseAge()
}
doSomething(with: fooBar)
fooBar.age

// -----3-----
class Tesla {
    let manufacturer = "Tesla"
    let model: String
    let year: Int
    
    init() {
        self.model = "X"
        self.year = 2023
        // can not call self.init here because this function is not convenience
        // self.init(model: "X", year: 2023)
    }
    
    init(model: String, year: Int) {
        self.model = model
        self.year = year
    }
    
    convenience init(model: String) {
        self.init(model: model, year: 2023)
    }
}
class TeslaModelY: Tesla {
    override init() {
        // can not call convenience init
        super.init(model: "Y", year: 2023)
    }
}

// -----4-----
class MyClass {
    init() {
        "Initialized"
    }
    func doSomething() {
        "Do something"
    }
    deinit {
        "Deinitialized"
    }
}
let myClosure = {
    let myClass = MyClass()
    myClass.doSomething()
}
myClosure()
```

## Protocol

like interface in java

```swift
// -----1-----
protocol HasName {
    var name: String { get }
    var age: Int { get set }
}
extension HasName {
    func describeMe() -> String {
        "Your name is \(name) and you are \(age) years old"
    }
    mutating func increaseAge() {
        self.age += 1
    }
}
struct Dog: HasName {
    let name: String
    var age: Int
}
var woof = Dog(name: "Woof", age: 10)
woof.name // Woof
woof.age // 10
woof.age += 1
woof.age // 11
woof.describeMe() // Your name is Woof and you are 11 years old
woof.increaseAge()
woof.age // 12

// -----2-----
protocol Vehicle {
    var speed: Int { get set }
    mutating func increaseSpeed(by value: Int)
}
extension Vehicle {
    mutating func increaseSpeed(by value: Int) {
        self.speed += value
    }
}
struct Bike: Vehicle {
    var speed = 0
}
var bike = Bike() //  can not use let here
bike.speed // 0
bike.increaseSpeed(by: 10)
bike.speed // 10

func describe(obj: Any) -> String {
    if obj is Vehicle {
        return "obj conforms to the Vehicle protocol"
    } else {
        return "obj does NOT conform to the Vehicle protocol"
    }
}
describe(obj: bike) // "obj conforms to the Vehicle protocol"

// -----3-----
// keyword "is" does NOT narrow the type of variables, but "as" does
bike.speed // 10
func increaseSpeedIfVehicle (obj: Any) {
    if var vehicle = obj as? Vehicle { // notice this "var" will copy struct
        vehicle.speed // 10
        vehicle.increaseSpeed(by: 10)
        vehicle.speed // 20
    } else {
        "This is not a vehicle"
    }
}
increaseSpeedIfVehicle(obj: bike)
bike.speed // 10 // because this bike is from struct, if this bike is a instance of class, it will be 20
```

## Extension

```swift
// -----1-----
struct Person {
    let firstName: String
    let lastName: String
}
extension Person {
    init(fullName: String) {
        let components = fullName.components(separatedBy: " ")
        self.init(firstName: components.first ?? fullName, lastName: components.last ?? fullName)
    }
}
let person = Person(fullName: "Foo Bar")
person.firstName // Foo
person.lastName // Bar

// -----2-----
protocol GoesVroom {
    var vroomValue: String { get }
    func goVroom() -> String
}
extension GoesVroom {
    func goVroom() -> String { "\(self.vroomValue) goes vroom!"}
}
struct Car {
    let manufactuer: String
    let model: String
}
let modelX = Car(manufactuer: "Tesla", model: "X")
extension Car: GoesVroom {
    var vroomValue: String {
        "\(self.manufactuer) model \(self.model)"
    }
}
modelX.goVroom() // Tesla model X goes vroom!

// -----3-----
extension GoesVroom {
    func goVroomVroomEvenMore() -> String {
      "\(self.vroomValue) is vrooming even more!"
    }
}
// extension can use multiple times.
```

## Generic

```swift
// -----1-----
func perform<N: Numeric>(
    _ op: (N, N) -> N,
    on lhs: N,
    and rhs: N
) -> N { op(lhs, rhs) }
perform(+, on: 10, and: 20) // 30 // the N is type Int

// -----2-----
protocol CanRun { func run() }
protocol CanJump { func jump() }
struct Person: CanRun, CanJump {
    func jump() {"Jumping..."}
    func run() {"Running..."}
}
func jumpAndRun<T: CanRun & CanJump>(_ value: T) {
    value.jump()
    value.run()
}
let person = Person()
jumpAndRun(person)

// -----3-----
// `extension [String]` equal `extension Array where Element == String`
extension [String] {
    func longestString() -> String? {
        self.sorted{
            (lhs: String, rhs: String) -> Bool in lhs.count > rhs.count
        }.first
    }
}
[ "Foo", "Bar Baz", "Qux" ].longestString() // "Bar Baz"


// -----4-----
protocol View {
    func addSubView(_ view: View)
}
extension View {
    func addSubView(_ view: View){}
}
struct Button: View { }
struct Table: View { }
protocol PresentableAsView {
    associatedtype ViewType: View
    func produceView() -> ViewType
    func configure(
        superView: View,
        thisView: ViewType
    )
    func present(
        view: ViewType,
        on superView: View
    )
}
extension PresentableAsView {
    func present(
        view: ViewType,
        on superView: View
    ) {
        superView.addSubView(view)
    }
    
    func configure(
        superView: View,
        thisView: ViewType
    ) {
        // empty by default
    }
}
struct MyButton: PresentableAsView {
    func produceView() -> Button { // ViewType generic now as type Button
        Button()
    }
    func configure(superView: View, thisView: Button) {
        
    }
}
extension PresentableAsView where ViewType == Button {
    func doSomethingWithButton() -> String {
        "This is a button"
    }
}
let button = MyButton()
button.doSomethingWithButton() // "This is a button"

struct MyTable: PresentableAsView {
    
    func produceView() -> Table { // ViewType generic now as type Button
        Table()
    }
    
}
let table = MyTable()
// table.doSomethingWithButton() // There is NOT doSomethingWithButton function on Table instance

```

## Optional

```swift
func multiplyByTwo(_ value: Int? = nil) -> Int {
    if let value {
        return value * 2
    } else {
        return 0
    }
}
multiplyByTwo()

let age: Int? = nil
func checkAge () {
    guard age != nil else {
        "Age is nil as expected"
        return
    }
    "Age is not nil here, stange!"
}
checkAge()
```

