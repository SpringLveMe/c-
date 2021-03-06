```
// ConsoleApplication4.cpp: 定义控制台应用程序的入口点。
//

#include "stdafx.h"
#include <iostream>
#include <memory>
#include <vector>
#include <map>
#include <thread>
#include <Windows.h>
#include <Thread>

bool f() {
	std::cout << "f()" << std::endl;
	return false;

}

class A {
public:
	A(std::shared_ptr<std::string>& ptr) {
		m_ptr = ptr;
	}
	A(const A& other) {
		m_ptr = other.m_ptr;
	}
	void operator()() {
		static int count = 0;
		while (true) {
			Sleep(1000);
			if (count++ > 10) {
				return;
			}
		}
	}

private:
	std::shared_ptr<std::string> m_ptr;
};


int main()
{
	std::weak_ptr<std::string> wp;
	
	{
		std::shared_ptr<std::string> sp1(new std::string("hello!"));
		wp = sp1;
		// 这个对象自己会释放掉，它的copy最终会在一个线程上下文中！
		A work(sp1);
		std::thread t(work);
		t.detach();
	}
	Sleep(1000 * 8);
	std::shared_ptr<std::string> sp2 = wp.lock();
	if (!sp2) {
		std::cout << "up-ing failed!" << std::endl;
	}
	else {
		std::cout << "up-ing success!" << std::endl;
	}

	getchar();
    return 0;
}



```



##### 让出时间片,避免线程变成 while(1)这种
- 原始的处理方式是用clock_t 加 Sleep(20);
```
std::this_thread::yield() ;

```

##### 线程中休眠
```
std::this_thread::sleep_for()
```

##### =delete

```
#include <iostream>
using namespace std;

class DataOnly {
public:
    DataOnly () {}
    ~DataOnly () {}

    DataOnly (const DataOnly & rhs) = delete; //禁止使用该函数
    DataOnly & operator=(const DataOnly & rhs) = delete; //禁止使用该函数

    DataOnly (const DataOnly && rhs) {}
    DataOnly & operator=(DataOnly && rhs) {}
};

int main(int argc, char *argv[]) {
    DataOnly data1;
    DataOnly data2(data1); // error: call to deleted constructor of 'DataOnly'
    DataOnly data3 = data1; // error: call to deleted constructor of 'DataOnly'
    return 0;
}

```


##### = defaule
- 有时候我们重载了构造函数，但是又需要默认构造函数
```
struct Point {
    Point()=default;
    Point(int _x, int _y) : x(_x), y(_y) {}
    int x = 0;
    int y = 0;
}
```
