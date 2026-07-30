
MainApp 是根组件，因为它是传递给 runApp 的组件。在这个组件内部，有一个 build 方法，它会返回另一个名为 MaterialApp 的组件。本质上，这就是 Flutter 应用的本质：由多个组件构成，形成一个称为组件树的树状结构。

作为 Flutter 开发者，你的任务是将 SDK 中的组件组合成更大的、自定义的组件，从而显示用户界面。


## 基础组件库










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