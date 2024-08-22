# C++ STL算法

STL的算法可以分为九个种类，下面是一个大体的分类，详细内容可以去微软的官网查看学习

[https://learn.microsoft.com/zh-cn/cpp/standard-library/algorithm?view=msvc-170](https://learn.microsoft.com/zh-cn/cpp/standard-library/algorithm?view=msvc-170)



(1) 查找算法：判断容器中是否包含某个值

adjacent\_find

binary\_search&#x20;

count

count\_if

equal\_range&#x20;

find

find\_end

find\_first\_of

find\_if

lower\_bound

upper\_bound

search

search\_n



(2) 排序算法：提供元素排序策略

inplace\_merge

merge&#x20;

nth\_element&#x20;

partial\_sort&#x20;

partial\_sort\_copy

partition

random\_shuffle

reverse

reverse\_copy

rotate

rotate\_copy

sort

stable\_sort

stable\_partition



(3) 删除和替换算法

copy

copy\_backward

iter\_swap

remove

remove\_copy

remove\_if

remove\_copy\_if

replace

replace\_copy

replace\_if

replace\_copy\_if

swap

swap\_range

unique

unique\_copy



(4) 排列组合算法

next\_permutation

prev\_permutation



(5) 算数算法

accumulate

partial\_sum

inner\_product

adjacent\_difference



(6) 生成和异变算法

fill

fill\_n

for\_each

generate

generate\_n

transform



(7) 关系算法

equal

includes

lexicographical\_compare

max

max\_element

min

min\_element

mismatch



(8) 集合算法

set\_union

set\_intersection&#x20;

set\_difference&#x20;

set\_symmetric\_difference



(9) 堆算法

make\_heap

pop\_heap

push\_heap

sort\_heap





STL算法，常用算法函数

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

int main()
{
#if 0  // adjacent_find
    std::vector<int> ivec{ 1, 2, 3, 3, 4, 5 };
    // 查找一对相邻重复元素，找到则返回指向这对元素的第一个元素forwardIterator。
    // 否则返回最后一个元素的forwardIterator
    // 
    // auto iter = std::adjacent_find(ivec.begin(), ivec.end());
    // 也可以自己定义，相邻元素的规则
    auto iter = std::adjacent_find(ivec.begin(), ivec.end(), [](int elem1, int elem2) {
        return elem1 >= 4 && elem2 >= 4;  // 相邻元素都大于>4，说明是相邻元素
        });
    if (iter == ivec.end()) {
        std::cout << "container doesn't have adjacent element" << std::endl;
    }
    else {
        std::cout << std::distance(ivec.begin(), iter) << std::endl;
    }
#endif

#if 0  // sort, binary_search
    //std::vector<int> ivec{ 1, 2, 3, 3, 4, 5, 6 };
    //std::sort(ivec.begin(), ivec.end());
    // 在有序序列中查找value，找到返回true
    //std::cout << std::binary_search(ivec.cbegin(), ivec.cend(), 3) << std::endl;

    // 注意，如果二分查找算法，使用可调用对象的函数重载，那么要注意
    // 二分和排序的自定义规则要相同
    std::vector<int> ivec{ 1, 2, 3, 3, 4, 5, 6 };
    std::sort(ivec.begin(), ivec.end(), [](int elem1, int elem2) {
        return elem1 > elem2;
        });
    std::cout << std::binary_search(ivec.begin(), ivec.end(), 5, [](int elem1, int elem2) {
        return elem1 > elem2;
        }) << std::endl;
#endif



#if 0  // count, count_if
    std::vector<int> ivec{ 1, 2, 3, 3, 4, 5, 6 };
    // 利用等于操作符，把标志范围内的元素与输入值比较，返回相等元素个数
    std::cout << std::count(ivec.begin(), ivec.end(), 3) << std::endl;
    // 利用输入的操作符，对标志范围内的元素进行操作，返回结果为true的个数
    std::cout << std::count_if(ivec.begin(), ivec.end(), [](int elem) {
        return elem > 3;
        }) << std::endl;
#endif


#if 0  // find_end, find_first_of, find, find_if
    //// 查找一段序列，在当前序列的最后出现，返回最后出现的第一个迭代器
    //std::vector<int> ivec1{ 3, 4 };
    //std::vector<int> ivec{ 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4 };
    //auto iter = std::find_end(ivec.cbegin(), ivec.cend(), ivec1.cbegin(), ivec1.cend());
    //std::cout << std::distance(ivec.cbegin(), iter) << std::endl;


    //// 查找一段序列，在当前序列的首次出现，返回首次出现的第一个迭代器
    //std::vector<int> ivec1{ 3, 4 };
    //std::vector<int> ivec{ 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4 };
    //auto iter = std::find_first_of(ivec.cbegin(), ivec.cend(), ivec1.cbegin(), ivec1.cend());
    //std::cout << std::distance(ivec.cbegin(), iter) << std::endl;
    
    
    // 查找一段序列里value的第一次出现，返回迭代器
    //std::vector<int> ivec1{ 3, 4 };
    //std::vector<int> ivec{ 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4 };
    //auto iter = std::find(ivec.cbegin(), ivec.cend(), 3);
    //std::cout << std::distance(ivec.cbegin(), iter) << std::endl;


    // 查找一段序列里大于3且是奇数的第一次出现，返回迭代器
    //std::vector<int> ivec1{ 3, 4 };
    //std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8};
    //auto iter = std::find_if(ivec.cbegin(), ivec.cend(), [](int elem) {
    //    return elem > 3 && elem % 2;
    //    });
    //std::cout << std::distance(ivec.cbegin(), iter) << std::endl;

#endif


#if 0 // merge
    std::vector<int> ivec1{ 1, 3, 5, 7 };
    std::vector<int> ivec2{ 2, 4, 6, 8 };
    std::vector<int> ivec(8);

    std::sort(ivec1.begin(), ivec1.end());
    std::sort(ivec2.begin(), ivec2.end());

    std::merge(ivec1.cbegin(), ivec1.cend(), ivec2.cbegin(), ivec2.cend(), ivec.begin());
    for (auto elem : ivec) {
        std::cout << elem << std::endl;
    }
#endif


#if 0  // random_shuffle
    std::vector<int> ivec1{ 1, 2, 3, 4, 5, 6, 7, 8};
    std::random_shuffle(ivec1.begin(), ivec1.end());
    for (auto elem : ivec1) {
        std::cout << elem << std::endl;
    }
#endif


#if 0  // remove
    std::vector<int> ivec1{ 1, 2, 3, 4, 5, 6, 7, 8 };
    std::remove(ivec1.begin(), ivec1.end(), 3);
    for (auto elem : ivec1) {
        std::cout << elem << std::endl;
    }
#endif


#if 0  // unique
    // unique会去除重复元素，但是容器的size不会变，尾部会填充
    std::vector<int> ivec1{ 1, 2, 2, 3, 3, 4, 5, 6, 7, 8 };
    auto iter = std::unique(ivec1.begin(), ivec1.end());
    for (auto elem : ivec1) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;
    for (auto it = ivec1.begin(); it != iter; ++it) {
        std::cout << *it << " ";
    }
#endif


#if 1  // for_each
    std::vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7 };
    // 注意这里必须加引用，否则不会改变容器内的对象
    std::for_each(ivec.begin(), ivec.end(), [](int& elem) {
        ++elem;
        });

    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
#endif
    return 0;
}
```
