## Weave Language
一种为 AI 优化的图布局语言，目前用于蓝图AI。

蓝图功能很多，但转义为 `K2Node` 文本时，对 `LLM` 来说是相当大的负担。  
`Weave语言` 是人和 `LLM` 都能读写蓝图的方式。

本插件旨在打通 LLM 与 蓝图之间的交互壁垒，制作 AI 与 蓝图 交互的中间件，未来将会逐步支持材质图表，动画图表，Control Rig 图表等，希望广大开发者可以一起共同开发。

### 特性
- **跨蓝图**: 你可以在不打开蓝图的情况下对蓝图进行编辑
- **可读性**：人类和 AI 都能看懂
- **双向转换**：蓝图 ↔ Weave，无损转换（更新中）
- **Patch 机制**：AI 只需说"改哪里"，不用重写整个文件
- **Schema 约束**：AI 不可能生成不存在的节点或非法连接
- **Token 高效**：比直接读取蓝图数据省 `70%以上` token



### Weave Language 语法参考

一个 `.weave` 文件包含多个声明块：
```
graphset <名称> <蓝图路径>
graph <函数名>

node ...
link ...
set ...
```

---
| 关键字 | 用途 |
|--------|------|
| `graphset` | 声明蓝图资源，这张蓝图在哪里 |
| `graph` | 声明事件图/函数，具体图表是哪个 |
| `node` | 声明节点 |
| `link` | 连接节点端点 |
| `set` | 设置节点属性值 |
---



#### 示例
##### 开始游戏后使用 Sequence 按顺序执行四个 Print String
- 此时操作的是 /Game/MyActor2 蓝图的事件图表。
```weave
graphset MyActor2 /Game/MyActor2.MyActor2
graph EventGraph

node a : event.Actor.ReceiveBeginPlay @ (-144, 16)
node b : special.Sequence @ (96, 16)
node c : call.KismetSystemLibrary.PrintString @ (384, 16)
node d : call.KismetSystemLibrary.PrintString @ (384, 160)
node e : call.KismetSystemLibrary.PrintString @ (384, 304)
node f : call.KismetSystemLibrary.PrintString @ (384, 448)

link a.then -> b.execute
link b.then_0 -> c.execute
link b.then_1 -> d.execute
link b.then_2 -> e.execute
link b.then_3 -> f.execute

set c.WorldContextObject = self
set c.InString = Print 1
set c.bPrintToScreen = true
set c.bPrintToLog = true
set c.TextColor = (R=0.000000,G=0.660000,B=1.000000,A=1.000000)
set c.Duration = 2.000000
set d.WorldContextObject = self
set d.InString = Print 2
set d.bPrintToScreen = true
set e.WorldContextObject = self
set e.InString = Print 3
set f.WorldContextObject = self
set f.InString = Print 4
```

### 如何使用？
拉取插件源代码，放到虚幻`cpp`项目的插件文件夹中，重新生成项目文件，编译后启动。
也可以下载 Release 中的发布版本直接放到项目插件文件夹中（若有）。

#### 快速测试
如果成功安装了插件，在虚幻引擎编辑器主界面上方应当有 `Weaver` 字样的下拉菜单，打开菜单后点击 `Weave 生成解释调试器` 即可打开。选中蓝图节点，点击 `Generate from Selection` 即可将选中的整个蓝图链翻译为 Weave 语言，当然点击 `Apply to Blueprint` 即可应用会对应的图表。

