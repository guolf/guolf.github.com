---
layout: post
title: Android单元测试详解（1）——— AndroidJunitTest
author: BeautifulSoup
date: 2016-3-6
categories: blog
tags: [Development,Android]
description: 本篇博客主要分析目前主流的Android测试框架AndroidJunitTest
---
# 背景
最近稍等空闲，考虑到我们项目测试覆盖率几乎等于0，因此痛下决心决定研究下Android单元测试。

# 单元测试的类型
首先我们需要明确安卓的单元测试主要分为两种类型：

* 在开发主机Java虚拟机上运行的Junit Test
* 在Android真机或者虚拟机上运行的Instrumented unit tests

因为Junit Test免去了将apk包安装到android设备上的步骤，因此速度上会比Instrumented unit tests快很多。但是跟android相关的一些库只能运行在android设备上，而无法再本机Java虚拟机上运行，比如很多包名中带android的库。因此我们这里需要做一个选择，如果某些类没有用到android相关的库，则完全可以使用Junit Test，加快测试速率。

如果是在Android Studio上开发，则文件结构应该是这样的，
![单元测试文件结构](https://img.alicdn.com/imgextra/i1/754328530/TB2VoYRlXXXXXc7XXXXXXXXXXXX-754328530.png)
其中androidTest包中存放的是Instrumented unit tests的文件，而test中则是Junit Test，这两个文件夹在项目创建的时候AS都会帮我们创建好。可能细心的同学已经发现了，test文件夹中的文件都不能运行。
如图，我们需要将Test Artiface改为Unit Test
![选择测试类型](https://img.alicdn.com/imgextra/i1/754328530/TB2JdHIlXXXXXarXpXXXXXXXXXX-754328530.png)
另外这两种测试都只有在debugable = true的编译类型的时候才能运行。
了解了单元测试的两个基本类型，接下来让我们来了解下目前主流的单元测试框架或者说工具。

# AndroidJunit
Junit是Java开发中应用最多的单元测试框架，目前已经到Junit4。在Android的单元测试中我们同样可以使用它，如果是Junit Test，可以直接使用Java的Junit4。而如果测试的类必须使用Instrument Test，则我们需要使用AndroidJunitTest
首先在build.gradle文件中的defaultConfig中加入如下设置，设置使用的Instrument test的runner 为AndroidJunitRunner。

```
testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
```
然后在androidTest的java文件夹中创建测试文件

```java
@RunWith(AndroidJUnit4.class)
@LargeTest
public class MainActivityExprssoTest {


    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = new ActivityTestRule<MainActivity>(MainActivity.class);


    @Before
    public void init(){
        Log.d("MainActivity test", "init test MainActivity");
    }
    @Test
    public void testOnBtnClick(){
        onView(withId(R.id.text_btn1))
                .perform(click()) ;
        Assert.assertTrue(mActivityRule.getActivity().getKey());
    }
    
    @After
    public void destroy(){
    }


}
```
我们可以在@Before 中做一些初始化操作，在@After中做一些单元测试结束前需要做的一些工作。而带@Test的方法则是具体的测试方法。AndroidJunitRunner会逐个运行这些方法。关于其他的一些anntotation其实跟Junit中是一样的，具体可以参考[Android官方文档](http://developer.android.com/intl/zh-cn/tools/testing-support-library/index.html#AndroidJUnitRunner)。

另外以前做过Android Activity单元测试的同学可能会发现上面的代码跟你写的有些不一样。Corrcet，之前的单元测试类如果是对某个Activity做测试需要继承自ActivityInstrumentationTestCase2<CalculatorActivity>，传入需要测试的Activity类型作为模板测试。目前该类型已被deprectd，而使用一个ActivityTestRule，作为模板参数的Activity会在@Before方法之前被launch，在@After之后被停止运行，在这之间我们可以去调用它的一些方法。

如果你不需要对Activity做测试，但是需要访问一些图片等resource资源，则需要让你的测试类继承自AndroidTestCase，这样你就可以通过getContext()获取应用的上下文了。

ok，本期对AndroidJunitTest的介绍就到这里。下一期我们将会介绍界面测试框架Expresso，敬请期待。