---
title: "查找中点"
date: "2015-05-02 13:01"
categories：
    -algorithm
tags:
    
---

有这么一道面试题：
> 找出未知单链表中点元素。


市面上有2种方法：
1. 先遍历算出总长度为length，再遍历length/2个元素。
2. 使用快慢指针。快指针步长为2，慢指针为1。快指针到达链表末端时，慢指针正好为中点元素。

然而这2种方法本质上没有差别。

###废话不说先上结果：

> 0.00027776 s : find_mid   循环法  
> 0.000276798 s : find_mid_1  双指针  
> 2.81981 s : find_mid			提高指针取得下一个元素的代价 
> 2.81391 s : find_mid_1

```
#include <iostream>
#include <thread>
#include <sys/timeb.h>
#include <time.h>
#include <windows.h>
using namespace std;
class list {
private:
	list *next = nullptr;
	
public:
	int m_d;
	list* add(list* p) { next = p; return p;  }
	list* get_next() { Sleep(delay); return next; }
	static int delay;
};
int list::delay = 0;
list* find_mid(list* ptr)
{
	int len = 0;
	list *p_start = ptr;
	while (true)
	{
		if (!p_start)
			break;
		len++;
		p_start = p_start->get_next();
	}
	len /= 2; p_start = ptr;
	while (len--)
	{
		p_start = p_start->get_next();
	}
	return p_start;
}
list* find_mid_1(list* ptr)
{
	list *p_s1 = ptr, *p_s2 = ptr;
	while (p_s2=p_s2->get_next())
	{
		p_s2 = p_s2->get_next();
		if (!p_s2)
			break;
		p_s1 = p_s1->get_next(); 
	}
	return p_s1;
}
#define cal_time(fun,ptr)	{\
	QueryPerformanceCounter(&startCount);\
	fun(ptr);\
	QueryPerformanceCounter(&endCount);\
	double elapsed = (double)(endCount.QuadPart - startCount.QuadPart) / freq.QuadPart;\
	cout << elapsed<<" s : "<< #fun   << endl;}
int main()
{
	int c = 1*1000;
	list * start = new list,*p;
	p = start;
	while (c--)
	{
		p = p->add(new list);
		p->m_d = c;
	}
	LARGE_INTEGER startCount;
	LARGE_INTEGER endCount;
	LARGE_INTEGER freq;
	QueryPerformanceFrequency(&freq);
	_ASSERT(find_mid_1(start) == find_mid(start));
	cal_time(find_mid, start);
	cal_time(find_mid_1, start);
	list::delay = 1;

	cal_time(find_mid, start);
	cal_time(find_mid_1, start);
    return 0;
}
```
#分析
其实这2种方法都是O(n),使用大O表示法不够精确，这种简单的算法直接数一下next的调用次数就可以知道都是1.5*len个get_next().find_mid_1 唯一的优势就是少用了一个 int len。所以性能提升实在少的可怜。
#下面给出正确的方案
思路：其实也是用到了2个指针，其中一个走过的路程是另一个的1/2。但千万不要使用一个+1另一个+2。而是在前一个指针遍历的时候保存下来给另一个指针。

##原理：
虽然我们不能预知链表的长度，但是我们可以**预期**。

当**前一个指针**移动了n步时，将这个值赋值给**后一个指针**，然后**预期**前一个指针能访问到2n,如果**预期达成**，那么**继续开展预期**。如果**预期失败**，那么中点为上一次预期成功的点再移动 c/2步。

##分析：
**上一次预期成功的点**就是节约计算的关键。

最好情况为：预期成功了，然后链表也到底了。

最差情况为：链表长度为2^n-1。**上一次预期成功的点**为(2^(n-2))然后还要再走(2^(n-2))步。

当然如果增加期望点，性能还有望提升。
```
list* find_mid_2(list* ptr)
{
	list *p_s1 = ptr, *p_s2 = ptr, *tmp= ptr;
	int d = 1,t=1;
	int i = 0;
	while (true)
	{
		
		tmp = p_s2;
		for (int c=0; i < d; i++,c++)
		{
			p_s2 = p_s2->get_next();
			if (!p_s2)
			{
				int len = c / 2;
				while (len--)
					{
						p_s1 = p_s1->get_next();
					}
				return p_s1;
			}
		}
		d *= 2;
		p_s1 = tmp;
	}
	return p_s1;
}
```
来看效果吧

>最好情况
> 0.608294 s : find_mid 
0.612391 s : find_mid_1
0.404288 s : find_mid_2
> 
> 最差情况
> 0.604105 s : find_mid
0.600299 s : find_mid_1
0.502341 s : find_mid_2

我估算需要(1,1.25)n个get_next()操作。

```
#include <iostream>
#include <thread>
#include <sys/timeb.h>
#include <time.h>
#include <windows.h>
#include <list>
#include <queue>
#include <map>
using namespace std;
typedef list<int> mlist;
#define cal_time(fun,ptr)	{\
	QueryPerformanceCounter(&startCount);\
	fun(ptr);\
	QueryPerformanceCounter(&endCount);\
	double elapsed = (double)(endCount.QuadPart - startCount.QuadPart) / freq.QuadPart;\
	cout << elapsed<<" s : "<< #fun   << endl;}
class stage {
public:
	mlist* msrc;
	map<int, mlist::iterator, std::greater<int> > m_map;
	stage(mlist& m)
	{
		msrc = &m;
	}
	mlist::iterator operator[](int n)
	{
		if (n == 0)
			return mlist::iterator();
		map<int, mlist::iterator, std::greater<int> >::iterator itr = m_map.find(n);
		
		if (itr != m_map.end())
		{
			return itr->second;
		}
		else
			return operator[](n/2);
	}
};

mlist::iterator find_mid_2(mlist ptr) {
	stage probe_ptr(ptr);
	auto& m_map = probe_ptr.m_map;
	auto itr = ptr.begin();
	m_map[1] = itr;
	int len = 1;
	while (itr!=ptr.end())
	{
		auto m_itr = m_map.begin();
		int top_key=m_itr->first;
		while (m_itr!=m_map.end())
		{
			if(m_itr->first<top_key/2)
				break;
			if(len==2*top_key)
				m_map[len]=itr;
			m_itr++;
		}
		
		itr++; len++;
	} 
	for (auto effect:m_map)
	{
		int k = effect.first;
		if (m_map.find(k) != m_map.end())
		{
			k = k / 2;
			auto mid = m_map[k]; 
			while (k++<len/2)
			{
				mid++;
			}
			cout << "res:" << *mid << endl;
			return mid;
		}
	}
}
int main()
{
	int c = 1 * 10;
	mlist  start;
	while (c--)
	{
		start.push_back(c);
	}
	LARGE_INTEGER startCount;
	LARGE_INTEGER endCount;
	LARGE_INTEGER freq;
	QueryPerformanceFrequency(&freq);

	cal_time(find_mid_2, start);
	return 0;
}
```