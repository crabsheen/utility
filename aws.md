## AWS
  
**分享目的，看看全球市场份额第一的云服务都有什么好玩有趣的产品和优势在哪里？**

产品总览 [https://console.aws.amazon.com](https://console.aws.amazon.com)

其中`计算` 和 `数据库`是核心基础产品，`负载均衡`被包含在`计算`内。

###EC2(Elastic Compute Cloud)
关于EC2的一些定价模式 https://aws.amazon.com/cn/ec2/pricing 

>按需		以小时或者分钟为实例付费，适用于短时业务。

>预留实例	为用户提供预留资源，适用于相对长期服务。

>专用主机	灵活绑定使用许可证，比如你有软件的license，并满足合规。

>竞价实例	Amazon EC2 Spot 实例是 AWS 云中的可用空闲计算容量，与按需实例的价格相比，这类实例可提供超低折扣。按需实例和竞价实例的唯一区别在于，当 EC2 需要更多容量时，需中断。

```
PS:新用户享有t2.micro规格为期12个月的免费使用权。
```
安全组：任何安全组规则的update将导致取决于该规则的流量在较短的时间段内下降， 直到能够创建新规则。
![/var/folders/d_/mfrr4ww10d18vsmwnplt6s9r0000gn/T/com.evernote.Evernote/com.evernote.Evernote/WebKitDnD.iVlLgD/99DDC09C-2917-4D49-9021-9698AAA3CB61.png](/var/folders/d_/mfrr4ww10d18vsmwnplt6s9r0000gn/T/com.evernote.Evernote/com.evernote.Evernote/WebKitDnD.iVlLgD/99DDC09C-2917-4D49-9021-9698AAA3CB61.png)

EC2优缺点

```
优点:
1.主机类型覆盖程度广，匹配更多业务需求。比如 通用型，计算型，内存优化，存储优化，GPU计算、图形。
2.定价模式灵活性强。
3.使用报告数据维度丰富且直观。并且智能给用户推荐资源组合产品(这个厉害，根据用户当前资源使用监控数据给用户推荐最佳匹配方案，包括成本降低和性能兼顾)
4.CloudWatch设置监控报警。
5.VPC网络流日志用于排查分析。
6.用户自定义 PlacementGroups。
7.Marketplace可以提供AMI或者个人定制AMI

缺点:
1.免费实例监控细粒度不够，特别是追查问题时，5分钟维度太粗了，如果设置为1分钟需要额外付费。
2.尚不支持IPV6弹性IP(Elastic IP Address,eip)
3.经典网络模式和VPC网络模式两者之间弹性IP迁移的限制。单向迁移，从经典网络到VPC。
```
什么是*PlacementGroups（置放群组）*？ 

**群集置放(不可跨可用区)**和**分布置放**，可以想象下我们的大数据大流量内聚邻近部署业务 和 分散以提高冗余的普通业务。
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/7687330120359338422.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/7687330120359338422.png)

等于无需自主分布调整服务节点，交由aws来根据置放群组的属性动态分配。


###LoadBalancer(属于计算大类)
跨可用区(能做到跨可用区的流量分发均衡)，类似于为跨机房VIP互挂。

监听器(我们叫健康检查 check\_http\_send 等) **切记realserver安全组设置给予健康检查的放开**

TargetGroups(概念类似于nginx upstream Pool)

安全组，访问控制(和实例主机的安全组设置无异)
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/14991306570217794965.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/14991306570217794965.png)

负载均衡域名
[http://myloadbalance-366c0de14d5fb9b2.elb.us-east-2.amazonaws.com/index.html](http://myloadbalance-366c0de14d5fb9b2.elb.us-east-2.amazonaws.com/index.html)

___
定价模式：**时间** + **LCU (Load Balancer Capacity Unit)** 组合定价。(腾讯云TGW是如何计费的？)

7层负载价格 > 4层负载价格；欧洲区域价格 > 美洲区域 or 亚太区域。

**LCU 分4个维度细分，按使用量最高的维度付费**

```
1. 新连接数：每秒新建连接的数量。通常，每个连接可发送多个请求。	25个新建/每秒
2. 活跃连接数：每分钟内活跃连接的数量。	3000个活动连接/每分钟
3. 带宽：负载均衡器处理的流量 (以 Mbps 为单位) 	1G/每小时
4. 规则评估数：指负载均衡器处理的规则数量与请求率的乘积。免费处理前 10 个规则 (规则评估数 = 请求率 * (处理的规则数量 – 10 个免费规则)) 	1000个规则评估/每秒
```

思考？我们若遇到类似负载均衡服务，会如何制定服务定价对应的衡量办法？

###aws lamdba
玩弄下最近相对火的 **lamdba，函数计算**

根据框架模板(比如java执行example.Hello::myHandler)，完全由用户自主编写函数代码块上传调试or运行。 有最高并发限制。

目前支持java,go,c#,node.js,python，我做了个把玩的例子，具体实现在Hello类的myHandler()中。

```
$ cat  META-INF/MANIFEST.MF   
Manifest-Version: 1.0
Created-By: 1.8.0_72 (Oracle Corporation)
Main-Class: example.Hello

$ jar cvfm duwei.jar META-INF/MANIFEST.MF *
```

![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/1187564736508021812.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/1187564736508021812.png)

**关于lambda的定价模式**：

```
With AWS Lambda, you pay only for what you use.
You are charged based on the number of requests for your functions and the duration, 
the time it takes for your code to execute.
```
计费原则比较明确，分两块合计:

一个是计算资源的开销(把时间转换为mem)

一个是按照请求数(为响应事件通知或调用 (包括控制台的测试调用) 而开始执行时便记为一次请求)。

```
美国区 免费资源为 每个月可以调用100W次请求 + 40W GB的内存计算资源
```

![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/3217997063568209768.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/3217997063568209768.png)


**lambda 与 batch有哪些区别？？或者咱们的vacuum呢？**


###成本管理器，账单(非常细致，方便用户对费用的全面了解，类似爽完结束后的清单。。)
**aws service charges** 按产品分配合计并展示详细子类的费用。
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/17214379926625402523.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/17214379926625402523.png)

