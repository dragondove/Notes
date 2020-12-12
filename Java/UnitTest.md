# Java 单元测试编写完全教程(TestNG + Mockito + Powermock)

## 什么是单元测试？

单元测试是为了测试代码的最小单元而存在的测试。怎样的单元算是最小单元则由程序员自己决定。在面向对象语言中，最小单元往往是一个 (public) method。当然，有些人也会把整个类当成最小单元。不过说到底，单元测试是越简单越好的。如果一个单元测试很复杂，那么它很可能是一个集成测试。当然，为了每个单元测试都足够简单，一般期望每一个 public 方法所做的事情也尽量简单，这样也有利于代码的复用。

## 为什么要编写单元测试？

起初，我对单元测试感到疑惑：明明都有集成测试了，为什么还需要编写单元测试？通过后期对系统的维护，我感受到了单元测试的几个优点：

- 帮助程序员更好地理解需求：每次编写单元测试时，我刚好能对代码做一次 review，同时可以再理一遍思路，查看是否符合需求。整个过程有点像是[小黄鸭调试法](https://baike.baidu.com/item/%E5%B0%8F%E9%BB%84%E9%B8%AD%E8%B0%83%E8%AF%95%E6%B3%95)。
- 提升代码的质量：如果单元测试难以编写，一般说明代码在编写层次上存在缺陷，需要进行调整甚至重构。
- 提供代码级别的文档：对于未接触过当前应用的人来说，他们可以通过单元测试代码来了解应用的运行方式。
- 方便代码修改或者重构时的派错：当你对原本的代码进行修改的时候，良好的单元测试可以及时地告诉你哪些修改会影响到原本的逻辑。

## 单元测试框架的选择(Junit4/Junit5/TestNG)

下面的表格列出了不同的功能使用这三个框架时分别需要使用哪些注解：

| 功能                       | Junit 4                           | TestNG                                      | Junit 5                           |
| -------------------------- | --------------------------------- | ------------------------------------------- | --------------------------------- |
| 标注为单元测试方法         | @Test                             | @Test                                       | @Test                             |
| 单元测试类执行前执行的方法 | @BeforeClass                      | @BeforeClass                                | @BeforeAll                        |
| 单元测试类执行后执行的方法 | @AfterClass                       | @AfterClass                                 | @AfterAll                         |
| 每个方法执行前执行的方法   | @Before                           | @BeforeMethod                               | @BeforeEach                       |
| 每个方法执行后执行的方法   | @After                            | @AfterMethod                                | @AfterEach                        |
| 禁用单元测试               | @Ignore                           | @Ignore/@Test(enabled=false)                | @Disable                          |
| 断言异常                   | @Test(expected={Exception.class}) | @Test(expectedExceptions={Exception.class}) | @Test(expected={Exception.class}) |

从常用功能上来看，三者实际上都足够开发使用。但是从以来引进的便携度来说，TestNG 更加方便，引入一个包就包含了测试结果报告生产的功能，而 Junit 则需要额外引入生产报告的扩展。在提供的断言工具类中，TestNG 的 assertEquals(actual, expected) 相比 Junit 的 isEquals(expected, actual) 更符合我的习惯，因此我使用 TestNG 来写本篇教程。

## 使用 TestNG

首先，引入 TestNG 的依赖
