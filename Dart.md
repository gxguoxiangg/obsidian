# 语法基础

## 变量

### 空安全

Dart 语言强制执行健全的空安全。

空安全可防止因无意访问设置为 `null` 的变量而导致的错误。此错误称为空解引用错误。当您访问表达式（其计算结果为 `null` ）的属性或调用其方法时，就会发生空解引用错误。此规则的例外情况是当 `null` 支持属性或方法时，例如 `toString()` 或 `hashCode` 。使用空安全，Dart 编译器会在编译时检测这些潜在错误。

例如，假设您想找到 `int` 变量 `i` 的绝对值。如果 `i` 为 `null` ，则调用 `i.abs()` 会导致空解引用错误。在其他语言中，尝试此操作可能会导致运行时错误，但 Dart 的编译器会禁止这些操作。因此，Dart 应用不会导致运行时错误。

空安全引入了三个主要更改：

1. 当您为变量、参数或其他相关组件指定类型时，您可以控制该类型是否允许 null。
2. 您必须在使用变量之前对其进行初始化。可空变量默认为 null。Dart 不会为不可空类型设置初始值。它强制您设置初始值。Dart 不允许您观察未初始化的变量。这可以防止您访问属性或调用方法，其中接收方的类型可以是 `null` ，但 `null` 不支持使用的方法或属性。
3. 您不能访问具有可空类型的表达式的属性或调用其方法。即使是 `null` 本身也有的属性或方法（比如 `toString()`、`hashCode`），也不能直接调用。虽然 `null.toString()` 在技术上不会崩溃，但 Dart 从**类型安全**的角度一刀切：只要类型是可空的，就一律不许直接用。

健全的空安全将潜在的 **运行时错误** 转换为 **编辑时** 分析错误。当非空变量已被：

- 未初始化为非空值。
- 分配了 `null` 值。

此检查允许您在部署应用 _之前_ 纠正这些错误。

### 默认值

具有可空类型的未初始化变量的初始值为 `null` 。即使是具有数字类型的变量最初也是 null，因为数字（就像 Dart 中的所有其他内容一样）都是对象。

int? lineCount;  
assert(lineCount == null);

### 延时变量

`late` 修饰符有两种用例：

- 声明一个在声明后初始化的不可空变量。
- 延迟初始化变量。

通常，Dart 的控制流分析可以检测到在使用不可空变量之前何时将其设置为非空值，但有时分析会失败。两种常见情况是**顶级变量和实例变量**：Dart 通常无法确定它们是否已设置，因此它不会尝试。

如果您确定在使用变量之前已设置该变量，但 Dart 不同意，则可以通过将变量标记为 `late` 来纠正此错误：
```dart
late String description;    // 不加 late 关键字，则会发生编译时错误  
​  
void main() {  
  description = 'Feijoada!';    // 如果没有初始化 late 变量，则使用该变量时会发生运行时错误  
  print(description);  
}
```

当您将变量标记为 `late` 但在其声明处对其进行初始化时，则在第一次使用该变量时运行初始化程序。这种延迟初始化在以下几种情况下非常方便：

- 可能不需要该变量，并且初始化该变量的成本很高。
- 您正在初始化实例变量，并且其初始化程序需要访问 `this` 。

在以下示例中，如果从未使用 `temperature` 变量，则从未调用代价高昂的 `readThermometer()` 函数：
```dart
// 这是程序对 readThermometer() 的唯一调用。  
late String temperature = readThermometer(); // 延迟初始化。
```

### final 和 const

如果您不打算更改变量，请使用 `final` 或 `const` ，替换 `var` 或添加到类型中。 `final` 变量只能设置一次； `const` 变量是编译时常量。（ `const` 变量隐式为 `final` 。）

