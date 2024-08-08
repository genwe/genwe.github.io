---
layout: post
comments: true
title: iOS常见的设计模式(一)
---

主要学习一下常见的设计模式(桥接模式、观察者模式、中介者模式等)

#### 设计模式是什么？

设计模式是软件设计过程中常见问题的典型解决方案、代码设计经验的总结。

#### 争议

因为早年间编程语言或技术缺少必要的抽象功能，设计模式可为语言提供更优功能的蹩脚解决方案。大部分现代编程语言中部分设计模式是透明的(lambda实现策略模式)。

不当教条的使用，使问题复杂化。

#### 学习设计模式的意义
设计模式的本质是面向对象设计原则的运用，是对类的封装、继承、多态以及类的关联和组合关系的充分理解。

正确的使用可以提高开发者设计能力，使代码灵活性好、可维护性强。

#### 分类
创建型：封装对象创建过程，将对象的创建与使用分离，让使用者不用关注对象的创建细节。

结构型：如何将对象、类按照某种布局组成更大的结构，并同时保持结构的灵活、高效。

行为型：负责对象间的高效沟通和职责委派，描述多个类、对象之间如何协作共同完成单个对象无法独立完成的任务。

#### 常见设计模式
##### 委托模式
iOS常用delegate模式，常用来逆向传值
```swift
protocol MyDelegate: AnyObject {
    func sendData(str: String)
}

class A: MyDelegate {
    func sendData(str: String) {
        print("MyDelegate \(str)")
    }
}

class B {
    weak var delegate: MyDelegate?
    func test() {
        delegate?.sendData(str: "test")
    }
}

let delegate = A()
let b = B()
b.delegate = delegate
b.test()
```
使用组合的方式，将工作委派给其他对象😂

代理、桥接、适配器、装饰器等模式，相似的特征是通过委托的方式对一个或多个对象进行包装，不过各自解决了不同的问题；

> 计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决。😆

##### 桥接模式
属于结构型模式，定义：将抽象与实现分离，是它们可以独立变化。

不知道是翻译的问题，还是当年作者描述的问题，定义真的很扯！

正常的类比描述是某个类具有两个或多个变化维度，比如图形有形状、颜色的不同，如果使用继承方式，则有m种形状和n中颜色的图形有m*n个子类；桥接模式使用组合的方式代替继承，抽取一个维度使其成为独立的类层次，使原来的类引用这个新层次的对象。如新增颜色类，在形状类中添加某个颜色对象的成员变量，相关颜色的工作委托给颜色对象，这样颜色的变化就不需要修改形状的类层次，可以独立变化了。
```swift
protocol Color {
    func getColor() -> String
}

class Yellow: Color {
    func getColor() -> String {
        return "yellow"
    }
}

class Red: Color {
    func getColor() -> String {
        return "red"
    }
}

class Bag {
    var color: Color
    
    init(color: Color) {
        self.color = color
    }
    public func name() -> String {
        return color.getColor() + "Bag"
    }
}

class HandBag: Bag {
    override func name() -> String {
        return color.getColor() + "HandBag"
    }
}

class Wallet: Bag {
    override func name() -> String {
        return color.getColor() + "Wallet"
    }
}

let color: Color = Red()
let bag = HandBag(color: color)
bag.name()
```
这个例子好像举得不好呀，如果有个包Bag类，需要一个红色的包，常规想法应该是加一个颜色属性，而不是继承Bag创建一个RedBag呀。。。

##### 观察者模式
定义：多个对象间存在一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。又称作发布-订阅模式、模式-视图模式。

主要角色：
1. 抽象主题(Subject)角色，提供一个用于保存观察者对象的集合类和增加、删除观察者对象的方法、通知所有观察者的抽象方法。
2. 具体主题(Concrete Subject)角色，实现抽象主题中的通知方法。
3. 抽象观察者(Observer)角色，是一个接口或抽象类，包含一个更新自己的抽象方法，当接到具体主题的更改通知时被调用。
4. 具体观察者(Concrete Observer)角色，实现抽象观察者定义的抽象方法。

