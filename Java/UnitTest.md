# Java 单元测试编写完全教程(TestNG + Mockito + Powermock)

> 本文是笔者自己对单元测试的理解，由于刚入行，可能理解不深，希望读者发现错误可以帮忙指出，谢谢。

- [Java 单元测试编写完全教程(TestNG + Mockito + Powermock)](#java-单元测试编写完全教程testng--mockito--powermock)
  - [术语表](#术语表)
  - [什么是单元测试？](#什么是单元测试)
  - [为什么要编写单元测试？](#为什么要编写单元测试)
  - [单元测试框架的选择(Junit4/Junit5/TestNG)](#单元测试框架的选择junit4junit5testng)
  - [使用 TestNG](#使用-testng)
  - [使用 Mockito](#使用-mockito)
  - [使用 Powermock](#使用-powermock)

## 术语表

| 术语         | 解释                                                                          |
| ------------ | ----------------------------------------------------------------------------- |
| Unit Testing | 简称 UT，单元测试                                                             |
| Stub         | 只做参数填充并直接返回你想要的结果的代码段（例如函数 int foo(args) return v） |
| Fake         | 提供数据的代码段，由于单元测试需要数据，因此就需要产生测试数据的代码段。      |
| Mock         | 模拟，mock 可以提供 stub 的能力，同时可以帮你验证代码的行为。                 |

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

## 使用 [TestNG](https://testng.org/)

首先，引入 TestNG 的依赖。由于我使用的是 gradle，因此只写了 gradle 的配置方式，maven 的方式可以参考 [Maven Surefire Plugin-Using TestNG](https://maven.apache.org/surefire/maven-surefire-plugin/examples/testng.html)

```gradle
// 使用 gradle 引入 testng 依赖
testImplementation 'org.testng:testng:7.3.0'

// 配置测试套件
test {
    useTestNG()
}
```

简单编写一个工具类并测试

```java
// MyUtil.java
import java.util.Arrays;

public class MyUtil {
    public static int sum(int[] num) {
        return Arrays.stream(num).sum();
    }
}

// MyUtilTest.java
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;

public class MyUtilTest {
    // 使用 @Test 注解来表示该方法为单元测试方法
    @Test
    void testSum() {
        int[] arr = new int[] {1, 2, 3};
        // 断言结果，确保结果正确
        assertEquals(MyUtil.sum(arr), 6);
    }
}
```

编写完代码之后，执行 `gradle test` 执行测试，执行完毕之后可以在 build/reports/ 目录下找到单元测试报告。

实际业务中编写单元测试会更加复杂，因为每一个方法都可能依赖几个其他模块，为了防止其他模块影响自己模块的单元测试，我们就需要 stub，在 Java 中，一般使用 Mockito 框架来提供这种能力。

## 使用 [Mockito](https://site.mockito.org/)

```gradle
// Gradle 引入 mockito 依赖
// 使用 inline 版本以提供静态方法和构造器的 mock 支持。
// 注意：inline 版本不能用于安卓开发
testImplementation 'org.mockito:mockito-inline:3.6.28'
```

使用示例

```java
// 例子写的比较奇怪，凑合看吧
public String doFilter(String requestUrl) {
  if (requestUrl.contains("manage")) {
     User user = SessionUtil.getLoginUser();
     if (user == null || !user.getUsername().equalsIgnoreCase("admin")) {
        return "redirect:401";
     }
  }
  return "success";
}


// 单元测试编写
// 首先编写 mockito 单元测试的基类
import org.mockito.MockitoAnnotations;
import org.testng.annotations.BeforeClass;

public class BaseMockTest {
  @BeforeClass
  public void initTest() {
    // 老版本的 mockito 使用 MockitoAnnotations.initMocks(this);
    MockitoAnnotations.openMocks(this);
  }
}

// 所有单元测试继承这个基类以提供 mock 的能力
import org.mockito.MockedStatic;
import org.testng.annotations.Test;

import static org.mockito.Mockito.*;
import static org.testng.Assert.*;

public class MainTest extends BaseMockTest {
  @Test
  void testDoFilter() {
    User user = new User();
    user.setUsername("Admin");
    AdminFilter adminFilter = spy(AdminFilter.class);
    try (MockedStatic<SessionUtil> mockedSessionUtil = mockStatic(SessionUtil.class)) {
      // mock SessionUtil.getLoginUser，mock 静态方法需要 mockito 3.4.0 以上版本
      mockedSessionUtil.when(SessionUtil::getLoginUser).thenReturn(user);
      assertEquals(adminFilter.doFilter("/manage"), "success");
    }
  }
}
```

## 使用 [Powermock](https://github.com/powermock/powermock)

由于 mockito 现在已经支持 mock 静态方法和构造器(since 3.5.0)，powermock 使用场景变少。如果需要 mock private 方法，则可以考虑使用 powermock。或者当你使用低版本的 mockito 时想要 mock 静态方法和构造器，也可以使用，但是使用起来更为麻烦。

引入 powermock 依赖，注意，需要将 mockito-inline 依赖更改为 mockito-core

```gradle
// 更改 mockito 依赖
testImplementation 'org.mockito:mockito-core:3.6.28'
// powermock 依赖
testImplementation 'org.powermock:powermock-api-mockito2:2.0.9'
// powermock testng 整合依赖
testImplementation 'org.powermock:powermock-module-testng:2.0.9'
```

更新单元测试基类

```java
import org.mockito.MockitoAnnotations;
import org.powermock.modules.testng.PowerMockObjectFactory;
import org.testng.IObjectFactory;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.ObjectFactory;

public class BaseMockTest {
  @BeforeClass
  public void initTest() {
    MockitoAnnotations.openMocks(this);
  }
  // powermock 对象工厂
  @ObjectFactory
  public IObjectFactory getObjectFactory() {
    return new PowerMockObjectFactory();
  }
}
```

使用 powermock 来 mock 静态对象

```java
import org.powermock.core.classloader.annotations.PrepareForTest;
import org.testng.annotations.Test;

import static org.mockito.Mockito.spy;
import static org.mockito.Mockito.when;
// 注意此处引入的是 powermock 中的 mockStatic
import static org.powermock.api.mockito.PowerMockito.mockStatic;
import static org.testng.Assert.assertEquals;

@PrepareForTest(SessionUtil.class)
public class MainTest extends BaseMockTest {
  @Test
  void testDoFilter() {
    AdminFilter adminFilter = spy(AdminFilter.class);
    User user = new User();
    user.setUsername("Admin");
    mockStatic(SessionUtil.class);
    when(SessionUtil.getLoginUser()).thenReturn(user);
    assertEquals(adminFilter.doFilter("/manage"), "success");
  }
}
```
