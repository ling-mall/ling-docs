# Flowable

官方网站：[https://www.flowable.com/open-source](https://www.flowable.com/open-source)

文档: [https://www.flowable.com/open-source/docs/oss-introduction](https://www.flowable.com/open-source/docs/oss-introduction)

lowable BPMN 用户手册 (v 6.3.0): [https://tkjohn.github.io/flowable-userguide/](https://tkjohn.github.io/flowable-userguide/)

在线设计器：[http://www.bpmnmodeler.com/flowable/](http://www.bpmnmodeler.com/flowable/)

参考文章：

- <https://blog.csdn.net/Z_xiansheng11/article/details/106144837>
- <https://blog.csdn.net/jflows/article/details/126766737>

## 开始

Flowable是一个使用Java编写的轻量级业务流程引擎。Flowable流程引擎让你可以部署BPMN 2.0流程定义（用于定义流程的行业XML标准）、创建这些流程定义的流程实例、进行查询、访问运行中或历史的流程实例与相关数据，等等。

## Spring boot 整合

文档：<https://www.flowable.com/open-source/docs/bpmn/ch05a-Spring-Boot>

首先需要引入对应的版本的依赖

```xml
        <dependency>
            <groupId>org.flowable</groupId>
            <artifactId>flowable-spring-boot-starter</artifactId>
            <version>7.0.0</version>
        </dependency>

        <!--Mysql数据库驱动-->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
        </dependency>
```

然后配置好数据源后启动项目，会自动生成很多张数据库的表

表说明来自：<https://www.toutiao.com/article/7140565987921084932>

- 表名前缀：

  - ACT\_：表示与Flowable开源代码库相关的数据库表名以ACT\_开头。
  - FLW\_：表示特定于Flowable Work或Engage的数据库表以FLW\_前缀开头。

- 表名中间部分：

  - APP：表示这都是跟应用程序相关的表。
  - CMMN：表示这都是跟CMMN协议相关的表。
  - CO：表示这都是跟内容引擎相关的表。
  - DMN：表示这都是跟DMN协议相关的表。
  - EVT：表示这都是跟事件相关的表。
  - FO：表示这都是跟表单相关的表。
  - GE：表示这都是通用表，适用于各种用例的。
  - HI：表示这些是包含历史数据的表。
  - ID：表示这都是跟用户身份认证相关的表。
  - PROCDEF：表示这都是跟记录流程定义相关的表。
  - RE：表示这都是跟流程的定义、流程的资源等包含了静态信息相关的表。
  - RU：代表运行时，这些是包含尚未完成的流程、案例等的运行时数据的运行时表。
  - CHANNEL：表示这都是跟泳道相关的表。
  - EV：在这里似乎并没有一个明确的含义，相关的表也都是跟Liquibase相关的表。
  - EVENT：表示这都是跟事件相关的表。

- 表名后缀：
  - DATABASECHANGELOG：表名中包含这个单词的，表示这个表是Liquibase执行的记录。
  - DATABASECHANGELOGLOCK：表名中包含这个单词的，表示这个表记录Liquibase执行锁的。

## BPMN

参考链接：

- <https://www.flowable.com/open-source/docs/bpmn/ch02-GettingStarted>
- <https://developer.aliyun.com/article/840431>
- <https://blog.csdn.net/honor_zhang/article/details/120727673>
- <https://blog.csdn.net/FeiChangWuRao/article/details/126252962>
- ChatGPT

BPMN(Business Process Model & Notation)，中文名为业务流程模型与符号。BPMN2.0是OMG(Object Management Group，对象管理组织)制定的，其主要目的是既给用户提供一套简单的、容易理解的机制，以便用户创建流程模型；又能使用户能很好的处理不同流程模型内在的复杂性。

1、是一套业务流程模型与符号建模标准；

2、精准的执行语义来描述元素的操作，可以将元素的操作描述的很清楚，没有二义性；

3、以XML为载体，以符号可视化业务，每个xml模块都可以对应符号，流程图与xml有一个相互转换的过程。

BPMN包含：

- 流对象（Flow Object）:
  - 事件（Events）
    - 启动事件（startEvent）
      - 空启动事件
      - 定时器启动事件
      - 消息启动事件
      - 信号启动事件
      - 错误启动事件
    - 结束事件
      - 空结束事件
      - 错误结束事件
      - 终止结束事件
      - 取消结束事件
    - 边界事件
      - 定时器边界事件
      - 错误边界事件
      - 信号边界事件
      - 消息边界事件
      - 取消边界事件
      - 补偿边界事件
    - 中间事件
      - 捕获中间事件
        - 定时器捕获中间事件
        - 信号捕获中间事件
        - 消息捕获中间事件
      - 抛出中间事件
        - 空抛出中间事件
        - 信号抛出中间事件
        - 补偿抛出中间事件
  - 活动（Activities）
  - 网关（Gateways）
    - 排他网关
    - 并行网关
    - 包容网关
    - 基于事件的网关
- 连接对象（Connecting Objects）:
  - 顺序流（Sequence Flow）
    - 条件顺序流
    - 默认顺序流
  - 消息流（Message Flow）
  - 关联（Association）

- 泳道（Swimlanes）:
  - 池（Pool）
  - 道（Lane）

- 附加工件（Artifacts/Artefacts）:
  - 数据对象（Data Object）
  - 组（Group）
  - 注释（Annotation）

### 根元素

一个BPMN 2.0 XML流程的根是definitions元素。 在命名状态，子元素会包含真正的业务流程定义。 每个process子元素 可以拥有一个id（必填）和 name（可选）。下面是一个空的BPMN 2.0业务流程 。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
             xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC"
             xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI"
             xmlns:flowable="http://flowable.org/bpmn"
             typeLanguage="http://www.w3.org/2001/XMLSchema"
             expressionLanguage="http://www.w3.org/1999/XPath"
             targetNamespace="http://www.flowable.org/processdef">

    <process id="MyProcess" name="Holiday Request">
    </process>

</definitions>
```

> **下文示例除特殊情况，均在根元素的 `process` 标签下**

### 流对象

流对象包括事件(Events)，活动(Activities)，网关(Gateways)。

#### 事件

事件是BPMN2.0执行语义中一个非常重要的概念，是流程运行过程中发生的事情，这些事情的发生会影响到流程的运转。对每个事件而言，一般会包含两个要素，触发事件的原因与事件发生导致的结果。

![事件全览](https://灵.top/picture/image/picgo/202401181137654.png)

按照位置分类： 开始事件(Start)、中间事件(Intermediate)、结束事件(End)。通过事件控制器为系统添加辅助功能，如与其他业务系统集成、活动预警等。

事件的图像符号是圆形。其中空心圆表示开始事件，嵌套的圆表示中间事件，嵌套圆且两圆之间为阴影表示结束事件。如下图所示。

![事件](https://灵.top/picture/image/picgo/202401181128251.png)

依据触发方式不同分类：我们可以分为抛出事件(Throw)，捕获事件(Catch)，抛出事件是流程走到某一步主动发生的事件，捕获事件是流程定义好的触发器被触发的时候，流程就开始执行，捕获事件是被动型。

按照定义分类：分为消息(Message)事件，定时器(Timer)事件，升级(Escalation)事件，条件(Conditional)时间，链接(Link)事件，错误(Error)事件，取消(Cancel)事件，补偿(Compensation)事件，信号(Signal)事件，多重(Multiple)事件，并行(Parallel multiple)多重事件。

按照是否会中断流程运转分类：分为中断(Interrupting)事件和非中断(non-interrupting)事件。中断事件用圆实线表示，非中断事件用圆虚线表示。

##### 启动事件

启动事件是流程的起点，表示业务流程的开始。

启动事件在BPMN图中以一个空心圆圈表示，通常位于流程图的最上方。它代表着流程的启动，当满足某个条件时，流程将从启动事件处触发执行。

###### 空启动事件

未指定触发器，就是由用户来触发启动的事件。

空心圆来代表空启动事件（中间没有图标就是没有触发器的意思）

定义空启动事件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
             xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC"
             xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI"
             xmlns:flowable="http://flowable.org/bpmn"
             typeLanguage="http://www.w3.org/2001/XMLSchema"
             expressionLanguage="http://www.w3.org/1999/XPath"
             targetNamespace="http://www.flowable.org/processdef">

    <process id="MyProcess" name="Holiday Request">
        <!--下面是空启动事件-->
        <startEvent id="start" name="my start event"/>
    </process>
</definitions>
```

当启动一个流程实例时，流程实例如果有空启动事件，流程实例将从这个事件处立刻开始执行

启动流程实例：

```java
@Autowired
private RuntimeService runtimeService;
...
runtimeService.startProcessInstanceByKey("MyProcess");
```

其中 `MyProcess` 是流程定义的key，是 `process` 标签的 id

扩展 `flowable:formKey`:

```xml
<startEvent id="request" flowable:formKey="simpleForm" />
```

`flowable:formKey` 属性的值可以是一个字符串，用于标识流程启动时要使用的表单。这个表单可以是一个外部的表单或者是一个内部的表单（由 Flowable Form Engine 管理）。

这样，用户就可以填写和提交表单数据，作为流程的输入。

###### 定时器启动事件

在流程只需要定时启动一次，或者流程需要在特定的时间间隔重复启动时，可以使用定时器启动事件。

**注意**：

- 子流程不能有定时器启动事件
- 定时器启动事件，在流程部署的同时就开始计时。不需要调用startProcessInstanceByXXX就会在时间启动。调用startProcessInstanceByXXX时会在定时启动之外额外启动一个流程。
- 当部署带有定时器启动事件的流程的更新版本时，上一版本的定时器作业会被移除。这是因为通常并不希望旧版本的流程仍然自动启动新的流程实例。

定义定时器启动事件，使用 `timerEventDefinition` 标签定义，子标签为定义的时间、等待时间、周期等

子标签

- `timeDate`: 到设定的时间就会触发
- `timeDuration`: 从设定的时间开始，等待指定的时间后开始执行事件
- `timerCycle`: 从设定的时间开始，等待指定的时间后开始执行事件，周期性触发

`timeDate`示例:

```xml
<timerEventDefinition>
    <timeDate>2024-01-11T12:13:14</timeDate>
</timerEventDefinition>
```

`timeDuration`示例:

```xml
<timerEventDefinition>
    <timeDuration>PT15M</timeDuration>
</timerEventDefinition>
```

时间格式遵循 [`ISO 8601 duration`](https://en.wikipedia.org/wiki/ISO_8601#Durations) 格式

格式符号：

- `P`：是持续时间标识符（表示周期），位于持续时间表示的开头。
  - `Y`：是紧随日历年数值后的年份标识符。
  - `M`：是紧随日历月数值后的月份标识符。
  - `W`：是紧随周数值后的周标识符。
  - `D`：是紧随日历天数值后的日期标识符。
- `T`：是在时间表示的时间组件之前的时间标识符。
  - `H`：是紧随小时数值后的小时标识符。
  - `M`：是紧随分钟数值后的分钟标识符。
  - `S`：是紧随秒数值后的秒标识符。

格式示例：

- `P3Y6M4DT12H30M5S` 表示当这个事件被触发时，需要等待一个持续时间，表示“三年六个月四天十二小时三十分五秒”后开始执行事件
- `PT15M` 表示当这个事件被触发时，需要等待一个持续时间，表示“15分钟”后开始执行事件

`timerCycle`示例:

```xml
<timerEventDefinition>
    <timerCycle>R3/PT10H/2022-01-12T23:59:59+00:00</timerCycle>
</timerEventDefinition>
```

`R3/PT10H/2022-01-12T23:59:59+00:00` 表示重复三次启动，每次间隔为10小时，到指定时间时结束重复

可以搭配变量比如结束时间`${EndDate}`

```xml
<timerEventDefinition>
    <timeCycle>R3/PT10H/${EndDate}</timeCycle>
</timerEventDefinition>
```

###### 消息启动事件

消息启动事件允许在接收到特定消息时自动启动或继续执行流程。

示例：

```xml
<definitions id="definitions"
              xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
              xmlns:flowable="http://flowable.org/bpmn"
              targetNamespace="Examples"
              xmlns:tns="Examples">

    <message id="newInvoice" name="newInvoiceMessage" />

    <process id="invoiceProcess">
        <!---   这里启动事件 + messageEventDefinition元素作为消息启动事件  -->
        <startEvent id="messageStart" >
            <messageEventDefinition messageRef="tns:newInvoice" />
        </startEvent>
        ...
    </process>
</definitions>
```

`messageRef` 中的 `tns:` 表示命名空间，在根标签 `definitions` 内定义

`targetNamespace`：BPMN文件中定义的所有元素和属性的命名空间URI，用于确保BPMN文件中所有元素和属性的唯一性。

`xmlns:tns`：用户自定义命名空间前缀，用于区分BPMN文件中不同的元素和属性。

**注意**:

- 给定流程定义中，**消息启动事件的名字必须是唯一的**。一个流程定义不得包含多个同名的消息启动事件。如果流程定义中有两个或多个消息启动事件引用同一个消息，或者两个或多个消息启动事件引用了具有相同消息名字的消息，则Flowable会在部署这个流程定义时抛出异常。
- 在所有已部署的流程定义中，消息启动事件的名字必须是唯一的。如果在流程定义中，一个或多个消息启动事件引用了已经部署的另一流程定义中消息启动事件的消息名，则Flowable会在部署这个流程定义时抛出异常。
- 在部署流程定义的新版本时，会取消上一版本的消息订阅，即使新版本中并没有这个消息事件。

###### 信号启动事件

## 操作

<https://developer.aliyun.com/article/1138002>