```swift
protocol Observer {
    func update()
}

class ConcreteObserver1: NSObject, Observer {
    func update() {
        print("ConcreteObserver1 update\n")
    }
}

class ConcreteObserver2: NSObject, Observer {
    func update() {
        print("ConcreteObserver2 update\n")
    }
}

class Subject {
    var observers = NSMutableSet()
    public func register(observer: Observer) {
        observers.add(observer)
    }
    
    public func remove(observer: Observer) {
        observers.remove(observer)
    }
    
    func notifyObserver() { }
}

class ConcreteSubject: Subject {
    override func notifyObserver() {
        for observer in observers {
            if let observer = observer as? Observer {
                observer.update()
            }
        }
    }
}

let subject = ConcreteSubject()
let ob1 = ConcreteObserver1()
let ob2 = ConcreteObserver2()
subject.register(observer: ob1)
subject.register(observer: ob2)
subject.notifyObserver()
```
以上例子仅供参考(存在不少缺陷，比如未考虑Subject中集合的强引用问题、集合未做多线程读写保护、功能单一，同个推送监听不同数据怎么办、等)

##### 中介者模式(Mediator)
定义：由一个中介对象来封装一系列对象之间的交互，使原有对象之间的耦合松散，可以独立变化它们之间的交互。减少对象之间的无序依赖关系，限制对象之间的直接交互。
缺点：将原本多个对象直接相互依赖变成了中间者与多个同事类的依赖关系。当同事类变多时，中介者会变得臃肿、负责且难以维护。

主要角色：
1. 抽象中介者(Mediator)角色，是中介者的接口，提供了同事对象注册和转发的抽象方法。
2. 具体中介者(Contrete Mediator)角色，协调各个同事角色之间的交互关系。
3. 抽象同事类(Colleague)角色，是同事类接口，保存中介者对象，提供同事对象交互的抽象方法，实现公共功能。
4. 具体同事类(Concrete Colleague)角色，实现抽象同事类，当需要与其他同事交互时，由中介者对象负责后续的交互。

![_config.yml](/images/mediator.png)

```swift
class Colleague {
    weak var mediator: Mediator?
    init(mediator: Mediator? = nil) {
        self.mediator = mediator
    }
}

protocol Mediator: AnyObject {
    func dispatch(colleague: Colleague, message: String)
}

class ConcreteColleague1: Colleague {
    func testA(){
        print("ConcreteColleague1 func testA")
        mediator?.dispatch(colleague: self, message: "testA")
    }
    func testB(){
        print("ConcreteColleague1 func testB")
        mediator?.dispatch(colleague: self, message: "testB")
    }
}

class ConcreteColleague2: Colleague {
    func testC(){
        print("ConcreteColleague1 func testC")
        mediator?.dispatch(colleague: self, message: "testC")
    }
    func testD(){
        print("ConcreteColleague1 func testD")
        mediator?.dispatch(colleague: self, message: "testD")
    }
}

class ConcreteMediator: Mediator {
    private var col1: ConcreteColleague1
    private var col2: ConcreteColleague2
    
    init(col1: ConcreteColleague1, col2: ConcreteColleague2) {
        self.col1 = col1
        self.col2 = col2
        self.col1.mediator = self
        self.col2.mediator = self
    }
    
    func dispatch(colleague: Colleague ,message: String) {
        if message == "testA" {
            self.col2.testD()
        } else if message == "testC" {
            self.col1.testB()
        }
    }
}

let col1 = ConcreteColleague1()
let col2 = ConcreteColleague2()
let meidator = ConcreteMediator(col1: col1, col2: col2)
col1.testA()
col2.testC()
```

中介者能使得程序更易于修改和扩展，能更方便地对独立的组件进行复用。

中介者模式常用于帮助程序GUI组件之间通信。在MVC模式中，控制器是中介者的同义词。


##### MVC、MVP(被动视图模式的MVP)

![_config.yml](/images/mvc.png)

MVC各层职责:

1. Models: 业务模型层，负责数据的获取和处理、数据持久化等
2. Views: 展示层
3. Controllers: 控制器层，是Model、View的中间人。当用户对View有操作时，它负责去修改相应的Model；当Model的值发生变化时，它负责去更新相应的View；从而保证M和V的可测试性和复用性。


![_config.yml](/images/mvp.png)

MVP各层职责:

1. Models: 同上
2. Views: 由View、Controller共同组成，
3. Presenter: 表示层，负责逻辑处理

