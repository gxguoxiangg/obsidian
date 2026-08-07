

## 目录结构

```text
lib/
├── main.dart                     # 入口：ProviderScope + App
├── app/
│   ├── app.dart                  # 根组件（MaterialApp.router 和 主题应用）
│   └── router/app_router.dart    # go_router 路由表
├── core/
│   ├── constants/app_constants.dart   # 全局常量（API 地址、应用名称和版本号等）
│   ├── theme/app_theme.dart           # 应用主题配置
│   └── network/                       # ApiClient 封装与依赖注入
├── features/
│   ├── home/                     # 主页工具盒模块
│   │   ├── models/               # ToolItem / ToolCategory
│   │   ├── data/tool_registry.dart    # 工具注册表
│   │   ├ viewmodel/            # HomeViewModel
│   │   └── view/                 # HomePage 及子组件
│   ├── article/                  # 文章内容展示模块
│   │   ├── models/               # Article
│   │   ├── data/                 # ArticleRepository
│   │   ├── viewmodel/            # 加载 / 搜索 ViewModel
│   │   └── view/                 # ArticleListPage / ArticleCard
│   ├── settings/                 # 全局设置（占位）
│   └── about/                    # 关于 App
└── shared/
    └── widgets/
        |──app_drawer.dart        # Drawer 应用侧边栏
        |──markdown_view.dart     # Markdown 内容渲染组件

```



## shared


### widgets

#### app_drawer.dart
侧边栏里的布局和内容。

AppDrawer 继承 Stateless 类：
- 成员变量：一个 const static List，存储侧边栏菜单项
- 