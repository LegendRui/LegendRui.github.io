# gTest学习笔记

本文参考：https://blog.csdn.net/sevenjoin/article/details/89962344

## Ubuntu下安装与测试
### 安装
```
git clone https://github.com/google/googletest
cd googletest
mkdir build
cd build
cmake ..
make & make install
```

### 测试
1. 创建add.cc
```
#include <iostream>
  
int add(int a, int b)
{
        return a + b;
}
```

2. 创建testAdd.cc
```
#include <gtest/gtest.h>

extern int add(int a, int b);

TEST(testCase, test0)
{
    EXPECT_EQ(add(2, 3), 5);
}

int main(int argc, char **argv)
{
 testing::InitGoogleTest(&argc, argv);
 return RUN_ALL_TESTS();
}
```

3. 编译&运行
终端输入：
```
g++ add.cc testAdd.cc -o test -lgtest -lpthread
./test
```

显示结果为：
```
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from testCase
[ RUN      ] testCase.test0
[       OK ] testCase.test0 (0 ms)
[----------] 1 test from testCase (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.

```

***注：**编译时，也可以不写main函数，加入`-lgtest_main`开关。

## gTest用法
gTest提供若干和case方法进行测试不同的用例。主要常见的有TEST/TEST_F。在同一份TestCase中不能混用TEST和TEST_F。

TEST_F与TEST的区别在于：TEST_F会通过继承::testing::Test生成一个新的类，在新类中可以通过`void SetUp()`和`void TearDown()`进行创建和清除相关的资源数据。

### TEST宏
TEST宏的作用是创建一个简单的测试，它定义了一个测试函数，在这个函数里可以使用任何的C/C++代码并使用提供的断言。

语法：
```
TEST(test_case_name, test_name)
```
+ test_case_name：测试的用例名，通常是待测试函数名或类名
+ test_name: 测试名，可以随便取，但最好有意义
+ 测试完成后测试结果将以“测试用例名.测试名”显示

例：
```
#include <iostream>
#include <memory>
#include <gtest/gtest.h>

// #include <Memory.h>

// using namespace sampleCXX::common;

class Base {
public:
	Base(std::string name):m_name(name) {
		std::cout << "name: " << m_name << std::endl;
	}

	std::string getName() {
		return m_name;
	}

	~Base() {
		std::cout << "destroy base" << std::endl;
	}

private:
	std::string m_name;
};

void getNameFunc(std::shared_ptr<Base> base) {
	std::cout << __func__ << ":usercount:" << base.use_count() << std::endl;
	std::cout << __func__ << ":name:" << base->getName() << std::endl;
}

TEST(Base, createInstance) {
	std::unique_ptr<Base> instance(new Base("SvenBaseUnique"));
	// 测试创建的instance实例是否不为nullptr
	EXPECT_NE(instance, nullptr);
	instance.reset();
	// 测试instance实例是否为nullptr
	EXPECT_EQ(instance, nullptr);
}

TEST(Base, getName) {
	std::unique_ptr<Base> instance(new Base(("BaseUnique")));
	EXPECT_NE(instance, nullptr);
	auto name = instance->getName();
	EXPECT_STREQ(name.c_str(), "BaseUnique");
	instance.reset();
	EXPECT_EQ(instance, nullptr);
}

TEST(Base, share_ptr) {
	std::shared_ptr<Base> instance = std::make_shared<Base>("BaseShared");
	EXPECT_NE(instance, nullptr);
	std::cout << "shared_ptr.use_count:" << instance.use_count() << std::endl;
	EXPECT_EQ(1, instance.use_count());
	getNameFunc(instance);
	EXPECT_EQ(1, instance.use_count());
}



TEST(Base, unique_ptr) {
	std::unique_ptr<Base> instance(new Base("BaseUnique"));
	EXPECT_NE(instance, nullptr);
	getNameFunc(std::move(instance));
	EXPECT_EQ(instance, nullptr);
}
```

结果：
```
Running main() from /home/r/googletest/googletest/src/gtest_main.cc
[==========] Running 4 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 4 tests from Base
[ RUN      ] Base.createInstance
name: SvenBaseUnique
destroy base
[       OK ] Base.createInstance (0 ms)
[ RUN      ] Base.getName
name: BaseUnique
destroy base
[       OK ] Base.getName (0 ms)
[ RUN      ] Base.share_ptr
name: BaseShared
shared_ptr.use_count:1
getNameFunc:usercount:2
getNameFunc:name:BaseShared
destroy base
[       OK ] Base.share_ptr (0 ms)
[ RUN      ] Base.unique_ptr
name: BaseUnique
getNameFunc:usercount:1
getNameFunc:name:BaseUnique
destroy base
[       OK ] Base.unique_ptr (0 ms)
[----------] 4 tests from Base (0 ms total)

[----------] Global test environment tear-down
[==========] 4 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 4 tests.

```

