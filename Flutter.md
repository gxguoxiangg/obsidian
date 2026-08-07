
MainApp 是根组件，因为它是传递给 runApp 的组件。在这个组件内部，有一个 build 方法，它会返回另一个名为 MaterialApp 的组件。本质上，这就是 Flutter 应用的本质：由多个组件构成，形成一个称为组件树的树状结构。

作为 Flutter 开发者，你的任务是将 SDK 中的组件组合成更大的、自定义的组件，从而显示用户界面。



## 基础组件库



```text
  ┌─────────────────────────────┐
  │          Margin             │  ← 盒子外面，和其他物体的距离
  │   ┌───────────────────┐     │
  │   │      Padding      │     │  ← 盒子里面，内容和边框的距离
  │   │   ┌─────────┐    │     │
  │   │   │  内容   │    │     │
  │   │   └─────────┘    │     │
  │   └───────────────────┘     │
  └─────────────────────────────┘
```

```dart
Container(
  margin: EdgeInsets.all(20),   // 外部：离其他组件 20 像素
  padding: EdgeInsets.all(10),  // 内部：子组件离边框 10 像素
  color: Colors.blue,
  child: Text('Hello'),
)
```

**效果**：
- `margin: 20`：这个蓝色盒子和其他组件之间会空出 20 像素。
- `padding: 10`：文字 "Hello" 不会紧贴蓝色盒子的边框，四周有 10 像素的留白。

| 属性          | 作用位置            | 视觉效果                | 是否影响背景色             |
| ----------- | --------------- | ------------------- | ------------------- |
| **Margin**  | Widget **边框之外** | 把当前组件**推开**，离别的组件远点 | ❌ 背景色不延伸到 Margin 区域 |
| **Padding** | Widget **边框之内** | 把组件**内部内容**挤向中间     | ✅ 背景色会填充 Padding 区域 |

### 支持 const 的 Widget

| 支持 `const`                                                                                            | 不支持 `const`                                                                      |
| ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `Text`, `Icon`, `SizedBox`, `Padding`, `Center`, `Align`, `Expanded`, `Flexible`, `Divider`, `Spacer` | `Container`, `Scaffold`, `ListView`, `Column`, `Row`, `Stack`, `Image.network` 等 |






Column
Listview：滚动组件，比如 Column 和 Row 中的子项过大则会显示不全，而滚动组件可以让所有子项都能够滚动显示。如果[API说明](https://api.flutter-io.cn/flutter/widgets/ListView-class.html)。





### AlertDialog 类









## 无状态组件


## 有状态组件

当一个 Widget 的==外观或数据需要在其生命周期内发生变化时==，你需要一个 *StatefulWidget* 和一个与之配套的 State 对象。虽然 *StatefulWidget* 本身是不可变的（其属性在创建后无法更改），但 State 对象是长期存在的，可以保存可变数据，并且可以在数据更改时重建，从而更新 UI。

最基本的 *StatefulWidget* ：
```dart
class ExampleWidget extends StatefulWidget {
	ExampleWidget({super.key});
	
	@override
	State<ExampleWidget> createState() => _ExampleWidgetState();
}

class _ExampleWidgetState extends State<ExampleWidget> {
	@override
	Widget build(BuildContext context) {
		return Container();
	}	
}

```






## 规范
### 设计模式

#### MVVM，Model-View-ViewModel
客户端应用的架构模式，它将应用程序分为三层：
- Model 数据模型：负责数据和业务逻辑。它不关心界面张什么样，只定义数据结构、处理数据存取。
- View 视图：负责 UI 展示，被动形式，负责根据 ViewModel 提供的数据画出界面，并把用户操作转发出去。
- ViewModel 视图模型：管理状态并将 View 和 Model 连接起来，它持有 Model，处理展示逻辑和用户交互，将 Model 的数据转换为 View 可以直接用的格式，并暴露给 View 进行监听。





### 性能

#### 在构造函数中使用 const 来提高性能

在你创建 Widget 时，如果它的所有参数在编译时就能确定，就在它前面加上 const，这样就提升 Flutter 的性能。
```dart
// ❌ 原来：不加 const
Padding(
  padding: EdgeInsets.all(8.0),
  child: Text('Hello'),
)

// ✅ 改后：加上 const
const Padding(
  padding: EdgeInsets.all(8.0),
  child: Text('Hello'),
)
```

`const` 对象在编译时就被创建并**规范化**了。意思是：不管你的 Widget 树 rebuild 多少次，只要配置相同，这个 `const` Widget **在内存中永远是同一个实例**。

- **普通对象**：每次 `build()` 都重新创建 → 每次都要销毁旧对象、分配新内存
- **`const` 对象**：编译时创建一次，之后永远复用 → 不创建、不销毁、不比较

Flutter 框架在重建 UI 时，遇到 `const` Widget 会直接跳过，因为它知道"这个不会变，不用管它"。这减少了重建开销，提升了帧率。


#### 在 @immutable 类的构造函数中使用 const 字面量作为参数

**普通字面量的隐患**：  
如果你写 `items: ['Apple', 'Banana']`，虽然看上去像常量，但它实际上是一个运行时才创建的 `List` 对象。更关键的是，**它默认是可变的**。  
虽然 `MyCustomList` 内部可能不会去修改它，但传给一个 `@immutable` 的类一个可变对象，存在被意外修改的风险。

**`const` 字面量的好处**：  
当你写成 `items: const ['Apple', 'Banana']` 时：
1. **不可变**：这个列表本身是不可修改的，完美契合了 `@immutable` 类的要求。
2. **性能**：和之前讲的一样，`const` 对象是编译时就创建好的，Widget 每次重建时都复用同一个列表实例，节省内存和重建开销。