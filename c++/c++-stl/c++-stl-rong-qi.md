# C++ STL容器

## 工程实践



顺序容器：每个元素都有固定的位置，位置取决于插入时间和地点，与元素的值无关



vector：将元素置于一个动态数组中，可以随机存储元素（也就是用索引直接存取）

(1) 随机访问最好使用at，而不是\[]，因为在发生越界时，\[]越界直接assert报错，而at越界是抛出异常，可以使用try catch捕获异常

(2) 数组尾部添加或删除元素非常迅速。但在中部或头部就比较费时

(3) vector构造函数在VS中的快速查看技巧，直接在括号中加入()

注意：allocator参数一般是使用默认参数

(4) vector使用迭代器初始化，可以使用其他容器的迭代器，例如：deque，set，list等

(5) vector常用构造函数，vector(size\_t count)，vector(size\_t count, int val);

(6) vector增加函数，push\_back(), insert()

注意：insert返回值为指向新添加的第一个元素的迭代器

(7) vector删除函数，erase(), pop\_back(), clear()

注意，erase返回值为指向被删除元素后面的那个元素的迭代器

(8) vector遍历函数，注意几个点

1. at，\[]，front(), back()这些函数返回的都是元素引用，而不是值拷贝，修改后，自然会影响到容器内部的元素
2. 迭代器最基础的有 begin(), end()，还有对应的反向迭代器，和常量迭代器；如果vecotr存储的是常量对象，使用常量迭代器，如果想反向遍历，可以使用反向迭代器；

(9) vector 遍历函数，大小函数

1. size，capacity，max\_size分别代表，容器中的元素个数，当前容器不扩张的容量，容器最大容量
2. emptry() 判断容器是否为空

(10) vector swap函数，assign函数，resize函数

1. assign有三个函数重载，assign(initializer\_list), assign(size\_t count, int value), assing(const\_iter first, const\_iter end)；
2. resize函数，当参数 > 当前容器的size，后面补0；小于0的时候，截断；



deque："double end queue"的缩写，也就是双端队列。deque的实现相比于vector 有些复杂，但本质仍然是优化过的动态数组，只不过相比于单纯的动态数组，在 前面添加或删除元素非常快了。 可以随机存储元素。头部和尾部添加或删除元素都非常快（略慢与vector）。但在中间插入元素比较费时（和vector差不多）。



list：不能随机存取元素（也就是list无法用索引存取元素）。在任何位置插入和删除元素都比较迅速。（在任何位置插入删除元素的时间相同，在元素头部操作慢于deque， 在元素尾部操作慢于deque和vector）



string：没什么好说的，就是把普通字符串封装了一下



forward\_list：单项链表，简单来说就是受限的list，凡是list不支持的功能，它都不 支持。做各种支持的操作效率都会高于 list，最典型的就排序算法了，forword\_list 要优于list。

1. ForwordList 只提供前向迭代器，而不是双向迭代器。因此它也不支持反向迭代 器。&#x20;
2. ForwordList 不提供成员函数 size()。&#x20;
3. ForwordList 没有指向最末元素的锚点。基于这个原因，不提供用以处理最末元素的成员 back(), push\_back(), pop\_back()；







关联容器（associated container）：元素位置取决于元素的值，和插入顺序无关。&#x20;

set/multiset：使用“红黑树”实现，是一种高度平衡的二叉树，如果大家不了解红 黑树，可以去百度一下。了解个大概就可以了。二叉树的本质决定了 set/multiset 的元素存取值取决于元素本身的值，和插入顺序无关。 内部元素的值依据元素的值自动排列，与插入顺序无关。set内部相同数值的元素只能 出现一次，multiset 内部相同数值的元素可出现多次。容器用二叉树实现，便于查找。&#x20;



map/multimap：使用“红黑树”实现，是一种高度平衡的二叉树。 内部元素是成对的“key/value”，也就是“键值/实值”，内部元素依据其键值自动排 序，map内部相同的键值只能出现一次，multimap则可以出现多次。



无序式容器（unordered container）：&#x20;