### TEST_F宏
TEST_F一般用于针对多种不同的情况的测试TestCase使用一份相同的测试数据。即：使用相同的数据测试不同的行为。如果采用TEST宏进行测试那么将会为不同的测试case各创建一份数据，而TEST_F宏将会共用一份数据避免重复拷贝。

语法：
```
TEST_F(test_case_name, test_name)
```
+ test_case_name：测试用例名，**必须**是类名
+ test_name：测试名，可随便取
+ TEST_F必须继承`::testing::Test`类。该类提供了两个接口`void SetUp()`和`void TearDown()`
    +   void SetUp()：为测试准备对象
    + void TearDown()：测试后销毁对象资源

例：
```
#include <iostream>
#include <memory>
#include <gtest/gtest.h>


class Base {
public:
    Base(std::string name) : m_name(name) {
        std::cout << "Create constructor name: " << m_name << std::endl;
    }

    std::string getName() {
        return m_name;
    }

    void setName(const std::string &name) {
        m_name = std::string(name);
    }

    ~Base() {
        std::cout << "Destroy base" << std::endl;
    }

private:
    std::string m_name;
};

class BaseTest : public ::testing::Test {
protected:
    void SetUp() override {
        m_base = std::make_shared<Base>("SvenBaseTest");
    }

    void TearDown() override {
        m_base.reset();
    }

    std::shared_ptr<Base> m_base;
};

TEST_F(BaseTest, testCreateInstance) {
    std::unique_ptr<Base> instance(new Base("SvenbaseUnique"));
    EXPECT_NE(instance , nullptr);
    instance.reset();
    EXPECT_EQ(instance, nullptr);
}

TEST_F(BaseTest, testGetName) {
    auto name = m_base->getName();
    EXPECT_STREQ(name.c_str(), "SvenBaseTest");
}

TEST_F(BaseTest, testSetName) {
    m_base->setName("NewSvenName");
    auto name = m_base->getName();
    EXPECT_STREQ(name.c_str(), "NewSvenName");
}
```

测试结果：
```
Running main() from /home/r/googletest/googletest/src/gtest_main.cc
[==========] Running 3 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 3 tests from BaseTest
[ RUN      ] BaseTest.testCreateInstance
Create constructor name: SvenBaseTest
Create constructor name: SvenbaseUnique
Destroy base
Destroy base
[       OK ] BaseTest.testCreateInstance (0 ms)
[ RUN      ] BaseTest.testGetName
Create constructor name: SvenBaseTest
Destroy base
[       OK ] BaseTest.testGetName (0 ms)
[ RUN      ] BaseTest.testSetName
Create constructor name: SvenBaseTest
Destroy base
[       OK ] BaseTest.testSetName (0 ms)
[----------] 3 tests from BaseTest (0 ms total)

[----------] Global test environment tear-down
[==========] 3 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 3 tests.

```

## 断言分类
### Boolean断言
|致命断言|非致命断言|结果|
|:--:|:--:|:--:|
|ASSERT_TRUE(condition)|EXPECT_FALSE(condition)|condition为true|
|ASSERT_FALSE(condition)|EXPECT_FALSE(condition)|condition为false|

### 二元值断言
|致命断言|非致命断言|结果|
|:--:|:--:|:--:|
ASSERT_EQ(val1, val2)|EXPECT_EQ(val1, val2)|val1 == val2|
ASSERT_NE(val1, val2)|EXPECT_NE(val1, val2)|val1 != val2|
ASSERT_LT(val1, val2)|EXPECT_LT(val1, val2)|val1 < val2|
ASSERT_LE(val1, val2)|EXPECT_LE(val1, val2)|val1 <= val2|
ASSERT_GT(val1, val2)|EXPECT_GT(val1, val2)|val1 > val2|
ASSERT_GE(val1, val2)|EXPECT_GE(val1, val2)|val1 >= val2|

### 字符串断言
|致命断言|非致命断言|结果|
|:--:|:--:|:--:|
|ASSERT_STREQ(val1, val2)|EXPERT_STREQ(val1, val2)| val1 == val2|
|ASSERT_STRNE(val1, val2)|EXPERT_STRNE(val1, val2)| val1 != val2|
|ASSERT_STRCASEEQ(val1, val2)|EXPERT_STRCASEEQ(val1, val2)| val1 == val2（忽略大小写）|
|ASSERT_STRCASENE(val1, val2)|EXPERT_STRCASENE(val1, val2)| val1 == val2（忽略大小写）|

## GMock用法
// TO DO