```swift
//: Playground

import UIKit
import PlaygroundSupport

struct Person {     // Model
    let firstName: String
    let lastName: String
}

protocol GreetingView {
    func setGreeting(greeting: String)
}

protocol GreetingViewPresenter {
    init (view : GreetingViewController, person : Person)
    func showGreeting()
}

class GreetingPresenter : GreetingViewPresenter {   //Presenter
    weak var view : GreetingViewController?
    var person : Person
    
    required init(view: GreetingViewController, person: Person) {
        self.view = view
        self.person = person
    }
    
    func showGreeting() {
        let greeting = "Hello" + " " + person.firstName + " " + person.lastName
        view?.setGreeting(greeting: greeting)
    }
}

class GreetingViewController: UIViewController, GreetingView {  //View
    var presenter : GreetingPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        showGreetingButton.addTarget(self, action: #selector(didTapButton(button:)), for: .touchUpInside)
        setupUI()
    }
    
    @objc func didTapButton(button : UIButton) {
        presenter .showGreeting()
    }
    
    func setGreeting(greeting: String) {
        greetingLabel.text = greeting
    }
    
    func setupUI() {
        view.frame = CGRect(x: 0, y: 0, width: 320, height: 480)
        view.backgroundColor = UIColor.white
        showGreetingButton.frame = CGRect(x: 10.0, y: 10.0, width: 90.0, height: 30.0)
        showGreetingButton.backgroundColor = UIColor.blue
        
        greetingLabel.frame = CGRect(x: 10.0, y: 60.0, width: 200.0, height: 20.0)
        greetingLabel.textColor = UIColor.blue
        greetingLabel.text = "Say hello to who?"
        
        view.addSubview(showGreetingButton);
        view.addSubview(greetingLabel);
    }
}

// MVP
let model = Person(firstName: "Ma", lastName: "Jack")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter

PlaygroundPage.current.liveView = view
```

##### 哪些设计模式可以用来优化很多if-else的情况？

策略模式、责任链模式等

##### 函数式接口是什么个概念？
> 函数式接口在java中是指: 有且仅有一个抽象方法的接口。

有什么用？函数式接口可以被隐式转换为lambda表达式。支持行为参数传递，比如传递lambda、方法引用、函数式接口对应的实例对象等。

扯一下 函数式接口，可以类比swift的闭包来方便理解

##### 策略模式
定义：对象行为型模式，该模式定义一系列算法，并将每个算法封装起来(分别放入独立的类中)，使它们可以相互替换，且算法的变化不会影响使用的用户。
主要角色：
1. 抽象策略(Strategy)类：定义一个公共接口/抽象类，各种不同的算法以不同的方式实现这个接口，环境角色使用这个接口调用不同的算法。
2. 具体策略(Concrete Strategy)类：实现抽象策略定义的接口，提供具体的算法实现。
3. 环境(Context)类：持有一个策略类的引用，给用户调用。

传统实现方式

```swift
protocol Strategy {
    func compute(a: Int, b: Int) -> Int
}

class Add: Strategy {
    func compute(a: Int, b: Int) -> Int {
        return a + b
    }
}

class Multiply: Strategy {
    func compute(a: Int, b: Int) -> Int {
        return a * b
    }
}

class Context {
    let strategy: Strategy
    init(strategy: Strategy) {
        self.strategy = strategy
    }
    func use(a: Int, b: Int) {
        strategy.compute(a: a, b: b)
    }
}

let context = Context(strategy: Add())
context.use(a: 1, b: 1)
```

简化版
```swift
let add = {(a: Int, b: Int) -> Int in
    return a + b
}

let mutiply = {(a: Int, b: Int) -> Int in
    return a * b
}

class FPContext {
    let strategy: (Int, Int) -> Int
    init(strategy: @escaping ((Int, Int) -> Int)) {
        self.strategy = strategy
    }
    func use(a: Int, b: Int) {
        strategy(a, b)
    }
}

let context = FPContext(strategy: mutiply)
context.use(a: 2, b: 2)
```
设计模式中的不少模式存在都是由于函数的使用限制，需要在使用时用类包裹函数。如果语言存在一等函数的情况下，可以简化不少问题。

#### 小结
1. 对事物的认识是一个渐进的过程，最近一次(至少一年前)看的时候，当时看的是java版的例子，留下比较深印象是java中接口的使用；这次看的时候，遇到不明白的地方也耐着性子再花点时间看看，想一下相关的问题加深理解，以前设计模式高深莫测的感觉渐渐消散了，好像明白了点什么。
2. 纸上得来终觉浅，输出是一种不错的学习方式。别人的永远都是别人的。。。
3. 结构型模式与行为型模式是如何归类的，本来以为结构型使用的时候仅仅暴露一个类，但是并不是这样，也是有多个类协作的情况
4. 好吧，先就水到这里吧🐶

***
#### 引用源
+ [设计模式java版](http://c.biancheng.net/view/1317.html)
+ [Swift中的设计模式](https://www.infoq.cn/article/design-patterns-in-swift) 
