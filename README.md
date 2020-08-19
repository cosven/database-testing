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

## 其它系统怎样进行测试？

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
