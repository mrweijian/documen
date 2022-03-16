文档手册：https://tkjohn.github.io/flowable-userguide/

# 1、Flowable是什么？

Flowable是一个使用Java编写的轻量级业务流程引擎。Flowable流程引擎可用于部署BPMN 2.0流程定义（用于定义流程的行业XML标准）， 创建这些流程定义的流程实例，进行查询，访问运行中或历史的流程实例与相关数据等等。

Flowable可以十分灵活地加入你的应用/服务/构架。可以将JAR形式发布的Flowable库加入应用或服务，来*嵌入*引擎。 以JAR形式发布使Flowable可以轻易加入任何Java环境：Java SE；Tomcat、Jetty或Spring之类的servlet容器；JBoss或WebSphere之类的Java EE服务器，等等。 另外，也可以使用Flowable REST API进行HTTP调用。也有许多Flowable应用（Flowable Modeler, Flowable Admin, Flowable IDM 与 Flowable Task），提供了直接可用的UI示例，可以使用流程与任务。

# 2、流程图参数介绍

![image-20220316165348560](C:\Users\10267\AppData\Roaming\Typora\typora-user-images\image-20220316165348560.png)

**事件**（event）通常用于为流程生命周期中发生的事情建模，图里是【开始、结束】两个圈。

**顺序流**（sequence flow）是流程中两个元素间的连接器。图里是【箭头线段】。

**网关**（gateway）用于控制执行的流向。图里是【菱形（中间有X）】

**用户任务**（user task）用于对需要人工执行的任务进行建模。图里是【矩形】。



**流程介绍：**

首先启动了工作流后，由【开始】节点自动流向【学生】节点，等待该任务执行。任务被分配的学生用户执行后流向 【老师】节点，再次等待该任务执行。被分配的老师用户执行后流向 【网关】，网关以此检查每个出口，流向符合条件的任务，比如这里老师执行任务时是同意，就流向【校长】节点，等待该任务执行。执行后跟老师类似，同意后就流向【结束】节点，整个流程到此结束。



# 3、数据库说明

**1、**Flowable的所有数据库表都以ACT_开头。第二部分是说明表用途的两字符标示符。服务API的命名也大略符合这个规则。

**2、**ACT_RE_: 'RE’代表repository。带有这个前缀的表包含“静态”信息，例如流程定义与流程资源（图片、规则等）。

**3、**ACT_RU_: 'RU’代表runtime。这些表存储运行时信息，例如流程实例（process instance）、用户任务（user task）、变量（variable）、作业（job）等。Flowable只在流程实例运行中保存运行时数据，并在流程实例结束时删除记录。这样保证运行时表小和快。

**4、**ACT_HI_: 'HI’代表history。这些表存储历史数据，例如已完成的流程实例、变量、任务等。

**5、**ACT_GE_: 通用数据。在多处使用。

## **1）通用数据表（2个）**

**act_ge_bytearray：**二进制数据表，如流程定义、流程模板、流程图的字节流文件；
**act_ge_property**：属性数据表（不常用）；

## 2）历史表（8个，HistoryService接口操作的表）

**act_hi_actinst：**历史节点表，存放流程实例运转的各个节点信息（包含开始、结束等非任务节点）；
**act_hi_attachment**：历史附件表，存放历史节点上传的附件信息（不常用）；
**act_hi_comment：**历史意见表；
**act_hi_detail：**历史详情表，存储节点运转的一些信息（不常用）；
**act_hi_identitylink：**历史流程人员表，存储流程各节点候选、办理人员信息，常用于查询某人或部门的已办任务；
**act_hi_procinst：**历史流程实例表，存储流程实例历史数据（包含正在运行的流程实例）；
**act_hi_taskinst：**历史流程任务表，存储历史任务节点；
**act_hi_varinst：**流程历史变量表，存储流程历史节点的变量信息；

## 3）用户相关表（4个，IdentityService接口操作的表）

**act_id_group：**用户组信息表，对应节点选定候选组信息；

**act_id_info：**用户扩展信息表，存储用户扩展信息；

**act_id_membership：**用户与用户组关系表；

**act_id_user：**用户信息表，对应节点选定办理人或候选人信息；

## 4）流程定义、流程模板相关表（3个，RepositoryService接口操作的表）

**act_re_deployment：**部属信息表，存储流程定义、模板部署信息；
**act_re_procdef：**流程定义信息表，存储流程定义相关描述信息，但其真正内容存储在act_ge_bytearray表中，以字节形式存储；
**act_re_model：**流程模板信息表，存储流程模板相关描述信息，但其真正内容存储在act_ge_bytearray表中，以字节形式存储；

## 5）流程运行时表（6个，RuntimeService接口操作的表）

**act_ru_task：**运行时流程任务节点表，存储运行中流程的任务节点信息，重要，常用于查询人员或部门的待办任务时使用；
**act_ru_event_subscr：**监听信息表，不常用；
**act_ru_execution：**运行时流程执行实例表，记录运行中流程运行的各个分支信息（当没有子流程时，其数据与act_ru_task表数据是一一对应的）；
**act_ru_identitylink：**运行时流程人员表，重要，常用于查询人员或部门的待办任务时使用；
**act_ru_job：**运行时定时任务数据表，存储流程的定时任务信息；
**act_ru_variable：**运行时流程变量数据表，存储运行中的流程各节点的变量信息；



**测试**

流程图地址： http://116.62.223.130:8080/flowable-ui/   admin/test

开启流程：http://116.62.223.130:8888/flow/startProcess?modelKey=key1

获取任务：http://116.62.223.130:8888/flow/getTasks?assignee=weijian/teacher/headmaster

审核通过：http://116.62.223.130:8888/flow/applyTask?taskId=e31c0807-a504-11ec-9380-0250f2000002

审核驳回：http://116.62.223.130:8888/flow/rejectTask?taskId=fcd878ad-a504-11ec-9380-0250f2000002