unordered\_map/unordered\_multimap：使用“哈希表”实现的，由于哈希表的特性， 实现了真正的无序。如果不理解为什么使用“哈希表”就是真正无序的，可以去百 度一下“哈希表”，或者干脆直接记住就可以了。 使用方法也是“key/value”，和map/multimap类似。&#x20;

unordered\_set/unorder\_multiset：同样使用“哈希表”实现的。自然具有了哈希表 实现的容器的特点。 使用方法和setl/multiset 类似。



关联式容器和无序式容器的对比：&#x20;

(1) 关联式容器都是有序的，对于那些对顺序有要求的操作，关联式容器效率会高很多。（比如增加元素，删除元素）&#x20;

(2) 无序容器都是真正的无序，在查找数据方面有着优势。（比如修改特定元素，查找元素）&#x20;

(3) 从内存消耗的角度讲，无序容器要高于关联容器不过这并不重要。&#x20;

一句话来说，如果从这两类容器中选一个使用的话。如果是增加，删除元素比较频繁， 就使用关联式容器。如果修改元素，查找元素比较平凡，就使用无序容器。



我们在处理数据时应该选择什么容器呢？&#x20;

(1) 需要使用存储key/value的容器时，只能使用map/multimap/unoredered\_map/unordered\_multimap。

如果增加删除频繁，就使用map/multimap

修改，查找频繁，就使用unordered\_map/unoredered\_multimap。&#x20;

在真正的大型项目中，常常会对这两种容器进行测试，普通练习靠感觉就可以了&#x20;

(2) 在处理普通元素：&#x20;

当元素需要频繁插入删除时，选择顺序容器。

1. 如果在尾部插入删除，选择vector
2. 在头部，尾部插入删除，选择deque
3. 在中间插入，删除，选择list&#x20;

当元素需要频繁查找时，选择set/multiset/unorder\_set/unorder\_multiset。

频繁增加，删除时，选set；

频繁查找，修改时，选unordered\_set&#x20;



我们发现，对于普通元素，容器的选择不怎么容易判断。 其实在真正的大型项目中，要对各种容器进行测试的，普通练习一般选择vector或set 就可以了。这两个使用是比较频繁的





STL常用容器和基本操作

