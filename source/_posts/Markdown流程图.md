---
title: Markdown流程图
author: 不二
date: 2019-07-12 10:17:15
tags: Markdown
mathjax: true
categories: 经验总结
---
## 文字不如图

---

### 流程图

Markdown中的流程图都是通过代码实现的，即平时展示代码的地方写流程图代码，使用的语言是**mermaid**。

#### 连线选择
| 圆角 | 方形 | 条件 | 圆形 | 带箭头的连线 | 不带箭头的连线 |
| :--: | :--: | :--: | :--: | :----------: | :------------: |
|  ()  |  []  |  {}  | (()) |     -->      |      ---       |

| 虚线连线 | 带文字的虚线连线 | 粗连线 | 带文字的粗连线 |
| :------: | :--------------: | :----: | :------------: |
|   -.-    |    -. text .-    |  ==>   |  == text \==>  |

*连线上的文字在连线代码后用||包裹*


#### 横向流程图

```mermaid
graph LR
A["方形 &hearts;#9733;"]-->B(圆角)
B-->C{条件a}
C-->|a=1|D[结果1]
C-->|a=2|E[结果2]
F[横向流程图]
```

#### 竖直流程图

```mermaid
graph TD
A[方形]-->B(圆角)
B --> C{条件a}
C -->|a=1| D>结果1]
C -->|a=2| E((结果2))
F[竖向流程图]
```

#### 子图

```mermaid
graph TB
    c1-->a2
    subgraph one
    a1-->a2
    end
    subgraph two
    b1-->b2
    end
    subgraph three
    c1-->c2
    end
```
---

### 序列图

```mermaid
sequenceDiagram
    participant A as Alice
    participant J as John
    A->>J: Hello John, how are you?
    J->>A: Great!
```
或者使用
```mermaid
sequenceDiagram
    Alice->>+John: Hello John, how are you?
    John-->>-Alice: Great!
```
#### 连线选择
| 类型 |            描述            |
| :--: | :------------------------: |
|  ->  |       没有箭头的实线       |
| -->  |       没有箭头的虚线       |
| ->>  |        带箭头的实线        |
| -->> |        带箭头的虚线        |
|  -x  | 实线与末端的十字架（异步） |
| --x  | 最后用十字线的虚线（异步） |

#### 激活
```mermaid
sequenceDiagram
    Alice->>+John: Hello John, how are you?
    Alice->>+John: John, can you hear me?
    John-->>-Alice: Hi Alice, I can hear you!
    John-->>-Alice: I feel great!
```
#### 笔记
```mermaid
sequenceDiagram
    participant John
    Note right of John: Text in note
```
#### 循环

```mermaid
sequenceDiagram
    Alice->John: Hello John, how are you?
    loop Every minute
        John-->Alice: Great!
    end
```

---

### 甘特图

```mermaid
gantt
    title A Gantt Diagram
    dateFormat  YYYY-MM-DD
    section Section
    A task           :a1, 2014-01-01, 30d
    Another task     :after a1  , 20d
    section Another
    Task in sec      :2014-01-12  , 12d
    another task      : 24d
```

```mermaid
gantt
       dateFormat  YYYY-MM-DD
       title Adding GANTT diagram functionality to mermaid

       section A section
       Completed task            :done,    des1, 2014-01-06,2014-01-08
       Active task               :active,  des2, 2014-01-09, 3d
       Future task               :         des3, after des2, 5d
       Future task2              :         des4, after des3, 5d

       section Critical tasks
       Completed task in the critical line :crit, done, 2014-01-06,24h
       Implement parser and jison          :crit, done, after des1, 2d
       Create tests for parser             :crit, active, 3d
       Future task in critical line        :crit, 5d
       Create tests for renderer           :2d
       Add to mermaid                      :1d

       section Documentation
       Describe gantt syntax               :active, a1, after des1, 3d
       Add gantt diagram to demo page      :after a1  , 20h
       Add another diagram to demo page    :doc1, after a1  , 48h

       section Last section
       Describe gantt syntax               :after doc1, 3d
       Add gantt diagram to demo page      :20h
       Add another diagram to demo page    :48h
```
