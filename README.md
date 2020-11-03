# 分布式数据库测试

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [分布式数据库测试](#分布式数据库测试)
    - [关于 QA 这个岗位](#关于-qa-这个岗位)
        - [网红文章、观点记录](#网红文章观点记录)
            - [左耳朵耗子 - 我们需要专职的QA吗？](#左耳朵耗子---我们需要专职的qa吗)
            - [TODO 至简李云 - 软件测试团队“核心价值”的思考](#todo-至简李云---软件测试团队核心价值的思考)
            - [TODO 段念 - 测试团队与咖啡店](#todo-段念---测试团队与咖啡店)
    - [怎样量化一个软件的质量？](#怎样量化一个软件的质量)
        - [关于覆盖率](#关于覆盖率)
    - [典型系统的测试](#典型系统的测试)
        - [Cassandra](#cassandra)
        - [etcd](#etcd)
        - [FoundationDB](#foundationdb)
        - [Cockroach](#cockroach)
        - [RocksDB](#rocksdb)

<!-- markdown-toc end -->

收集一下测试（偏数据库）相关的经典、网红资料，附带自己简单的阅读笔记。

## 关于 QA 这个岗位

### 网红文章、观点记录

#### 左耳朵耗子 - 我们需要专职的QA吗？
[我们需要专职的QA吗？](https://coolshell.cn/articles/6994.html)

文章中心思想：
> 我觉得是不需要全职的QA的，甚至不需要QA这一专职角色或部门，因为，不懂开发的人必然做不好测试。
就像不懂开发的研发经理必然管不好研发团队一样。

这篇文章似乎在当时（2012 年）引起很大的反响，我简单的看了评这篇文章的文章，主要两种观点：
1. 很多专职测试人员完全不懂开发，这确实不健康。开发可以兼职测试的工作。[ref](https://www.cnblogs.com/guanhe/archive/2012/04/12/response_to_do_we_need_qa.html)
2. 需要测试，测试也是软件工程的一个方向，需要专才。[ref](https://www.cnblogs.com/xinz/archive/2012/04/09/2439695.html)

#### TODO 至简李云 - 软件测试团队“核心价值”的思考
[软件测试团队“核心价值”的思考](https://blog.csdn.net/hzliyun/article/details/9773917)

#### TODO 段念 - 测试团队与咖啡店
[测试团队与咖啡店](https://www.infoq.cn/article/2013/07/testing-and-coffeeshop/)

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


### Cockroach

* [Introducing Pebble: A RocksDB Inspired Key-Value Store Written in Go](https://www.cockroachlabs.com/blog/pebble-rocksdb-kv-store/)

  Cockroach DB 引入一个新的 Key-Value Store(Pebble) 来代替 RocksDB。比较感兴趣的是它如何测试 Pebble 的：

  它的测试分为几层（读后感：分层会给读者/大家一种很清晰的感觉）
  1. Pebble unit tests
  2. Randomized tests (a.k.a metamorphic tests)
  3. Bidirectional compatibility tests
  4. CockroachDB unit tests
  5. CockroachDB nightly tests (a.k.a. roachtests)

  有趣的部分是 metamorphic tests
  > The metamorphic tests have proved incredibly useful in finding existing bugs,
  > and quickly catching regressions when new functionality has been introduced.

  它的变质测试也是分为两级：自己和自己对比；自己和 RocksDB 对比。
  自己和自己怎样对比呢？使用不同配置。
  该怎样设计不同的配置呢？依据是不同的配置会走不同的代码路径，然后设计一些配置（灰盒）。另外还会随机一些配置。

  变质测试 + 错误注入 这些手段一般会结合在一起。

### RocksDB

* [Stress test](https://github.com/facebook/rocksdb/wiki/Stress-test)

  注：上面的 wiki 链接内容其实已经很精炼了，推荐阅读，这里主要记录自己的理解。

  RocksDB 除了单元测试，另外一个重要的保证稳定性的措施就是 stress test 的持续运行，
  称作“crash test”，被证明是一个高效的找到正确性 bug 的方法。

  单元测试的特点如下：
  1. 确定性（deterministic）
  2. 覆盖一个功能点或者少数 feature
  3. 运行时间有限

  crash test 覆盖的场景不一样：
  1. 随机的
  2. 覆盖广泛的特性
  3. 持续运行
  4. 模拟错误

  读后感：上面这个对比，总结非常精髓。

  crash test 的 workflow：
  * 场景：测试是在一个单一的 db 上进行；每隔一段时间让 db crash；crash 后会 reopen
  * 配置：每次 crash 后，会使用不同的 option 来 open db，option 是随机生成的
  * 负载：每次运行，会并发的对 db 进行操作，输入也是随机生成的
  * 校验：validation 是持续进行，每个重启后，所有的数据都会被校验
  * 编译：debug 模式是打开的，assert 会有效

  注：wiki 里面，后面特意单独描述一下三部分

  1. Database operation -> The goal is for the test to cover as many database operations as possible.
  2. Data validation
     * 每个 key 对应的 value 是可预期的
     * crash 前后，哪些 key 应该存在是可预期的
     * 会把一份操作写入 log 文件，重启后，会进行校验
     * 有些用例，会把数据写入两个 CF，两个 CF 数据需要完全一致
  3. Periodically crash the database
     分两种：黑盒 crash 和白盒 crash。
     * 黑盒：kill -9
     * 白盒：会在文件系统操作接口前后预制一些 point
