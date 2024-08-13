# C++ 继承

## 继承 作用

C++继承介绍：C++非继承的类相互是没有关联性的，假设现在需要设计医生，教师，公 务员三个类，需要定义很多重复的内容而且相互没有关联，调用也没有规律。如果这还算好， 那一个游戏有几千件物品，调用时也要写几千个函数。这太要命了。于是继承能力就应运而生了



<mark style="background-color:orange;">C++继承原理：C++的继承可以理解为在创建子类成员变量之前先创建父类的成员变量， 实际上，C 语言就是这么模仿出继承功能的</mark>



## 继承注意事项

(1) <mark style="background-color:orange;">C++子类对象的构造过程：先调用父类的构造函数，再调用子类的构造函数</mark>，也就是说先初始化父类的成员，再初始化子类的成员；<mark style="background-color:orange;">C++子类对象的析构过程：先调用子类的析构函数，再调用父类的析构函数</mark> 如何理解：只要明白<mark style="background-color:orange;">子类在内存上其实就相当于把父类的成员变量放在子类的成员变量前面罢了，构造和析构过程也是为了这个机制而设计的</mark>

(2) 若父类没有默认的构造函数，子类的构造函数又未调用父类的构造函数，则无法编译

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>父类没有默认构造函数</p></figcaption></figure>

## 继承 相关代码

```cpp
#include <iostream>
#include <string>


class Person
{
public:
    Person() { std::cout << "Person()" << std::endl; }
    Person(std::string name_, int age_) :name(name_), age(age_) { std::cout << "Person(para)" << std::endl; }
    ~Person() { std::cout << "~Person()" << std::endl; }

    std::string name;
    int age;
};


class Teacher: public Person
{
public:
    Teacher(){ std::cout << "Teacher()" << std::endl; }
    Teacher(std::string name_, int age_, int tId_) : tId(tId_), Person(name_, age_) { std::cout << "Teacher(para)" << std::endl; }
    ~Teacher() { std::cout << "~Teacher()" << std::endl; }

    int tId;
};


class Student: public Person
{
public:
    Student(){ std::cout << "Student()" << std::endl; }
    Student(std::string name_, int age_, int sId_) : Person(name_, age_), sId(sId_) { std::cout << "Student(para)" << std::endl; }
    ~Student() { std::cout << "~Student()" << std::endl; }


    int sId;
};


int main()
{
    Teacher* t1 = new Teacher();
    delete t1;

    Student* s1 = new Student();
    delete s1;

    std::cout << "====================" << std::endl;

    Teacher t2("teahcer2", 30, 1);
    Student s2("student2", 15, 2);
    return 0;
}



```
