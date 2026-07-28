# 类与对象



## 类


### 实例变量

使用可空类型声明的未初始化实例变量的值为 null 。不可空实例变量==必须在声明时初始化==。
```dart
class Point {
	double? x;
	double? y;
	double z = 0;
}
```

所有实例变量都会生成一个隐式 *getter* 方法。没有初始值的非 final 实例变量和 late final 实例变量还会生成一个隐式 *setter* 方法。

赋值发生在构造函数体执行之前，此时对象还没完全构造好，所以不能访问 this：
```dart
class Person {
  String name;
  int age;
  
  // ❌ 错误：初始化时不能访问 this
  // String info = this.toString();  // 编译错误！
  
  // ❌ 错误：不能访问另一个实例变量
  // String greeting = 'Hello, $name';  // name 此时还不可用
  
  // ✅ 正确：只用了字面量和静态方法
  String id = DateTime.now().toString();  // 不涉及 this，没问题
}
```

执行顺序图解：
```text
1. 声明处的初始化（不能用 this）
       ↓
2. 初始化列表（可以用 this，但有限制）
       ↓
3. 构造函数体（this 完全可用）
```


实例变量可以是 final，在这种情况下，必须精确设置一次。在声明时初始化非 late 变量，使用构造函数参数，或使用构造函数的**初始化列表**。
```dart
class ProfileMark {
	final String name;
	final DateTime start = DateTime.now();
	
	ProfileMark(this.name);
	ProfileMart.unnamed() : name = '';
}
```

如果您需要在构造函数体开始后分配 `final` 实例变量的值，可以使用以下方法之一：