```cpp
// vector.c
#include <iostream>
#include <vector>
#include <set>
#include <deque>
#include <algorithm>


int main()
{
#if 0  // 随机访问最好使用at，而不是[]，因为在发生越界时，[]越界直接assert报错，而at越界是抛出异常，可以使用try catch捕获异常
    std::vector<int> ivec{ 1, 2, 3, 4, 5 };
    // 随机访问 不越界
    std::cout << ivec[1] << std::endl;
    std::cout << ivec.at(1) << std::endl;

    // 随机访问 越界 推荐使用at，可以抛出异常，而不是报错
    try {
        //std::cout << ivec[10] << std::endl;
        std::cout << ivec.at(10);
    }
    catch (const std::out_of_range& except) {
        std::cout << except.what() << std::endl;

    }
#endif

#if 0  // 数组尾部添加或删除元素非常迅速。但在中部或头部就比较费时
    std::vector<int> ivec{ 1, 2, 3, 4 ,5 };
    // 尾部增删快
    ivec.push_back(1);
    ivec.pop_back();

    // 头部和中间增删慢
    ivec.insert(++ivec.begin(), 10);
#endif

#if 0  // vector构造函数在VS中的快速查看技巧，直接在括号中加入()
    std::vector<int> ivec(())
#endif


#if 0  // vector使用迭代器初始化，可以使用其他容器的迭代器，例如：deque，set，list等 
    std::vector<int> ivec{ 1, 2, 3, 4, 5 };
    std::deque<int> ideque{ 1, 2, 3, 4, 5 };
    std::set<int> iset{ 1, 2, 3, 4, 5 };

    std::vector<int> ivec2(ideque.cbegin(), ideque.cend());
    for (int elem : ivec2) {
        std::cout << elem << std::endl;
    }

    std::cout << "====================" << std::endl;

    std::vector<int> ivec3(iset.cbegin(), iset.cend());
    for (int elem : ivec3) {
        std::cout << elem << std::endl;
    }
#endif

#if 0  // vector常用构造函数，vector(size_t count)，vector(size_t count, int val);
    //std::vector<int> ivec(10);
    std::vector<int> ivec(10, 2);
    for (int elem : ivec) {
        std::cout << elem << std::endl;
    }
#endif


#if 0  // vector增加函数，push_back(), insert()
    std::vector<int> ivec(10, 5);
    //ivec.push_back(20);
    //ivec.push_back(std::move(50));

    // insert返回值为指向新添加的第一个元素的迭代器
    //auto iter = ivec.insert(++ivec.cbegin(), 100);                 // 添加一个值
    //auto iter = ivec.insert(++ivec.begin(), { 1, 2, 3, 4, 5 });    // 添加一个初始化列表
    auto iter = ivec.insert(++ivec.begin(), 3, 40);                 // 添加count个value
    std::cout << std::distance(ivec.begin(), iter) << std::endl;

    for (int elem : ivec) {
        std::cout << elem << std::endl;
    }
#endif

#if 0  // vector删除函数，erase(), pop_back(), clear()
    std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8};
    // 返回指向被删除元素后面的那个元素的迭代器
    //auto iter = ivec.erase(ivec.begin());                   // 删除一个元素
    auto iter = ivec.erase(ivec.begin(), ivec.begin() + 3);   // 删除一段元素，注意左闭右开
    std::cout << *iter << std::endl;
    ivec.pop_back();
    ivec.clear();
    
    std::cout << "=================" << std::endl;
    
    

    for (int elem : ivec) {
        std::cout << elem << std::endl;
    }
#endif

#if 0  // vector遍历函数
    std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8 };
    // 注意这里返回的都是元素的引用
    //int& i = ivec.front();
    //i = 10;

    //int& i2 = ivec.back();
    //i2 = 20;

    //int& i3 = ivec.at(1);
    //i3 = 11;

    //int& i4 = ivec[2];
    //i4 = 12;


    //for (int elem : ivec) {
    //    std::cout << elem << std::endl;
    //}

    // 使用反向常量迭代器
    for (auto iter = ivec.crbegin(); iter != ivec.crend(); ++iter) {
        std::cout << *iter << std::endl;
    }
#endif

#if 0
    std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8 };
    std::cout << ivec.empty() << std::endl;
    std::cout << ivec.size() << std::endl;          // 返回当前容器中元素的个数
    std::cout << ivec.capacity() << std::endl;      // 返回当前容器不扩张所能容纳的最大元素数量
    std::cout << ivec.max_size() << std::endl;      // 返回当前机器可以存储的元素数量最大值
#endif


#if 0
    std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8 };
    std::vector<int> ivec2{ 1, 2, 3, 4, 5 };
    ivec.swap(ivec2);
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    ivec.assign({ 1, 2, 3 });
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    ivec.assign(5, 1);
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    ivec.assign(ivec2.begin(), ivec2.end());
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    ivec.resize(20);
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    ivec.resize(3);
    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
#endif

    return 0;
}


// deque.c
#include <iostream>
#include <deque>


int main()
{
    std::deque<int> ideque{ 1, 2, 3, 4 };
    ideque.push_front(0);
    ideque.push_back(5);

    for (auto elem : ideque) {
        std::cout << elem << std::endl;
    }
    return 0;
}



// list.c
#include <iostream>
#include <list>

int main()
{
    std::list<int> iList{ 1, 2, 3, 4, 5 };
    iList.push_back(6);
    iList.push_front(0);
    iList.insert(++iList.begin(), 10);
    
    for (auto elem : iList) {
        std::cout << elem << std::endl;
    }
    
    return 0;
}


// forward_list.c
#include <iostream>
#include <forward_list>

int main()
{
    std::forward_list<int> forList{ 1, 2, 3, 4, 5 };
    auto iter = forList.begin();
    ++iter;
    //--iter; // 迭代器不允许向后
    return 0;
}


// string.c
#include <iostream>
#include <string>

int main()
{
    const char* str1 = "hello world";
    std::string str2("hello world");

    std::cout << str2.c_str() << std::endl;
    std::cout << str2.data() << std::endl;
    return 0;
}
```
