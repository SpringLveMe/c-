##### c++的lambda无法形成闭包机制！
```
#include "stdafx.h"

#include <iostream>
#include <functional>
#include <Thread>

using namespace std;

typedef void(*fun)(int&);

void test1() {
	// 一：相当于值传递
	int a = 123;
	auto f = [a] { cout << a << endl; };
	a = 321;
	f(); // 输出：123
}

class A {
public:
	std::function< void(void) > myfunc;
	void operator()() {
		myfunc();
	}
};

A ma;

void test2() {
	// 二：一般引用&
	int a = 123;
	auto f = [&a] { cout << a << endl;a = 10000; };
	//a = 321;
	f(); // 输出：321
	cout << a << std::endl;
	ma.myfunc = f;
}

int main()
{
	test2();

	ma();//输出未知数据，因为test2 栈上的a早已被释放掉了！
	//std::thread t(ma);
	//t.detach();

	getchar();
    return 0;
}



```
