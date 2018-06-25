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
