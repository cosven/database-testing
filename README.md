# 分布式数据库测试

收集一下测试（偏数据库）相关的经典、网红资料，附带自己简单的阅读笔记。

## 关于 QA 这个岗位

#### 左耳朵耗子 - [我们需要专职的QA吗？](https://coolshell.cn/articles/6994.html)

文章中心思想：
> 我觉得是不需要全职的QA的，甚至不需要QA这一专职角色或部门，因为，不懂开发的人必然做不好测试。
就像不懂开发的研发经理必然管不好研发团队一样。

这篇文章似乎在当时（2012 年）引起很大的反响，我简单的看了评这篇文章的文章，主要两种观点：
1. 很多专职测试人员完全不懂开发，这确实不健康。开发可以兼职测试的工作。[ref](https://www.cnblogs.com/guanhe/archive/2012/04/12/response_to_do_we_need_qa.html)
2. 需要测试，测试也是软件工程的一个方向，需要专才。[ref](https://www.cnblogs.com/xinz/archive/2012/04/09/2439695.html)

#### TODO 至简李云 - [软件测试团队“核心价值”的思考](https://blog.csdn.net/hzliyun/article/details/9773917)

#### TODO 段念 - [测试团队与咖啡店](https://www.infoq.cn/article/2013/07/testing-and-coffeeshop/)

## 怎样量化一个软件的质量？

### 关于覆盖率

假设一个软件刚开发出来，没人用，那怎样衡量这个软件的质量？

[Uncovering Bugs in Distributed Storage Systems during Testing (not in Production!)](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/paper-1.pdf)
> We interviewed technical leaders and senior managers in Microsoft Azure regarding
the top problems in distributed system development. The consensus was that one of
the most critical problems today is how to improve testing coverage so that bugs
can be uncovered during testing and not in production. The need for better testing
techniques is not specific to Microsoft; other companies, such as Amazon and Google,
have acknowledged [7, 39] that testing methodologies have to improve to be able to
reason about the correctness of increasingly more complex distributed systems that
are used in production.

这段话里面有一句比较关键： **The consensus was that one of the most critical problems today is how to improve testing coverage.**
大概意思就是提升测试覆盖率是关键问题。

但这个覆盖率指的是什么呢？要提升到什么程度呢？
另外，分布式系统的 bug 很多都是由一系列的条件按照特定顺序才会触发，就算有类似 100% MC/DC 覆盖率，
那又能覆盖多少这种情况？

覆盖率，似乎很重要？

[Why Is Random Testing Effective for Partition Tolerance Bugs?](https://dl.acm.org/doi/abs/10.1145/3158134)
随机测试为什么有效？论文好像是说随机能达成一定的覆盖率。不过它这个覆盖率，说的不是代码覆盖率，
而是和网络分区相关的覆盖率。大概可以理解为有多少种分区类型把…然后和 read/write 组合一下。

还有两篇论文也聊到了覆盖率：其中一个聊到了 SQL 覆盖率。

## 典型系统的测试

### Cassandra

截止 2020-09-14 日，Cassandra 官方只能找到以下两篇测试相关的博客，但看起来它们在 4.0 做了不少测试相关的事情。

* [Testing Apache Cassandra 4.0](https://cassandra.apache.org/blog/2018/08/21/testing_apache_cassandra.html)

  简介了 Cassandra 使用的几种测试技术：

  * Replay Testing (Workload Recording, Log Replay, and Comparison)
  * Fuzz Testing and Property-Based Testing (Dynamic Test Generation and Fuzzing)
  * Distributed Tests and Fault-Injection Testing (Validating Behavior Under Fault Scenarios)
  * Upgrade Testing (Ensuring a Safe Upgrade Path)

  Property-Based Testing 这个概念之前听说的比较少，实际上似乎就是断言不变量 🤔

* [Finding Bugs in Cassandra's Internals with Property-based Testing](https://cassandra.apache.org/blog/2018/10/17/finding_bugs_with_property_based_testing.html)

  可以看到，Cassandra 也是在单测中使用了 QuickTeories 这样一个库来实践 property-based testing。
  但个人感觉 property-based testing 或许更适合在系统测试中使用？

Cassandra 开发文档上[有一页是介绍测试的](https://cassandra.apache.org/doc/latest/development/testing.html)，
它们测试分为几部分：

* Unit Testing
* DTests（集成/系统测试，仍然是 deterministic 的）
* Performance Testing


### etcd

截止 2020-08-07 日，在 coreos 官网上能找到两篇 etcd 测试相关的博客。
简而言之就是：集成测试 + 错误注入，没啥特别新奇的地方。
但框架和用例设计感觉值得借鉴和研究。

* [New Functional Testing in etcd](https://coreos.com/blog/new-functional-testing-in-etcd.html)

  > etcd’s functional test suite tests the functionality of an etcd cluster
  > with a focus on failure-resistance under heavy usage.

* [Testing distributed systems in Go](https://coreos.com/blog/testing-distributed-systems-in-go.html)

  上面的集成测试框架 + gofail


### FoundationDB

* [Testing Distributed Systems with Deterministic Simulation](http://alex-ii.github.io/notes/2018/04/29/distributed_systems_with_deterministic_simulation.html)

  1. 通过内置一个框架，让所有事件都通过一个线程来调度（感觉类比各种 loop），
     这样可以知道事件发生的顺序。
  2. 在 IO 操作等函数上封装一层，这样可以模拟现实中的错误。
  3. Simulated processes need to be deterministic: the processes
     being simulated need to themselves be deterministic