[实例变量](https://dart.wendang.dev/language/classes#instance-variables) 可以是 `final` ，但不能是 `const` 。

以下是创建和设置 `final` 变量的示例：
```dart
final name = 'Bob'; // 没有类型注解  
final String nickname = 'Bobby';
```

对于希望成为 **编译时常量** 的变量，请使用 `const` 。如果 `const` 变量位于类级别，请将其标记为 `static const` 。在声明变量的地方，将值设置为编译时常量，例如数字或字符串文字、 `const` 变量或对常量数字进行算术运算的结果：

const bar = 1000000; // 压力单位 (dynes/cm2)  
const double atm = 1.01325 * bar; // 标准大气压

`const` 关键字不仅仅用于声明常量变量。您还可以使用它来创建常量 _值_ ，以及声明创建常量值的构造函数。任何变量都可以具有常量值。

|表达式|变量 `a` 可变吗？|列表本身可变吗？|
|---|---|---|
|`const a = [1, 2, 3]`|❌ 不可变|❌ 不可变|
|`var a = const [1, 2, 3]`|✅ 可变|❌ 不可变|
```dart
var foo = const [];  
final bar = const [];  
const baz = []; // 等同于 `const []`
```

您可以更改非 final、非 const 变量的值，即使它以前具有 `const` 值：

```dart
foo = [1, 2, 3]; // 以前是 const []  
​  
baz = [42]; // 错误：常量变量不能赋值。
```


您可以定义使用 [类型检查和强制转换](https://dart.wendang.dev/language/operators#type-test-operators) （ `is` 和 `as` ）、 [集合 `if`](https://dart.wendang.dev/language/collections#control-flow-operators) 和 [扩展运算符](https://dart.wendang.dev/language/collections#spread-operators) （ `...` 和 `...?` ）的常量：

```dart
const Object i = 3; // i 是具有 int 值的 const Object ...  
const list = [i as int]; // 使用类型转换。  
const map = {if (i is int) i: 'int'}; // 使用 is 和集合 if。  
const set = {if (list is List<int>) ...list}; // ...和扩展运算符。
```


## 运算符

### 类型测试运算符

用于在运行时检查类型

|运算符|含义|
|---|---|
|`as`|类型转换（也用于指定 [库前缀](https://dart.wendang.dev/language/libraries#specifying-a-library-prefix) ）|
|`is`|如果对象具有指定的类型，则为 true|
|`is!`|如果对象不具有指定的类型，则为 true|

`obj is T` 的结果如果 `obj` 实现 `T` 指定的接口则为 true。例如， `obj is Object?` 总是为 true。

仅当您确定对象属于该类型时，才使用 `as` 运算符将对象转换为特定类型。示例：

```dart
(employee as Person).firstName = 'Bob';  
// 如果不确定对象是否为 T 类型，则在使用前使用 is T 检查类型  
if (employee is Person) {  
    emloyee.firstName = 'Bob';  
}
```

### 赋值运算符

要仅在被赋值变量为null时赋值，请使用 ??= 运算符：

```dart
// 如果 b 为 null, 则将值赋给 b; 否则, b 保持不变 
b ??= value
```

### 位运算符

|运算符|含义|
|---|---|
|`&`|与|
|`\|`|或|
|`^`|异或|
|`~`_`expr`_|一元按位取反（0 变成 1；1 变成 0）|
|`<<`|左移|
|`>>`|右移|
|`>>>`|无符号右移|

```dart
// 右移示例，在 Web 上的结果行为不同，  
// 因为操作数的值在掩码为 32 位时会发生变化：  
assert((-value >> 4) == -0x03);  
​  
assert((value >>> 4) == 0x02); // 无符号右移  
assert((-value >>> 4) > 0); // 无符号右移
```


### 条件表达式

- _`expr1`_ `??` _`expr2`_
    
    如果 _expr1_ 不为 null，则返回其值； 否则，计算并返回 _expr2_ 的值。
    

如果布尔表达式测试 null 值， 请考虑使用空值合并运算符 `??` （也称为空值合并运算符）。

String playerName(String? name) => name ?? 'Guest';

### 级联表达式

允许您对同一对象执行一系列操作。除了访问实例成员外，您还可以对同一对象调用实例方法。这通常可以节省您创建临时变量的步骤，并允许您编写更流畅的代码。

考虑以下代码：

```dart
var paint = Paint()  
  ..color = Colors.black  
  ..strokeCap = StrokeCap.round  
  ..strokeWidth = 5.0;
```


构造函数 `Paint()` 返回一个 `Paint` 对象。 级联表示法后面的代码对该对象进行操作，忽略任何可能返回的值。

前面的示例等效于以下代码：
```dart
var paint = Paint();  
paint.color = Colors.black;  
paint.strokeCap = StrokeCap.round;  
paint.strokeWidth = 5.0;
```

如果级联操作的对象可能为 null， 则对第一个操作使用 _空值简写_ 级联（ `?..` ）。 以 `?..` 开头可以保证不会对该 null 对象尝试任何级联操作。

```dart
querySelector('#confirm') // 获取对象。  
  ?..text = 'Confirm' // 使用其成员。  
  ..classes.add('important')  
  ..onClick.listen((e) => window.alert('Confirmed!'))  
  ..scrollIntoView();
```

您还可以嵌套级联。例如：

```dart
final addressBook = (AddressBookBuilder()  
      ..name = 'jenny'  
      ..email = 'jenny@example.com'  
      ..phone = (PhoneNumberBuilder()  
            ..number = '415-555-0100'  
            ..label = 'home')  
          .build())  
    .build();
```

### 展开运算符

展开运算符计算一个产生集合的表达式， 解包结果值，并将它们插入另一个集合中。

**展开运算符实际上不是运算符表达式** 。 `...` / `...?` 语法是集合字面量本身的一部分。 因此，您可以在 [集合](https://dart.wendang.dev/language/collections#spread-operators) 页面上了解有关展开运算符的更多信息。

因为它不是运算符，所以语法没有任何“ [运算符优先级](https://dart.wendang.dev/language/operators/#operators) ”。 实际上，它具有最低的“优先级”——任何类型的表达式都可以作为展开目标，例如：

### 其他运算符

|运算符|名称|含义|
|---|---|---|
|`()`|函数应用|表示函数调用|
|`[]`|下标访问|表示对可重写 `[]` 运算符的调用；示例： `fooList[1]` 将整数 `1` 传递给 `fooList` 以访问索引 `1` 处的元素|
|`?[]`|条件下标访问|与 `[]` 相同，但最左边的操作数可以为 null；示例： `fooList?[1]` 将整数 `1` 传递给 `fooList` 以访问索引 `1` 处的元素，除非 `fooList` 为 null（在这种情况下，表达式的值为 null）|
|`.`|成员访问|指的是表达式的属性；示例： `foo.bar` 从表达式 `foo` 中选择属性 `bar`|
|`?.`|条件成员访问|与 `.` 相同，但最左边的操作数可以为 null；示例： `foo?.bar` 从表达式 `foo` 中选择属性 `bar` ，除非 `foo` 为 null（在这种情况下， `foo?.bar` 的值为 null）|
|`!`|非空断言运算符|将表达式转换为其底层的非空类型，如果转换失败则抛出运行时异常；示例： `foo!.bar` 断言 `foo` 不为 null 并选择属性 `bar` ，除非 `foo` 为 null，在这种情况下会抛出运行时异常|

## 元数据

使用元数据为代码提供附加信息。元数据注释以字符 `@` 开头，后跟编译时常量的引用（例如 `deprecated` ）或对常量构造函数的调用。

所有 Dart 代码都可以使用四种注释： [`@Deprecated`](https://api.dart.dev/dart-core/Deprecated-class.html) 、 [`@deprecated`](https://api.dart.dev/dart-core/deprecated-constant.html) 、 [`@override`](https://api.dart.dev/dart-core/override-constant.html) 和 [`@pragma`](https://api.dart.dev/dart-core/pragma-class.html) 。有关使用 `@override` 的示例，请参阅 [扩展类](https://dart.wendang.dev/language/extend) 。以下是如何使用 `@Deprecated` 注释的示例：

## 库与导入

`import` 和 `library` 指令可以帮助您创建模块化且可共享的代码库。库不仅提供 API，而且还是隐私单元：以下划线 (`_`) 开头的标识符仅在库内可见。_每个 Dart 文件（及其部分）都是一个 [库](https://dart.wendang.dev/tools/pub/glossary#library)_，即使它不使用 [`library`](https://dart.wendang.dev/language/libraries/#library-directive) 指令。

```dart
// 使用库  
// 对于内置库, URL具有特殊的dart:模式  
import 'dart:html'  
import 'package:test/test.dart'  
      
      
import 'package:lib1/lib1.dart';  
import 'package:lib2/lib2.dart' as lib2;  
​  
// 使用 lib1 中的 Element。  
Element element1 = Element();  
​  
// 使用 lib2 中的 Element。  
lib2.Element element2 = lib2.Element();
```


**导入库的一部分**

```dart
// 只导入 foo。  
import 'package:lib1/lib1.dart' show foo;  
​  
// 导入除 foo 之外的所有名称。  
import 'package:lib2/lib2.dart' hide foo;

```

## 关键字

# 类型 Types

## 记录 Records

类似于元组？

允许将多个对象捆绑到一个对象中，记录是固定大小的、异构的、并且是类型化的。

记录表达式是以逗号分隔的命名字段或位置字段列表，并用括号括起来。

Records expressions are comma-delimited lists of named or positional fields, enclosed in parentheses:
```dart
var record = ('first', a: 2, b: true, 'list')  
​  
(int, int) swap((int, int) record) {  
    var (a, b) = record;  
    return (b, a)  
}  
// (int ,int) 称为记录类型注解 record type annotation  
// 记录类型注解中，分为 位置字段 和 命名字段
```


在记录类型注解中，命名字段位于所有位置字段之后，并用花括号分隔成类型-名称对的部分：

```dart
({int a, bool b}) record;  
record = (a: 123, b: true);
```

命名字段的名称和类型是类型定义的一部分：

```dart
({int a, int b}) recordAB = (a: 1, b: 2);  
({int x, int y}) recordXY = (x: 3, y: 4);  
// recordAB = recordXY;  
// 编译错误！recordAB 和 recordXY 是不同的类型, 不能相互赋值
```

位置字段则不一样，位置字段的名称纯粹用于文档说明，不会影响记录的类型：

```dart
(int a, int b) recordAB = (1, 2);  
(int x, int y) recordXY = (3, 4);  
​  
recordAB = recordXY; // OK.  
// 这类似于函数声明或函数typedef中的位置参数可以有名称，但这些名称不会影响函数的签名
```


记录字段可以通过内置的 `getter` 方法访问，记录是不可变的，因此字段没有 setter 方法：

```dart
// 位置字段和命名字段可以**任意穿插**，但解析后类型结构固定（位置在前，命名在后）  
var record = ('first', a: 2, b: true, 'last');  
print(record);  
// (first, last, a: 2, c: 3)  
print(record.$1);  
print(record.a);  
print(record.b);  
print(record.$2);

```


## 集合 Collections

### List

```dart
var list = [1, 2, 3];  
// Dart 会推断该列表 list 具有 List<int> 类型
```

在 Dart Collections Literal 字面量的最后一个元素可以添加逗号，这个尾随逗号不会影响集合本身，但可以帮助防止复制粘贴错误：

```dart
var list = ['Cat', 'Boat', 'Plane',];   // OK.
```

常见的格式有：
```dart
var list = [1, 2, 3];  
assert(list.length == 3);  
assert(list[1] == 2);
```

要创建一个编译时常量的列表，`const` 关键字需要添加在字面量前：

```dart
var constantList = const [1, 2, 3]
```


### Set

在Dart中，Set是一个无序、唯一的元素集合。

```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

创建空集的格式：

```dart
var names = <String>{};  
// Set<String> names = {};  // This works. too  
// var names = {};  // Creates a map, not a set
```

常见的格式有：

```dart
// 添加元素  
var elements = <String>{};  
elements.add('fluorine');  
elements.addAll(halogens)  
​  
assert(elements.length == 5);

```

要创建一个常量set，const在集合字面量前添加：
```dart
final constantSet = const {  
  'fluorine',  
  'chlorine',  
  'bromine',  
  'iodine',  
  'astatine',  
};  
// constantSet.add('helium'); // This line will cause an error.
```
### Map

在Dart中，Map的键是唯一的。
```dart
var gifts = {  
  // Key:    Value  
  'first': 'partridge',  
  'second': 'turtledoves',  
  'fifth': 'golden rings',  
};  
​  
var nobleGases = {2: 'helium', 10: 'neon', 18: 'argon'};  
```


​

### 集合元素 Collection elements

集合字面量包含一系列元素。运行时，每个元素都会被求值，产生零个或多个值，然后将这些值插入到最终的集合中。这些元素主要分为两类：叶子元素和控制流元素。
- **叶子元素：将单个项目插入集合字面量中。**
    - 表达式元素：计算单个表达式并将结果值插入集合中。
    - 映射条目元素：计算键值对表达式，并将结果条目插入集合中。
- **控制流元素：有条件地或迭代地向周围的集合添加零个或多个值。**
    - 空感知元素：计算表达式，如果结果不为空`null`，则将该值插入到周围的集合中。
    - 展开元素：遍历给定的序列（集合表达式），并将所有结果值插入到周围的集合中。
    - 空感知扩展元素：类似于扩展元素，但允许集合为空 `null`，如果集合为空则不插入任何内容。
    - 如果元素：根据给定的条件表达式有条件地评估内部元素，`else`如果条件为假，则可以选择评估另一个元素。
    - 对于元素：迭代并重复评估给定的内部元素，插入零个或多个结果值。

#### 表达式元素

```dart
var list = [1 + 2, someFunction()];
```

#### 映射条目元素
```dart
var map = {'key': 'value', 'key': someExpreesion()}
```

#### 空感知元素
```dart
var list = [1, maybeNull?, 3];  // 如果不是 null，才插入它的值
```

#### 展开元素
```dart
var list = [1, ...[2, 3, 4], 5];   // 结果是 [1, 2, 3, 4, 5]
```
#### 空感知展开元素
```dart
var maybeList;
var list = [1, ...?maybeList];  // maybeList 不为空才展开
```

#### If元素
```dart
var list = [1, if (isLoggedIn) 'Welcome', 3];
// 也可以带 else
var list = [1, if (isLoggedIn) 'Welcome' else 'Login', 3];

Object data = 123;
var typeInfo = [
  if (data case int i) 'Data is an integer: $i',
  if (data case String s) 'Data is a string: $s',
  if (data case bool b) 'Data is a boolean: $b',
  if (data case double d) 'Data is a double: $d',
]; // [Data is an integer: 123]
```

#### For元素
```dart
var list = [for (var i = 0; i < 5; i++) u * 2];
// 结果为 [0, 2, 4, 6, 8]
```


易错点：

在以下示例中，名为 a 的列表由于其值为空而被忽略，但名为 b 的列表的元素被添加到名为 items 的列表中。请注意，如果集合本身不为空，但其中包含为空的元素，则这些空元素仍会被添加到结果中。
```dart
List<int>? a = null;  
var b = [1, null, 3];  
var items = [0, ...?a, ...?b, 4]; // [0, 1, null, 3, 4]
```

不能对null进行`...`扩展

```dart
List<String> buildCommandLine(  
  String executable,  
  List<String> options, [  
  List<String>? extraOptions,  
]) {  
  return [  
    executable,  
    ...options,  
    ...extraOptions, // <-- Error  
    // ...?extraOptions, <-- OK  
  ];  
}  
​  
// Usage:  
//   buildCommandLine('dart', ['run', 'my_script.dart'], null);  
// Result:  
//   Compile-time error  
```

Collection elements 本质上是让集合的构建从“过程式”变成了“声明式”，让代码更贴近你对结果的直观描述。


### 泛型


### 类型别名
类型别名使用 *typedef* 关键字
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```
类型可以具有类型参数：
```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {};  // 冗长
ListMapper<String> m2 = {};         // 更短更清晰
```

在大多数情况下，我们建议使用 [内联函数类型](https://dart.wendang.dev/effective-dart/design#prefer-inline-function-types-over-typedefs) 代替函数的 typedef。 但是，函数 typedef 仍然很有用


# 函数 Functions

### 函数的参数

|类型|语法|是否必须|例子|
|---|---|---|---|
|**必填位置参数**|直接写|✅ 必须传|`String name`|
|**可选位置参数**|用 `[ ]` 包起来|❌ 可以不传|`[String? extra]`|
|**可选命名参数**|用 `{ }` 包起来|❌ 可不传（除非加 `required`）|`{String? name}`|

# 模式 Patterns

在Dart中，模式是一种语法类型，类似于语句和表达式。模式表示一组值的形状，这些值可能和实际值匹配。

## Overview & usage

### What patterns do

In general, a pattern can **match** a value, **destructure** a value, or both, depending on the context and shape of the pattern.

一般来说，根据模式的上下文和形状， 模式可以**匹配**一个值，**解构一个值，或者两者兼而有之。**

First, _pattern matching_ allows you to check whether a given value:

- Has a certain shape. 具有某种形状
- Is a certain constant. 是一个确定的东西
- Is equal to something else. 等于其他东西
- Has a certain type. 具有某种类型


#### Matching 匹配

_patterns_ 总是会和一个值对比，确定是否达到预期。

匹配的判定取决于你使用的 _patterns_ 类型，例如，_a constant pattern_ 匹配的判定标准是值是否相等：
```dart
switch (number) {  
    // Constant pattern matches if 1 == number  
    case 1:  
      print('one');  
}
```

许多模式都利用子模式，有时分别称为_外部_模式和_内部_ 模式。模式会递归地与其子模式匹配。例如，任何[集合类型](https://dart.dev/language/collections) 模式的各个字段都可以是 [可变模式](https://dart.dev/language/pattern-types#variable)或[常量模式](https://dart.dev/language/pattern-types#constant)：
```dart
const a = 'a';  
const b = 'b';  
switch (obj) {  
  // List pattern [a, b] matches obj first if obj is a list with two fields,  
  // then if its fields match the constant subpatterns 'a' and 'b'.  
  case [a, b]:  
    print('$a, $b');  
}
```

`switch` 作为表达式的情况下，可以不需要`case`：

```dart
// 表达式: switch 在 = 右边 或 => 右边  
var result = switch (value) { ... };    // 赋值  
String f() = > switch (value) { ... };  // 返回值  
​  
// 语句: switch 独立出现, 不产生值  
switch (value) { ... }  // 独立语句
```

#### Destructuring 解构

当对象与模式匹配时，该模式就可以访问对象的数据并将其分段提取出来。换句话说，该模式对对象进行结构：
```dart
var numList = [1, 2, 3];  
​  
var [a, b, c] = numList;  
​  
print(a + b + c);
```

你可以在解构模式中嵌套[任何类型的模式](https://dart.dev/language/pattern-types)。例如，以下 case 模式匹配并解构一个包含两个元素的列表，其中第一个元素为`'a'``or` `'b'`：
```dart
switch (list) {  
  case ['a' || 'b', var c]:  
    print(c);  
}
```

### Places patterns can appear 模式可能出现的地方

在 Dart 语言中，你可以在多个地方使用模式：

- 局部变量[声明](https://dart.dev/language/patterns#variable-declaration)和[赋值](https://dart.dev/language/patterns#variable-assignment)
- [for 循环和 for-in 循环](https://dart.dev/language/loops#for-loops)
- [if-case](https://dart.dev/language/branches#if-case)和[switch-case](https://dart.dev/language/branches#switch-statements)
- [集合字面量](https://dart.dev/language/collections#control-flow-operators)的控制流


#### 变量声明

在 Dart 允许声明局部变量的任何地方，都可以使用_模式变量声明_。该模式会匹配声明右侧的值。匹配成功后，它会解构该值并将其绑定到新的局部变量：

```dart
// 声明新的变量 a b 和 c  
var (a, [b, c]) = ('str', [1, 2]);
```

模式变量声明必须以 `var` 或 `final` 开头，后跟进模式。

#### 变量赋值

```dart
var (a, b) = ('left', 'right');  
(b, a) = (a, b);  // swap  
print('$a $b');
```

#### switch语句和表达式

每个 case 子句都包含一个模式。这适用于[switch 语句](https://dart.dev/language/branches#switch-statements) 和[表达式](https://dart.dev/language/branches#switch-expressions)，以及 [if-case 语句](https://dart.dev/language/branches#if-case)。你可以在 case 子句中 使用[任何类型的模式。](https://dart.dev/language/pattern-types)

案例模式是可反驳的，它们允许控制流向以下两种情况之一：

- 匹配并解构被开启的对象。
- 如果对象不匹配，则继续执行。

```dart
switch (obj) {  
  // Matches if 1 == obj.  
  case 1:  
    print('one');  
​  
  // Matches if the value of obj is between the  
  // constant values of 'first' and 'last'.  
  case >= first && <= last:  
    print('in range');  
​  
  // 如果 obj 是包含两个字段的记录，则匹配，  
  // 然后将这两个字段分别赋值为 'a' 和 'b'。  
  case (var a, var b):  
    print('a = $a, b = $b');  
​  
  default:  
}
```

​逻辑或模式在switch表达式或语句中让多个case共享一个主题非常有用：
```dart
// Dart 3 switch 表达式（新写法）  
var isPrimary = switch (color) {  
  Color.red || Color.yellow || Color.blue => true,  
  _ => false,  
};  
​  
// 传统 switch 语句（老写法）  
var isPrimary;  
switch (color) {  
  case Color.red:  
  case Color.yellow:  
  case Color.blue:  
    isPrimary = true;  
    break;  
  default:  
    isPrimary = false;  
}  
```

1. **`||` 合并多个模式**：不用像传统写法那样写多个空 `case` 让它 fall-through，一行搞定。
2. **`_` 通配符**：替代 `default`，表示“剩下所有情况”。
3. **穷尽性检查**：如果 `color` 是密封类或枚举，编译器会强制你覆盖所有情况，少写一个就编译报错，非常安全。

如果需要共享一个guard，则需要：
```dart
switch (shape) {  
  case Square(size: var s) || Circle(size: var s) when s > 0:  
    print('Non-empty symmetric shape');  
}
```

[守卫子句](https://dart.dev/language/branches#guard-clause)在 case 中评估任意条件，如果条件为假，则不会退出 switch 语句（就像`if`在 case 主体中使用语句会导致的情况一样）。
```dart
switch (pair) {  
  case (int a, int b):  
    if (a > b) print('First element greater');  
  // If false, prints nothing and exits the switch.  
  case (int a, int b) when a > b:  
    // If false, prints nothing but proceeds to next case.  
    // 如果为假，则不打印任何内容，但继续进行下一个案例。  
    print('First element greater');  
  case (int a, int b):  
    print('First element not greater');  
}  
```

#### for 循环和 for-in 循环

您可以在[for 和 for-in 循环](https://dart.dev/language/loops#for-loops)中使用模式来遍历和解构集合中的值。
此示例使用 for-in 循环中的对象解构来解构` <Map>.entries` 调用返回的 MapEntry 对象：

```dart

Map<String, int> hist = {'a': 23, 'b': 100};  
​  
for (var MapEntry(key: key, value: count) in hist.entries) {  
  print('$key occurred $count times');  
}

```

该对象模式检查 `hist.entries` 是否具有名为 `MapEntry` 的类型，然后递归地遍历名为 `key` 和 `value` 的字段子模式。它在每次迭代中调用 `MapEntry` 的 `key` 和 `value` 获取器，并将结果分别绑定到局部变量 `key` 和 `count`。

将 getter 调用结果绑定到同名变量是一个常见的用例，因此对象模式也可以从变量子模式中推断出 getter 的名称。这允许您将变量模式从冗余的 `key: key` 简化为 `:key:`。

### Use cases for patterns 模式的应用案例

#### 解构多个返回

解构函数的返回值
```dart
// before  
var info = userInfo(json);  
var name = info.$1;  
var age = info.$2;  
​  
// now  
var (name, age) = userInfo(json);
```

解构具有命名字段的记录：
```dart
getData() => (name: 'gx', age: 26);  
​  
final (:name, :age) = getData();  // 隐式变量声明, 等同于 final (name: name, age: age)
```



#### 解构类的实例

```dart
final Foo myFoo = Foo(one: 'one', two: 2);  
var Foo(:one, :two) = myFoo;  
print('one $one, two $two');
```


#### 代数数据类型 ADT

```dart
sealed class Shape {}  
​  
class Square implements Shape {  
  final double length;  
  Square(this.length);  
}  
​  
class Circle implements Shape {  
  final double radius;  
  Circle(this.radius);  
}  
​  
// 新写法（Dart 3，简洁且有穷尽性保证）  
double calculateArea(Shape shape) => switch (shape) {  
  Square(length: var l) => l * l,  
  Circle(radius: var r) => math.pi * r * r,  
};  
​  
// 传统写法（不检查穷尽性，需要手动写 default）  
double calculateArea(Shape shape) {  
  if (shape is Square) {  
    return shape.length * shape.length;  
  } else if (shape is Circle) {  
    return math.pi * shape.radius * shape.radius;  
  } else {  
    throw Exception('未知形状');  // 永远不知道是否漏掉了什么  
  }  
}     
```


​
#### 验证传入的 JSON

[Map](https://dart.dev/language/pattern-types#map)和[List](https://dart.dev/language/pattern-types#list)模式非常适合解构反序列化数据中的键值对，例如从 JSON 解析的数据：

```dart
var data = {  
  'user': ['Lily', 13],  
};  
var {'user': [name, age]} = data;  
​
```

如果你确定 JSON 数据的结构符合预期，那么前面的例子是合理的。但数据通常来自外部来源，例如网络传输。你需要先验证数据结构。

如果没有模式，验证过程会非常冗长：

```dart
if (data is Map<String, Object?> &&  
    data.length == 1 &&  
    data.containsKey('user')) {  
  var user = data['user'];  
  if (user is List<Object> &&  
      user.length == 2 &&  
      user[0] is String &&  
      user[1] is int) {  
    var name = user[0] as String;  
    var age = user[1] as int;  
    print('User $name is $age years old.');  
  }  
}  
```

单个[case 模式](https://dart.dev/language/patterns#switch-statements-and-expressions) 即可实现相同的验证。单个 case 模式最适合用作[if-case](https://dart.dev/language/branches#if-case)语句。模式提供了一种更具声明性、更简洁的 JSON 验证方法：
```dart
if (data case {'user': [String name, int age]}) {  
    print('User $name is $age years old.')  
}
```


此case模式同时验证了以下几点：

- json 是一个映射（map），因为它必须首先匹配外部映射模式才能继续。
- 而且，由于它是一个映射，它也确认了 json 不为空。
- json 包含一个键 user。
- user 键与一个包含两个值的列表配对。
- 列表值的类型分别为 String 和 int。
- 用于保存这些值的新局部变量是 name 和 age。

## Pattern Type

Pattern 优先级，从高到低列出了模式类型：

- 逻辑or模式的优先级低于逻辑and，逻辑and模式优先级低于关系型模式依次类推。
- 后缀一元模式（强制类型转换、空检查和空断言）具有相同的优先级。
- 其他主模式共享最高的优先级。集合类型（记录、列表和映射）和对象模式包含其他数据，因此首先作为外部模式进行评估。

### 逻辑或

subpattern1 || subpattern2

逻辑或模式用 `||` 分隔子模式，如果任何分支匹配，则匹配成功。分支从左到右进行评估。一旦一个分支匹配，其余分支将不会被评估。
```dart
var isPrimary = switch (color) {  
  Color.red || Color.yellow || Color.blue => true,  
  _ => false  
};
```
逻辑或模式中的子模式可以绑定变量，但分支必须定义相同的变量集，因为当模式匹配时，只有一个分支会被评估。

### 逻辑与

subpattern1 && subpattern2

只有当两个子模式都匹配时，用 `&&` 分隔的一对模式才匹配。如果左分支不匹配，则右分支不会被评估。

逻辑与模式中的子模式可以绑定变量，但每个子模式中的变量不得重叠，因为如果模式匹配，它们都会被绑定：

```dart
switch ((1, 2)) {  
  // 错误，两个子模式都尝试绑定 'b'。  
  case (var a, var b) && (var b, var c): // ...  
}
```
### 关系

== expression  
< expression

关系模式使用任何相等或关系运算符（ `==` 、 `!=` 、 `<` 、 `>` 、 `<=` 和 `>=` ）将匹配的值与给定常量进行比较。

当使用常量作为参数对匹配的值调用相应的运算符返回 `true` 时，模式匹配成功。

关系模式对于匹配数值范围非常有用，尤其是在与 [逻辑与模式](https://dart.wendang.dev/language/pattern-types/#logical-and) 结合使用时：

```dart
String asciiCharType(int char) {  
    const space = 32;  
    const zero = 48;  
    const nine = 57;  
      
    return switch (char) {  
      < space => 'control',  
      == space => 'space',  
      > space && < zero => 'punctuation',  
      >= zero && <= nine => 'digit',  
      _ => ''  
    }  
}
```

# 函数

Effective Dart 建议对公共 API 使用 [类型注解](https://dart.wendang.dev/effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious) ，但省略类型后函数仍然可以工作：
```dart
isNoble(atomicNumber) {  
    return _nobleGases[atomicNumber] != null;  
}
```

对于仅包含一个表达式的函数，可以使用简写语法（箭头语法）：
```dart
bool isNobel(int atomicNumber) => _nobleGases != null;
```
=> expr 语法，其中 箭头 (`=>`) 和分号 (`;`) 之间只能出现 _表达式_ 。

## 参数

函数可以具有任意数量的 _必需位置_ 参数。这些参数之后可以是 _命名_ 参数或 _可选位置_ 参数（但不能同时是两者）。

### 命名参数

命名参数是可选的，除非它们明确标记为 _required_

定义函数时，使用 `{param1, param2, …}` 指定命名参数。如果您不提供默认值或将命名参数标记为 `required` ，则它们的类型必须是可空的，因为它们的默认值为 `null` ：

```dart
// {...} 指定命名参数  
void enableFlags({bool? bold, bool? hidden}) {...}  
// 调用函数, 使用 paramName: value  
enableFlags(bold: true, hidden: false);
```

要定义除 `null` 之外的命名参数的默认值，请使用 `=` 指定默认值。指定的值必须是编译时常量。

标记为 `required` 的参数仍然可以为空：
```dart
const Scrollbar({super.key, required Widget? child});
```

您可能希望先放置位置参数，但 Dart 不要求这样做。当适合您的 API 时，Dart 允许命名参数放在参数列表中的任何位置：

### 可选位置参数

将一组函数参数括在 `[]` 中将其标记为可选位置参数。如果您不提供默认值，则它们的类型必须是可空的，因为它们的默认值为 `null` ：
```dart
String say(String from, String msg, [String? device]) {  
    ....  
}
```

### 总结

函数形参中被`{}`或`[]`包裹着的，就是可选参数。

|语法|类型|调用方式|
|---|---|---|
|`{ }`|**可选命名参数**|传参时必须带参数名|
|`[ ]`|**可选位置参数**|传参时按顺序，不写名字|

**不能混合使用 `[]` 和 `{}`**，只能选其一。

## 函数类型

函数类型通过关键字 Function 替换函数名称从函数声明投获得的。
```dart
void greet(String name, {String greeting = 'Hello'}) =>  
    print("$greeting $name!");  
​  
void Function(String, {String greeting}) g = greet;  
g('Dash', greeting: 'xxx');
```
## 匿名函数

可以创建没有名称的函数，这被称为匿名函数、lambda或闭包。

## 词法闭包

可以在函数位于其词法作用域之外时访问其词法作用域中的变量的函数对象称为 _闭包_

函数可以闭包在其周围作用域中定义的变量：

```dart
// 返回一个函数，该函数将 [addBy] 添加到函数的参数中。  
// 无论返回的函数去哪里，它都会记住 addBy   
Function makeAdder(int addBy) {  
  return (int i) => addBy + i;  
}  
​  
void main() {  
  // 创建一个添加 2 的函数。  
  var add2 = makeAdder(2);  
​  
  // 创建一个添加 4 的函数。  
  var add4 = makeAdder(4);  
​  
  assert(add2(3) == 5);  
  assert(add4(3) == 7);  
}
```

## tear-off 函数撕裂

当写出一个不带括号的函数名时，Dart 会'撕下'这个函数本身，作为一个对象传递，而不是调用它。

```dart
void greet() {  
    print('Hello');  
}  
​  
void main() {  
    var f = greet;  // 没有括号，这是 tear-off, 拿到函数本身  
    // var f = greet();  // 这是调用 greet，并把返回值赋给 f（void）  
    f();  
}
```

## 返回值

所有函数都返回一个值，如果没有指定返回值，则语句 return null ；会隐式附加到函数体中。

要在函数中返回多个和值，请在记录中聚合这些值。
```dart
(String, int) foo() {  
    return ('something', 42);  
}
```

## 生成器

如果需要惰性的生成一系列值，请考虑使用_生成器函数_，Dart内置支持两种生成器函数:

- 同步生成器：返回一个 Iterable 对象。
- 异步生成器：返回一个 Stream 对象。

要实现 **同步** 生成器函数，请将函数体标记为 `sync*` ，并使用 `yield` 语句来传递值：

```dart
Iterable<int> naturalsTo(int n) sync* {  
  int k = 0;  
  while (k < n) yield k++;  
}

要实现 **异步** 生成器函数，请将函数体标记为 `async*` ，并使用 `yield` 语句来传递值：

Stream<int> asynchronousNaturalsTo(int n) async* {  
  int k = 0;  
  while (k < n) yield k++;  
}

如果您的生成器是递归的，您可以使用 `yield*` 来提高其性能：

Iterable<int> naturalsDownFrom(int n) sync* {  
  if (n > 0) {  
    yield n;  
    yield* naturalsDownFrom(n - 1);  
  }  
}
```

## 外部函数

外部函数是一个函数，其主题与其声明分开实现。在函数声明之前包含 external 关键字：
```dart
external void someFunc(int i);
```

外部函数的实现可以来自另一个 Dart 库，或者更常见的是来自另一种语言。

# 控制流

## 错误处理

### 异常

Dart 代码可以抛出或捕获异常，如果异常没有被捕获，则引发异常的隔离区将被挂起，并且通常隔离区及其程序将被终止。

#### 抛出
```dart
throw 'xxxx';
```

因为抛出异常是一个表达式，所以你可以在 => 语句中抛出异常，以及在允许表达式的任何其他地方：
#### 捕获

捕获异常会阻止异常传播（除非你重新抛出异常）。捕获异常使你有机会处理它：
```dart
try {  
  breedMoreLlamas();  
} on OutOfLlamasException {  
  buyMoreLlamas();  
}
```
#### finally

要确保某些代码无论是否抛出异常都会运行，请使用 `finally` 子句。如果没有 `catch` 子句与异常匹配，则在 `finally` 子句运行后传播异常：
```dart
try {  
  breedMoreLlamas();  
} finally {  
  // 始终清理，即使抛出异常。  
  cleanLlamaStalls();  
}
```


### 断言

在开发过程中，使用断言语句—— `assert(<condition>, <optionalMessage>);` ——如果布尔条件为假，则中断正常执行。

`assert` 的第一个参数可以是任何解析为布尔值的表达式。如果表达式的值为真，则断言成功，执行继续。如果为假，则断言失败并抛出异常（一个 [`AssertionError`](https://dart.wendang.dev/dart-api/dart-core/AssertionError-class.html) ）。

断言究竟何时起作用？这取决于你使用的工具和框架：

- Flutter 在 [调试模式](https://dart.wendang.dev/flutter-docs/testing/debugging#debug-mode-assertions) 下启用断言。
- 仅限开发的工具，例如 [`webdev serve`](https://dart.wendang.dev/tools/webdev#serve) ，通常默认启用断言。
- 一些工具，例如 [`dart run`](https://dart.wendang.dev/tools/dart-run) 和 [`dart compile js`](https://dart.wendang.dev/tools/dart-compile#js) ，通过命令行标志支持断言： `--enable-asserts` 。

在生产代码中，断言被忽略，并且不会评估 `assert` 的参数。


# 类与对象


## 类

### 简介

#### 实例变量

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



#### 隐式接口

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

在构造函数体运行之前，可以初始化实例变量，用逗号分隔初始化程序。

```dart
Point.fromJson(Map<String, double> json)
	: x = json['x']!,  // ! 是空段言运算符, 表示我确定 json['x'] 的值不是 null
	  y = json['y']! {
		  print('In Point.fromJson():($x, $y)');
	}
```

初始化列表有助于设置 final 字段。


### 构造函数继承

子类不会从它的超类或父类继承构造函数。如果一个类没有声明构造函数，它只能使用默认构造函数。
一个类可以继承超类的参数，这些被称为==超参数。

构造函数的工作方式与调用静态方法链的方式有点类似。 每个子类都可以调用其超类的构造函数来初始化实例， 就像子类可以调用超类的静态方法一样。 此过程不会“继承”构造函数体或签名。

#### 非默认超类构造函数

Dart 按照以下顺序执行构造函数：
1. 初始化列表
2. 超类的无名、无参构造函数
3. 主类的无参构造函数

如果超类缺少无名、无参数的构造函数，则调用超类中的一个构造函数。在构造函数体之前，在冒号后指定超类构造函数：
```dart
class Person {
	String? firstName;
	Person.fromJson(Map data) {
		print('in Person');
	}
}
class Employee extends Person {
	// Person 没有默认构造函数
	// 必须调用 super.fromJson()
	Employee.fromJson(Map data) : super.fromJson(data) {
		print('in Employee');
	}
}
```

#### 超参数

超参数是Dart3引入的一个语法糖，让你在子类构造函数中可以直接用 `super.xxx` 吧参数透传给父类构造函数，不用再手写一遍赋值：
```dart
class Animal {
	final String name;
	final int age;
	Animal(this.name, this.age);
}

// ❌ 老写法：子类必须把参数再声明一遍，然后手动传给 super
class Dog extends Animal {
	Dog(String name, int age) : super(name, age);
}

// ✅ 超参数写法：直接在参数列表里用 super.xxx 透传
class Dog extends Animal {
	Dog(super.name, super,age);
}
```

子类可以有自己的参数，可以同时把部分参数透传给父类。


### 方法
> 方法是为对象提供行为的函数

#### 实例方法


#### 运算符
大多数运算符都是具有特殊名称的实例方法。Dart 允许您使用以下名称定义运算符：

| `<`   | `>`  | `<=` | `>=` | `==`  | `~`  |
| ----- | ---- | ---- | ---- | ----- | ---- |
| `-`   | `+`  | `/`  | `~/` | `*`   | `%`  |
| `\|`  | `ˆ`  | `&`  | `<<` | `>>>` | `>>` |
| `[]=` | `[]` |      |      |       |      |
要声明运算符，请使用内置标识符 `operator` ，然后是您要定义的运算符。以下示例定义了向量加法（ `+` ）、减法（ `-` ）和相等性（ `==` ）：
```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```

#### Getter 和 Setter

Dart 中控制对象属性的访问，核心是每个实例变量自带隐式的 getter 和 setter，你也可以用 `get` 和 `set` 关键字自定义这种读写行为。

显式 getter 和 setter：
```dart
class Rectangle {
	double width, height;
	Rectangle(this.width, this.height);
	
	// 显式 getter: 计算属性，看起来像属性，实际是方法
	double get area => width * height;
	
	// 显式 setter: 设置时做验证
	set safeWidth(double value) {
		if (value > 0) {
			width = value;
		}
	}
}

void main() {
  var r = Rectangle(3, 4);
  print(r.area);      // 12  （用起来像读属性，实际调用了 area getter）
  
  r.safeWidth = -5;   // 无效，被 setter 拦截了
  print(r.width);     // 3
}
```


#### 抽象方法

实例、Getter 和 Setter 方法可以是抽象，定义一个接口，但将其实现留给其他类。抽象方法只能存在于 抽象类 和 mixin 中，要使方法成为抽象方法，请使用分号（ `;` ）而不是方法体：
```dart
abstract class Doer {
  // 定义实例变量和方法...

  void doSomething(); // 定义一个抽象方法。
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 提供实现，因此此处方法不是抽象的...
  }
}
```

### 扩展类

使用 `extends` 创建子类，使用 `super` 引用超类：
```dart
c
```

#### 重写成员

子类可以重写实例方法（包括运算符）、getter 和 setter。可以使用 `@override` 注解来指示你有意重写成员：
```dart
class Television {
	set contrast(int value) {...}
}

class SmartTelevision extends Television {
	@override
	set contrast(num value) {...}
}
```

重写方法声明必须在以下几个方面与它重写的 method（或 methods）匹配：

- 返回类型必须与重写方法的返回类型相同（或为其子类型）。
- 参数类型必须与重写方法的参数类型相同（或为其超类型）。在前面的示例中， `SmartTelevision` 的 `contrast` setter 将参数类型从 `int` 更改为超类型 `num` 。
- 如果重写的方法接受 _n_ 个位置参数，则重写的方法也必须接受 _n_ 个位置参数。
- [泛型方法](https://dart.wendang.dev/language/generics#using-generic-methods) 不能重写非泛型方法，非泛型方法也不能重写泛型方法。


#### noSuchMethod()

`noSuchMethod()` 是 Dart 中一个特殊的方法，当你==调用一个不存在的方法或访问一个不存在的属性时==，他会被自动触发。

正常情况下，调用不存在的方法会直接抛出异常：
```dart
class Person {
	String name = 'Alice';
}

void main() {
	var p = Person();
	p.greet();  // ❌ NoSuchMethodError: Class 'Person' has no instance method 'greet'
}
```

重写 `noSuchMethod()` 来拦截：
可以重写这个方法，把调用不存在的方法变成你想要的行为。
```dart
class Person {
  String name = 'Alice';
  
  // 重写 noSuchMethod，拦截所有不存在的调用
  @override
  dynamic noSuchMethod(Invocation invocation) {
    print('你调用了不存在的方法: ${invocation.memberName}');
    print('参数是: ${invocation.positionalArguments}');
    return null; // 不会崩溃了
  }
}

void main() {
  var p = Person();
  p.greet();           // 打印信息，不会崩溃
  p.sayHello('Bob');   // 同样被拦截
}
```


重写 `noSuchMethod` 的类不能用 `extends` 继承一个具体类（除非那个类也支持动态调用）。通常配合`implements` 接口或 `extends` 一个标记类使用。
```dart
class DynamicClass {
  @override
  dynamic noSuchMethod(Invocation invocation) => super.noSuchMethod(invocation);
}

// 继承 DynamicClass，获得动态能力
class MyProxy extends DynamicClass {
  // 不需要声明任何方法，调用任何方法都会走 noSuchMethod
}
```


### Mixin

Mixin 是 Dart 中一种特殊的类，它的作用是在==不通过继承==的情况，为多个不相关的类==复用代码。

简单来说：`extends` 是 **"是什么"** 的关系，而 Mixin 是 **“能干什么”** 的能力。



#### Mixin vs 继承 vs 接口

|特性|`extends`（继承）|`implements`（接口）|`with`（Mixin）|
|---|---|---|---|
|关系|**是**什么|**承诺**实现什么|**能**做什么|
|复用代码|✅ 继承父类实现|❌ 必须全部重写|✅ 直接复用方法|
|数量限制|只能 1 个|可以多个|可以多个|
|例子|`Dog extends Animal`|`Dog implements Pet`|`Dog with Flyer, Swimmer`|


#### 在 mixin 中定义抽象成员

在 mixin 中声明抽象方法会强制使用该 mixin 的任何类型都必须定义此抽象方法
```dart
mixin Musician {
  void playInstrument(String instrumentName); // 抽象方法。

  void playPiano() {
    playInstrument('Piano');
  }
  void playFlute() {
    playInstrument('Flute');
  }
}

class Virtuoso with Musician {

  @override
  void playInstrument(String instrumentName) { // 子类必须定义。
    print('Plays the $instrumentName beautifully');
  }  
}
```


声明抽象成员还可以通过调用在 mixin 上定义为抽象的 getter 来访问 mixin 子类上的状态：

#### mixin 不能有构造函数



#### 用 on 限制使用范围


#### `class` 、 `mixin` 或 `mixin class` ？


### 枚举类型

枚举类型，是一种特殊的类，用于表示固定数量的常量。

#### 声明简单的枚举

```dart
enum Color { red, green, blue }
```

#### 声明增强的枚举

Dart 还允许枚举声明具有==字段、方法和常量构造函数==的类，这些类仅限于固定数量的已知常量实例。

要声明增强的枚举，请遵循类似于普通类的语法，但有一些额外的要求：

- 实例变量必须是 `final` ，包括由 [mixins](https://dart.wendang.dev/language/mixins) 添加的那些。
- 所有 [生成式构造函数](https://dart.wendang.dev/language/constructors#constant-constructors) 必须是常量。
- [工厂构造函数](https://dart.wendang.dev/language/constructors#factory-constructors) 只能返回一个已知的固定枚举实例。
- 不能扩展其他类，因为[`Enum`](https://api.dart.dev/dart-core/Enum-class.html)会自动扩展。
- 不能覆盖 `index` 、 `hashCode` 、相等运算符 `==` 。
- 枚举中不能声明名为 `values` 的成员，因为它会与自动生成的静态 `values` getter冲突。
- 枚举的所有实例都必须在声明的开头声明，并且必须至少声明一个实例。
```dart
enum Vehicle implements Comparable<Vehicle> {
  car(tires: 4, passengers: 5, carbonPerKilometer: 400),
  bus(tires: 6, passengers: 50, carbonPerKilometer: 800),
  bicycle(tires: 2, passengers: 1, carbonPerKilometer: 0);

  const Vehicle({
    required this.tires,
    required this.passengers,
    required this.carbonPerKilometer,
  });

  final int tires;
  final int passengers;
  final int carbonPerKilometer;

  int get carbonFootprint => (carbonPerKilometer / passengers).round();

  bool get isTwoWheeled => this == Vehicle.bicycle;

  @override
  int compareTo(Vehicle other) => carbonFootprint - other.carbonFootprint;
}
```

#### 使用枚举

要获取所有枚举值的列表，请使用枚举的 values 常量：
```dart
List<Color> colors = Color.values;
```
需要访问枚举值的名称，可以使用 .name 属性：
```dart
print(Color.blue.name);
```


### 可调用对象

要允许你的 Dart 类的一个实例像函数一样被调用，请实现 call() 方法。call 方法允许类的实例模拟函数：
```dart
class WannabeFunction {
	String call(String a, String b, String c) => '$a $b $c!';
}

var wf = WannabeFunction();
var out = wf('Hi', 'there', 'gang');

void main() => print(out);
```


# 类修饰符

修饰符关键字位于类或混入声明之前。例如，编写 `abstract class` 定义了一个抽象类。可以在类声明前出现的完整修饰符集包括：

- `abstract`
- `base`
- `final`
- `interface`
- `sealed`
- `mixin`

| 关键字         | 核心作用           | 能 `extends` 吗 | 能 `implements` 吗 | 能 `with` 吗 |
| ----------- | -------------- | ------------- | ---------------- | ---------- |
| `abstract`  | 不能实例化          | ✅             | ✅                | ✅          |
| `base`      | 限定同文件外只能继承     | ✅（同文件）        | ❌                | ❌          |
| `final`     | 完全封闭，不能派生      | ❌             | ❌                | ❌          |
| `interface` | 只能被实现，不能被继承    | ❌             | ✅                | ❌          |
| `sealed`    | 子类必须同文件，用于穷尽检查 | ✅（同文件）        | ✅（同文件）           | ❌          |
|             |                |               |                  |            |


作为 API 设计者，这些新的修饰符可以让您控制用户如何使用您的代码，反过来，您也可以在不破坏用户代码的情况下改进代码。

但是这些选项也带来了复杂性：您现在作为 API 设计者需要做出更多选择。此外，由于这些功能是新的，我们仍然不知道最佳实践是什么。每种语言的生态系统都不同，需求也不同。

幸运的是，您不需要一次性解决所有问题。我们故意选择了默认值，因此即使您什么都不做，您的类也大多具有与 3.0 之前相同的特性。如果您只想保持 API 的原样，请在已经支持该功能的类上添加 `mixin` ，然后就完成了。

随着时间的推移，当您了解到需要更精细的控制的地方时，您可以考虑应用其他一些修饰符：

- 使用 `interface` 来阻止用户重用类的代码，同时允许他们重新实现其接口。
    
- 使用 `base` 来要求用户重用类的代码，并确保类的每个实例都是该实际类或子类的实例。
    
- 使用 `final` 来完全阻止扩展类。
    
- 使用 `sealed` 来选择加入对一系列子类型的穷举检查。
    

当您这样做时，发布包时请递增主要版本号，因为这些修饰符都暗示着构成重大更改的限制。



# 并发

>Dart 中的并发编程指的是异步 API （如 Future 和 Stream）和 *隔离区*。所有 Dart 代码都在隔离区中运行，从默认的主隔离区开始，并根据需要扩展到你显式创建的任何后续隔离区。当您生成一个新的隔离区时，它拥有自己独立的内存和自己的**事件循环**。事件循环使 Dart 中的异步和并发编程成为可能。

## 事件循环

Dart 的运行时模型基于事件循环。事件循环负责执行程序的代码、收集和处理事件等等。

当您的应用程序运行时，所有事件都会添加到一个称为 **_事件队列_** 的队列中。事件可以是任何内容，从重绘 UI 的请求，到用户的点击和按键，再到磁盘的 I/O。因为您的应用程序无法预测事件发生的顺序，所以事件循环一次处理一个事件，按照它们排队的顺序处理事件。

![[Pasted image 20260728183242.png|697]]
事件循环的功能类似于以下代码：
```dart
while (eventQueue.waitForEvent()) {
	eventQueue.processNextEvent();
}
```

此示例事件循环是同步的，并在单个线程上运行。但是，大多数 Dart 应用程序需要一次执行多项操作。例如，==客户端应用程序可能需要执行 HTTP 请求，同时还要侦听用户点击按钮==。为了处理这个问题，Dart 提供了许多异步 API，例如 [Future、Stream 和 async-await](https://dart.wendang.dev/language/async) 。这些 API 是围绕此事件循环构建的。

例如，考虑进行网络请求：
```dart
http.get('https://example.com').then((response){
	if (response.statusCode == 200) {
		print('Success!');
	}
})
```

当此代码到达事件循环时，它会立即调用第一个子句 `http.get` 并返回一个 `Future` 。它还会告诉事件循环保留 `then()` 子句中的回调，直到 HTTP 请求解析。发生这种情况时，它应该执行该回调，并将请求的结果作为参数传递。

![[Pasted image 20260728184600.png]]

Dart 中**异步操作从开始到结束的完整执行流程**：

可以把它拆解为两个关键节点来理解，就像你在店里打包了一份需要慢火炖的“佛跳墙”：

1. **立即执行与返回 Future** ("你先下单，给你小票")：
	它会立即调用第一个子句 `http.get` 并返回一个 `Future`。
	- **这不是排队等待**：`http.get` 是同步发起的。你的代码会立刻、马上开始网络请求，不会卡住。
	- **返回值是“凭证”**：它立刻返回一个 `Future` 对象。这个 `Future` 就是一个承诺，表示“结果未来会到”，代码因此能继续向下执行，不会被阻塞。
2. **注册回调与事件循环**（“菜好了自然会叫你”）
	 告诉事件循环保留 `then()` 子句中的回调，直到 HTTP 请求解析。
	1.  **不会傻等**：拿到 `Future` 凭证后，代码不会停下来等响应，而是立刻继续往下走。
	2. **任务被挂起**：你写在 `.then()` 里的代码会被包装成一个“回调任务”，告诉事件循环：“先去忙别的，等网络数据回来了，再把我这个任务排进队列里执行。”
	3. **事件循环的职责**：它的内部就像一个永不停歇的待办清单，不断处理各种任务。当网络数据到达，“执行回调”的待办事项就会被它处理。

简单说，这就像点完菜出去逛街：
1. **http.get** → 下单并付款（立即），拿到取餐呼叫器（**Future**）。
2. **.then()** → 看着取餐器，响了就取餐（**注册回调**）。
3. **事件循环** → 你逛街的过程中，不断检查取餐器，确保餐好了你能第一时间去取。


此相同的模型通常是事件循环如何处理 Dart 中所有其他异步事件的方式，例如 [`Stream`](https://api.dart.dev/dart-async/Stream-class.html) 对象。


## 异步编程

### Futures
Futures 表示异步操作的结果，该操作最终将完成一个值或一个错误。
在此示例代码中，`Future<String>` 的返回类修表示最终提供 String 值或错误的承诺。
```dart
Future<String> _readFileAsync(String filename) {
  final file = File(filename);

  // .readAsString() 返回一个 Future。
  // .then() 注册一个回调，当 `readAsString` 解析时执行。
  return file.readAsString().then((contents) {
    return contents.trim();
  });
}
```

### async-await
async 和 await 关键字提供了一种声明式方法来定义异步函数并使用其结果。
这是一个同步代码示例，它在等待文件 I/O 时阻塞：
```dart
const String filename = 'with_keys.json';

void main() async {
  // 读取一些数据。
  final fileData = await _readFileAsync();
  final jsonData = jsonDecode(fileData);

  // 使用这些数据。
  print('JSON 密钥数量：${jsonData.length}');
}

Future<String> _readFileAsync() async {
  final file = File(filename);
  final contents = await file.readAsString();
  return contents.trim();
}
```

关键语法点：
- `async` 标记的函数会返回一个 Future，await 则用于等待这个 Future 的结果。这样写比用 `.then()` 链式调用更易读。
- `await` 标记的地方，可以暂停，将工作权移交出去（移交给事件循环），让程序先能处理其他任务。
- `await` 关键字仅适用于函数体前面有 `async` 的函数。

如下图所示，Dart 代码在 `readAsString()` 执行非 Dart 代码（在 Dart 运行时或操作系统中）时暂停。一旦 `readAsString()` 返回一个值，Dart 代码执行就会恢复。

![[Pasted image 20260728193752.png]]




### Streams

Dart 还支持流形式的异步代码。流在未来和重复地随时间提供值。承诺随着时间的推移提供一系列 `int` 值的类型为 `Stream<int>` 。

在下面的示例中，使用 `Stream.periodic` 创建的流每秒重复发出一个新的 `int` 值。

```dart
Stream<int> stream = Stream.periodic(const Duration(seconds: 1), (i) => i * i);
```


异步生成器函数：await-for 和 yield

- **`async`**：普通异步函数，返回一个 `Future`，只能返回**一个**最终结果。
- **`async*`**：异步生成器函数，返回一个 `Stream`，可以在不同时间点**多次**产出值。

`async*` 赋予了函数一种“暂停并产出”的能力，每次 `yield` 都会往流里发送一个值，然后函数暂停，等下一次需要时再继续执行。

```dart
Stream<int> sumStream(Stream<int> stream) async* {
  var sum = 0;                         // 1. 初始化累加器
  await for (final value in stream) {  // 2. 监听输入的 stream
    yield sum += value;                // 3. 累加，并把当前总和发送出去
  }
}
```

1. **`await for (final value in stream)`**：**异步 for 循环**。它不是一个普通的 `for`，每接收一个 `value` 都会先 `await` 等数据。每当输入流 `stream` 里有一个新值，循环就执行一次。
2. **`yield sum += value`**：`yield` 是生成器的关键。它先把 `value` 加到 `sum` 上，然后把这个**当前的累加和**发送到输出流里。函数接着暂停，等待下一个来自输入流的值。

假设这么调用（输入流按顺序发出 `1, 2, 3`）：

```dart
void main() async {
  var inputStream = Stream.fromIterable([1, 2, 3]);
  var outputStream = sumStream(inputStream);
  
  await for (final sum in outputStream) {
    print(sum);
  }
}
```
每次 `yield` 产出一个和，外部就能通过 `await for` 立刻收到并处理它。

| 关键字组合        | 返回类型            | 产出值的方式      | 使用场景           |
| ------------ | --------------- | ----------- | -------------- |
| `async`      | `Future<T>`     | `return`    | 单次异步操作（如网络请求）  |
| `sync*`      | `Iterable<T>`   | `yield`     | 同步惰性序列（如自定义列表） |
| **`async*`** | **`Stream<T>`** | **`yield`** | **异步数据流（如这里）** |

#### 两种流

**单订阅流：**

最常见的流类型包含一系列事件，这些事件是更大整体的一部分。事件需要按正确的顺序传递，并且不能缺少任何事件。这是读取文件或接收 Web 请求时获得的流类型。

==这种流只能监听一次==。稍后再次监听可能会错过初始事件，然后流的其余部分就没有任何意义了。当你开始监听时，数据将被获取并分块提供。

**广播流：**
另一种流类型用于可以逐个处理的单个消息。例如，这种流可以用于浏览器中的鼠标事件。

你可以随时开始监听这种流，并且你会收到在你监听时触发的事件。多个监听器可以同时监听，你可以在取消之前的订阅后稍后再次监听。



## 隔离区
