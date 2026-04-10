# asset layer plan v1

## A. 文档状态与边界
- 本文档是 **future plan**，用于指导后续 asset 层拆库建设。
- 本文档 **不改变当前系统行为**。
- 本文档 **不代表当前已实现** `attrib`、`cluster`、`closure`。
- 本文档只定义方向、边界、输入输出契约，供后续实现对齐。

## B. 为什么 asset 层要拆成三个项目
asset layer 未来不作为单一大项目建设，而是拆成三个独立小项目：
- `attrib`：归因层项目
- `cluster`：聚类层项目
- `closure`：收口层项目

拆分原因：
- 不拆会导致职责混乱：归因、聚类、收口在同一模块中容易相互污染。
- 三类任务失败模式不同：
  - `attrib` 常见问题是样本解释不稳定。
  - `cluster` 常见问题是簇边界漂移与稳定性不足。
  - `closure` 常见问题是命名冲突、对象重复、版本状态不清。
- 拆开后可独立评测、独立版本化、独立迭代，降低全链路耦合成本。

## C. asset 层总流程
高保真文本特征 + 高保真 review 特征  
→ `attrib`  
→ `attribution_units`  
→ `cluster`  
→ `cluster_sets`  
→ `closure`  
→ `asset_objects + relation_models`

结果语义：
- `attribution_units`：样本级关系结果。
- `cluster_sets`：模式级聚类结果。
- `asset_objects + relation_models`：正式资产输出。

顺序约束：
- `cluster` 不负责给 `attrib` 擦屁股。
- `closure` 不负责回头清洗 `cluster` 的原始脏结果。
- 每层只消费上一层的正式输出。

## D. `attrib` 项目设计
### 定位
样本级归因项目。

### 输入
- 高保真文本特征
- 高保真 review 特征
- 对齐信息

### 核心任务
从局部样本中建立：
文本机制 → 体验机制 → 读者反应。

### 输出
- `attribution_units`

### 不负责什么
- 不负责聚类
- 不负责资产命名定稿
- 不负责对象注册
- 不负责版本治理

### LLM 适合承担什么
- 归因链提炼
- 体验机制识别
- 机制解释

### 程序更适合承担什么
- 样本 ID
- evidence refs
- 去重
- 结构校验

## E. `cluster` 项目设计
### 定位
模式级聚类项目。

### 输入
- `attribution_units`

### 核心任务
把大量 attribution units 收成稳定模式簇。

至少包含三类聚类：
- 文本机制聚类
- 体验机制聚类
- 文本-体验耦合聚类

### 输出
- `cluster_sets`

### 不负责什么
- 不回头重做归因
- 不做正式资产收口
- 不做对象注册

### LLM 适合承担什么
- 簇解释
- 命名候选
- 合并建议

### 程序更适合承担什么
- 初始聚类
- 相似度计算
- 簇稳定性控制
- 聚类结果管理

## F. `closure` 项目设计
### 定位
资产收口项目。

### 输入
- `cluster_sets`

### 核心任务
把模式簇整理成正式资产对象与关系模型。

### 输出
- `asset_objects`
- `relation_models`

### 核心动作
- 命名定型
- 去重与归并
- 对象化
- 关系建模
- 版本与状态挂载

### 不负责什么
- 不回头重做归因
- 不回头重做聚类
- 不直接处理原始文本或评论特征

### LLM 适合承担什么
- 条目定义草案
- 名称候选
- 边界说明
- 关系解释文本

### 程序更适合承担什么
- asset_id
- version
- status
- evidence count
- registry 维护

## G. 三个项目的接口契约
### `attrib` 正式输出
- `attribution_units`

### `cluster` 正式输入 / 输出
输入：
- `attribution_units`

输出：
- `cluster_sets`

### `closure` 正式输入 / 输出
输入：
- `cluster_sets`

输出：
- `asset_objects`
- `relation_models`

契约原则：
- 每层只认上一层正式输出。
- 不直接跨层吃更底层数据。

## H. 为什么这种拆法有利于可维护性、可扩展性、可读性
### 可维护性
- 单一职责
- 边界清晰
- 改一层不拖全链

### 可扩展性
- 三层可独立演进
- 可独立替换实现
- 可独立版本化

### 可读性
- 术语统一
- 输入输出明确
- 不需要靠大量隐含上下文理解系统

## I. 一句话总括
asset 层未来应拆成 `attrib`、`cluster`、`closure` 三个独立项目：前者解释样本级因果，中者归纳模式级结构，后者将模式收成正式可用 `asset_objects` 与 `relation_models`。