- 使用 [工厂构造函数](https://dart.wendang.dev/language/constructors#factory-constructors) 。
- 使用 `late final` ，但 [_小心：_](https://dart.wendang.dev/effective-dart/design#avoid-public-late-final-fields-without-initializers) 没有初始化程序的 `late final` 会向 API 添加一个 setter。



### 隐式接口

Dart的独特设计：每个类都隐式的定义了一个接口，这个接口包含该类的所有公开成员。

其他类可以用`implements`来实现这个接口，必须重写接口中的所有成员。

```dart
// 这个类既是具体的实现，也是一个接口
class Person {
  String name;
  int age;
  
  Person(this.name, this.age);
  
  void greet() {
    print('Hello, I am $name');
  }
}

// 用 implements 实现 Person 接口
class Student implements Person {
  @override
  String name;
  
  @override
  int age;
  
  // 必须重写所有成员，即使逻辑一模一样
  @override
  void greet() {
    print('Hello, I am student $name');
  }
}
```

`Student` 用 `implements Person` 时，必须把 `Person` 里的 `name`、`age`、`greet()` 全部重写一遍，因为 `implements` 只继承接口定义，不继承任何实现。

`extends` vs `implements`

| 关键字          | 继承实现     | 继承接口   | 是否必须重写     |
| :----------- | :------- | :----- | :--------- |
| `extends`    | ✅ 继承父类实现 | ✅ 继承接口 | 可选         |
| `implements` | ❌ 不继承实现  | ✅ 继承接口 | **必须全部重写** |


### 类变量和方法

#### 静态变量

静态变量直到使用时才会初始化。

```dart
class Queue {
    static const initialCapacity = 16;
}
```

#### 静态方法

静态方法（类方法）不作用域实例，因此无法访问 this。但是，它们可以访问静态变量：

```dart
import 'dart:math';
class Point {
    double x, y;
    Point(this.x, this.y);
    static double distanceBetween(Point a, Point b) {
        var dx = a.x - b.x;
        var dy = a.y - b.y;
        return sqrt(dx * dx + dy * dy);
    }
}

void main() {
    var a = Point(2, 2);
    var b = Point(4, 4);
    var distance = Point.distanceBetween(a, b);
    assert(2.8 < distance && distance < 2.9);
    print(distance);
}
```


## 构造函数

>构造函数是创建类实例的特殊函数


Dart 实现多种类型的构造函数。 除了默认构造函数外， 这些函数使用与其类相同的名称。

- [生成式构造函数](https://dart.wendang.dev/language/constructors/#generative-constructors) ：创建新实例并初始化实例变量。
- [默认构造函数](https://dart.wendang.dev/language/constructors/#default-constructors) ：在未指定构造函数时用于创建新实例。它不接受参数且未命名。
- [命名构造函数](https://dart.wendang.dev/language/constructors/#named-constructors) ：阐明构造函数的目的或允许为同一类创建多个构造函数。
- [常量构造函数](https://dart.wendang.dev/language/constructors/#constant-constructors) ：创建作为编译时常量的实例。
- [工厂构造函数](https://dart.wendang.dev/language/constructors/#factory-constructors) ：创建子类型的新的实例或从缓存中返回现有实例。
- [重定向构造函数](https://dart.wendang.dev/language/constructors/#redirecting-constructors) ：将调用转发到同一类中的另一个构造函数。


### 构造函数类型

#### 生成式构造函数

要实例化一个类，请使用生产式构造函数
```dart
class Point {
	double x;
	double y;
	
	// 带有初始化形式参数的生成式构造函数
	Point(this.x, this.y);
}
```

#### 默认构造函数

如果未声明构造函数，Dart会生成并使用默认构造函数。默认构造函数是一个不带参数或名称的生成式构造函数。

#### 命名构造函数

使用命名构造函数可以为一个类实现多个构造函数，或提供额外的清晰度，其中名称可以自己取。

```dart
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  // 设置 x 和 y 实例变量
  // 在构造函数体运行之前。
  Point(this.x, this.y);

  // 命名构造函数
  Point.origin()
      : x = xOrigin,
        y = yOrigin;
}
```

子类不会继承超类的命名构造函数。 要创建具有在超类中定义的命名构造函数的子类， 请在子类中实现该构造函数。

由于 Dart==不支持构造函数重载==。如果你的类需要多种不同的构造方式，命名构造函数就是唯一的解决方案。

#### 常量构造函数

如果类需要生成不变得对象，请将这些对象设为编译时常量。要将对象设为编译时常量，请定义一个 const 构造函数，==使用 const 构造函数的条件是，所有实例变量都必须是 final 的。==

```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```

常量构造函数并不总是创建常量。 它们可能在非 `const` 上下文中被调用。 要了解更多信息，请参阅有关 [使用构造函数](https://dart.wendang.dev/language/classes#using-constructors) 的部分。

#### 重定向函数

构造函数可能会重定向到同一类中的另一个构造函数。==重定向构造函数的函数体必须是空的==。构造函数在冒号 (:) 后使用 `this` 而不是类名。
```dart
class Point {
	double x, y;
	
	Point(this.x, this.y);
	
	// 委托给主构造函数
	Point.alongXAxis(double x) : this(x, 0);
}
```

#### 工厂构造函数

当遇到以下两种实现构造函数的情况时，使用 factory 关键字：
- 构造函数并不总是创建其类的新的实例。虽然工厂构造函数不能返回null，但它可能会返回：
	- 从缓存中返回现有实例，而不是创建新的实例。
	- 子类型的新的实例。
- 需要在构造实例前执行非平凡的工作。这可能包括检查参数或执行初始化列表中无法处理的任何其他处理。

以下示例包含两个工厂构造函数。
- Logger 工厂构造函数从缓存中返回对象
- Logger.fromJson 工厂构造函数从 JSON 对象初始化 final 变量。

```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache 是库私有的，这要感谢其名称前面的 _。
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

#### 重定向工厂构造函数


#### 构造函数撕裂

就是把构造函数当作一个普通函数来传递，而不是立即调用它来创建对象。这和之前的函数撕裂完全一致，只是这里的函数是构造函数。
比如：
```dart
var names = ['Alice', 'Bob', 'Charlie'];

// 把 Person 构造函数传给 map，对每个元素创建 Person 对象
var people = names.map(Person.new).toList();
// 等价于：names.map((name) => Person(name)).toList()

print(people[0].name); // Alice
```

命名构造函数同样可以被撕裂：
```dart
class Point {
  final double x, y;
  Point(this.x, this.y);
  Point.origin() : x = 0, y = 0;
}

var factory1 = Point.new;      // 撕裂默认构造函数
var factory2 = Point.origin;   // 撕裂命名构造函数

var p1 = factory1(1.0, 2.0);   // Point(1.0, 2.0)
var p2 = factory2();           // Point.origin()
```


### 实例变量初始化

>Dart 可以通过三种方式初始化变量

#### 在声明中初始化实例变量

声明变量时初始化实例变量
```dart
class PointA {
  double x = 1.0;
  double y = 2.0;

  // 隐式默认构造函数将这些变量设置为 (1.0,2.0)
  // PointA();

  @override
  String toString() {
    return 'PointA($x,$y)';
  }
}

```

#### 使用初始化形式参数

当构造函数的参数只是为了直接赋值给实例变量时，你可以直接在参数列表中写 `this.propertyName`，省去手动在函数体里写赋值语句。
```dart
class Point {
  double x, y;
  
  // 繁琐写法：手动赋值
  // Point(double x, double y) {
  //   this.x = x;
  //   this.y = y;
  // }
  
  // 简洁写法：初始化形式参数（推荐）
  Point(this.x, this.y);
}
```

`this`的使用原则是
- 有名称冲突时，加上`this`
- 没有冲突时，可以省略`this`
- 初始化形式参数是例外：在这种简写语法中，**必须**加上 `this`，这是语法要求。
- **对象还没完全创建好之前，不能访问 `this`**。





#### 使用初始化列表
