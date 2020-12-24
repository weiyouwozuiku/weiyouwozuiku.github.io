---
title: CloudSim学习
date: 2019-07-08 23:36:11
mathjax: true
tags: 
- CloudSim
- 云计算
---

# CloudSim

## 基础类

- Cloudlet 对应的是云端应用
- Vm 对应的是虚拟机，其中封装了虚拟机相应的属性和行为
- Broker 对应的是用户代理，是云端用户的代表，包括虚拟机和云应用在内的资源都要绑定到Broker上。
- Host 对应的是物理主机
- Datacenter 对应的是数据中心，所有物理主机资源必须绑定到数据中心，至少创建一个

---

## 模拟实验步骤

1. 初始化CloudSim
2. 创建数据中心DataCenter
3. 创建用户代理Broker
4. 创建虚拟机Vm
5. 创建应用Cloudlet
6. 将云端应用绑定到指定的虚拟机上
7. 开启模拟仿真
8. 结束模拟仿真
9. 打印输出仿真结果

---

 ## DataCenter中Host的创建

所需参数：

* 主机编号
* 内存
* 网络带宽
* 外存容量
* cpu列表
* 虚拟机调度策略（主机上的）

```java
new Host(
    hostId,
    new RamProvisionerSimple(ram),
    new BwProvisionerSimple(bw),
    storage,
    peList,
    new VmSchedulerTimeShared(peList)
);
```

---

## 数据中心的创建

首先创建数据中心特性：
需要的参数 @parameter :

1. 主机架构
2. 操作系统
3. 虚拟机监视器种类
4. 时区
5. cpu的使用费用
6. 内存的使用费用
7. 外存的使用费用
8. 带宽的使用费用

```java
new DatacenterCharacteristics(
         arch, os, vmm, hostList, time_zone, cost, costPerMem,costPerStorage, costPerBw
         );
```

创建数据中心：

1. 数据中心的名字
2. 数据中心特性
3. 虚拟机的创建策略
4. 存储设备列表

```java
new Datacenter(name, characteristics, new VmAllocationPolicySimple(hostList), storageList, 0);
```

---

## 虚拟机的创建

需要的参数 @parameter:

1. 虚拟机编号
2. 用户
3. MIPS（Tips:CPU是由MIPS来标定其计算能力的）
4. 镜像大小（MB）
5. 内存大小（MB）
6. 网络带宽
7. cpu数量
8. 虚拟机监视器类型
9. 应用调度策略（虚拟机上的）

```java
new Vm(vmid, brokerId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());
```

---

## 应用的创建

需要的参数 @parameter ：

1. 应用编号
2. 执行时的应用长度(MI)
3. 要使用到的处理器数量
4. 提交应用前的文件大小（byte）
5. 应用执行完成后的文件大小(byte)
6. cpu的使用模型/策略
7. ram的使用模型/策略
8. 网络带宽的使用模型/策略

```java
new Cloudlet(id, length, pesNumber, fileSize, outputSize, utilizationModel, utilizationModel, utilizationModel);
```

---

## 用户的创建

一般情况下，Broker的创建需要自定义以适应不同的用户策略.

## 暂停仿真

- 覆盖Runnable接口的run方法;
- 将要运行的代码放在run方法中;
- 向cloudsim核心引擎层提交暂停仿真事件;
- Cloudsim仿真暂停5秒钟后,动态创建数据中心代理broker_1;
- 创建虚拟机和云任务并提交到broker_1代理中;
- 提交虚拟机列表和云任务列表;
- 重启Cloudsim仿真;

```java
// A thread that will create a new broker at 200 clock time
			Runnable monitor = new Runnable() {
                 //TODO 仿真暂停的代码
        //动态创建数据中心代理代码
        //以下代码为一个样例
        //仿真暂停到第200个时钟时间
				@Override
				public void run() {
					Log.printLine(CloudSim.clock());
                    //在CLoudsim中的clock到200时暂停Cloudsim
					CloudSim.pauseSimulation(200);
					Log.printLine(CloudSim.clock());
					while (true) {
						if (CloudSim.isPaused()) {
							Log.printLine(CloudSim.clock());
							Log.printLine("Cloudsim暂停中");
							break;
						}
						try {
							Log.printLine("线程睡眠100ms");
							Log.printLine(CloudSim.clock());
							Thread.sleep(100);
						} catch (InterruptedException e) {
							e.printStackTrace();
						}
					}

					Log.printLine("\n\n\n" + CloudSim.clock() + ": The simulation is paused for 5 sec \n\n");
                    //主程序暂停运行5秒
					try {
						Thread.sleep(5000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}

					DatacenterBroker broker = createBroker("Broker_1");
					int brokerId = broker.getId();

					//Create VMs and Cloudlets and send them to broker
					vmlist = createVM(brokerId, 5, 100); //creating 5 vms
					cloudletList = createCloudlet(brokerId, 10, 100); // creating 10 cloudlets

					broker.submitVmList(vmlist);
					broker.submitCloudletList(cloudletList);

					CloudSim.resumeSimulation();
				}
			};
```

## 改写仿真事件

#### processEvent

- 继承SimEntity类
- 创建代理
- 创建虚拟机和云任务并提交虚拟机列表和云任务列表
- Cloudsim.resumeSimulation();

#### startEntity

- 调用schedule()

## 网络

网络拓扑的信息包括:

- 节点的位置
- 节点的有向边
- 边时延
- 边带宽等信息

调用`org.cloudbus.cloudsim.NetworkTopology`构建网络拓扑图,然后把Cloudsim实体与拓扑图的节点进行映射.生成延迟矩阵(利用FloydWarshall计算的最短路径之后得出的)和带宽矩阵.

```java
//Sixth step: configure network
			//load the network topology file
			NetworkTopology.buildNetworkTopology("modules/cloudsim-examples/src/main/java/org/cloudbus/cloudsim/examples/network/topology.brite");

			//maps CloudSim entities to BRITE entities
			//PowerDatacenter will correspond to BRITE node 0
			int briteNode=0;
			NetworkTopology.mapNode(datacenter0.getId(),briteNode);

			//Broker will correspond to BRITE node 3
			briteNode=3;
			NetworkTopology.mapNode(broker.getId(),briteNode);
```

在brite文件中,程序寻找标记`Nodes`和`Edges`.从其下一行开始执行.

Node的格式(列)为:NodeID,xpos,ypos,indegree,outdegree,ASid(AS应用服务器,如果分层,节点属于的ASid),type(router/AS)

Edge的格式(列)为:边序号,始节点序号,终节点序号,边长度,边时延,边带宽.

通过事件管理引擎实现实体i到实体j的传递总共的仿真时间是t+d,其中t表示消息传送最初的仿真时间,d代表实体i与j之间的网络延时.

在`network`的`datacenter`中`AppCloudlet`可以理解为一个完整的工作流程,`NetworkCloudlet`可以理解为工作流程中的一个功能.