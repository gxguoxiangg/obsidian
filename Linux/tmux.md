
## dotfile

**Tmux的dotfile默认在**
```bash
~/.tmux.conf    # 可以修改该文件进行自定义配置
```

tmux主要由会话，窗口，窗格（session，window，pane）组成

## 会话

显示正在运行的会话列表
```bash
tmux ls
```
连接会话
```bash
tmux a -t 0
```
创建会话时指定名称（而不是像0这样的数字名称）
```bash
tmux new -s database
```
重命名现有对话
```bahs
tmux rename-session -t old_name new_name
```

## 窗口
> 一个会话可以有多个窗口

创建新的窗口
```bash
ctrl-b c
```
切换窗口
```bash
ctrl-b <number>	# number : 窗口编号
```
切换上一个窗口
```bash
ctrl-b p
```


## 窗格

全屏显示窗格
```bash
ctrl-b z
```
调整窗格大小
```bash
ctrl-b ctrl-<arrow key>
```
重命名当前窗口
```bash
ctrl-b ,
```
将所有窗格等距排列
```bash
ctrl-b space
```


## 其他常用

进入滚动模式（复制模式）：
```
Ctrl-b [
```