**cost-report** 

更多详细以趋势图表呈现，可视化。13个月的数据并`预测未来`3个月可能产生的费用。
维度很细：可用区，关联账号，API操作情况等等的数据维度视图，时间维度每日每月等。
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/3426941819343235723.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/3426941819343235723.png)

**AWS Budgets**

![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/12698854226242044809.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/12698854226242044809.png)
用户可以在aws上做成本预算，详细到各个产品的费用细节，如果实际运行资源成本超于一定阈值会给予提醒。

**针对特定客户比如netflix，aws是否可以根据它长期(10个Q以上)运行的业务使用资源趋势，直接给出未来的资源成本预算？**





##记录一次偶然的Case沟通(学好外语很重要)
3月6号 日账单显示我有个m4.large规格的instance持续使用了1个小时需付费。

奇怪的是我并没有创建过，心想是否可能是 **lambda** 或 **batch** 所导致的？

心想lambda只有比如cpu mem等资源使用情况，没有涉及到具体实例的。

猜测很可能是发生在aws batch上，因为batch是跑任务(可以是各种任务)的。

####我们先简单看下aws batch的结构

job ----> job queue ----> compute-environment ----> auto scaling group（ASG）

确实在ASG里面可以看到一些action如下
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/16662772288115772862.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/16662772288115772862.png)

有EC2 instace 创建记录但是没有说具体类型是什么。`这里要稍微吐槽一下，销毁的instance 历史操作记录不太方便查看。`

这儿再推荐个东西叫 **CloudTrail** 便于跟踪历史记录，个人认为信息已经很细致了。

基于时间，用户，具体的事件，资源类型等等 (比如谁在何时登陆，操作了什么资源比如EC2，怎么个操作法比如创建还是销毁)

看到`RunInstances`点详情看是`autoscaling.amazonaws.com`所引起的，进一步根据实例ID点进去找不到实例。
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/15907778299511579234.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/15907778299511579234.png)

目前为止只能猜测到这里。于是在aws support center上提了case.

关于case的响应 沟通方式 和后续跟踪处理 还是很友好的。

[https://console.aws.amazon.com/support/v1#/case/?displayId=4919337901&language=en](https://console.aws.amazon.com/support/v1#/case/?displayId=4919337901&language=en)
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/4434892214305810559.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/4434892214305810559.png)

aws support目前支持语言仅限于英语，日语
![/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/11296003620168586373.png](/Users/huiyang/Library/Application Support/com.mogujie.TeamTalk/ImageCache/11296003620168586373.png)
提交手机号之后会有人员自动回拨进行问题沟通，除了电话外还有处理记录会以邮件形式同步出来。

aws技术支持人员第一次联系我，主要就我的问题做详细了解，虽然10分钟的沟通但只有2分钟是有效的，英语口语还是太差了，报上case id然后勉强说明了问题，即这笔cost为什么会发生。**发生在何时，以及怎么发生的？**

对方回复先去后台查一下日志再回电，大概1小时后来电说明了m4.large实例的产生是由aws batch引起的，和猜想的一样，当batch任务结束之后实例也自动跟着销毁了。然后我追问为什么是该规格的实例而不是其他规格的？

```
Question: Why m4.large instance in particular was launched when you launched AWS Batch Job?

- It appears that you specified 2 x vCPUs as an attribute in the job definition. Since m4.large instances have 2x vCPUs, they were launched per this specification.
- When you choose “optimal” when creating their compute environment, which picks instance types (from the latest C, M, and R instance families) on the fly that match the demand of your job queues.
```
最后莫名其妙，`AWS Account and billing team` 给我发了份邮件说决定送我一个抵扣券用于抵扣前面发生的实例使用费用。😂😂

我只是个免费的个人用户，aws support的服务还是相当可以的。
