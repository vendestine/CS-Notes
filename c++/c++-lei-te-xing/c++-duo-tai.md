# C++ 多态

## 多态和虚函数

多态的作用：一句话概括，父类指针 调用 子类的成员函数

(1) 多态是面向对象的第三大特点：多态；C++中使用虚函数来实现多态；&#x20;

(2) 父类指针可以指向子类对象，这个是自然而然的，因为子类对象的内存前面就是父类成员，类型完全匹配&#x20;

(3) 当父类指针指向子类对象，且子类重写父类某一函数时，父类指针调用该函数，就会产生以下的可能&#x20;

1. 该函数为虚函数：父类指针调用的是子类的成员函数。&#x20;
2. 该函数不是虚函数：父类指针调用的是父类的成员函数。



虚函数实现多态&#x20;

(1) 动态绑定和静态绑定:

静态绑定：程序在编译时就已经确定了函数的地址，比如<mark style="background-color:orange;">非虚函数就是静态绑定</mark>

动态绑定：<mark style="background-color:orange;">程序在编译时确定的是程序寻找函数地址的方法，只有在程序运行 时才可以真正确定程序的地址，比如虚函数就是动态绑定</mark>&#x20;

(2) 虚函数是如何实现动态绑定的呢

每个有虚函数的类都会有一个虚函数表，对象的内存开头会存放一个指向虚函数表的指针， 编译时编译器只告诉了程序会在运行时查找虚函数表的对应函数。每个类都会有自己的虚函数表，所以当父类指针引用的是子类虚函数表时，子类又重写了虚函数，自然调用的就是子类的函数



## 注意事项

(1) <mark style="background-color:orange;">子父类的虚函数必须完全相同</mark>，为了防止开发人员将函数名写错，C++11 添加了override 关键字，如果不匹配，在编译阶段就会报错；

(2) <mark style="background-color:orange;">父类的析构函数必须为虚函数</mark>：当父类指针指向子类对象时，如果父类没有使用虚析构函数，那么最后只会调用父类的析构函数，导致独属于子类的内存泄露

(3) 重写虚函数时，父类虚函数的virtual关键字必须保留，子类虚函数的virtual关键字可以省略



## 多态 相关代码

```cpp
#include <iostream>
#include <string>


class Person
{
public:
    Person() { std::cout << "Person()" << std::endl; }
    Person(std::string name_, int age_) :name(name_), age(age_) { std::cout << "Person(para)" << std::endl; }
    virtual ~Person() { std::cout << "~Person()" << std::endl; }
    virtual void showInfo()const
    {
        std::cout << "Person::showInfo()" << std::endl;
    }
    void output()const
    {
        std::cout << "Person::output()" << std::endl;
    }
    virtual void input()const
    {
        std::cout << "Person::intput()" << std::endl;
    }

protected:
    std::string name;
    int age;
};


class Teacher : public Person
{
public:
    Teacher() { std::cout << "Teacher()" << std::endl; }
    Teacher(std::string name_, int age_, int tId_) : tId(tId_), Person(name_, age_) { std::cout << "Teacher(para)" << std::endl; }
    ~Teacher() { std::cout << "~Teacher()" << std::endl; }
    void showInfo()const override
    {
        std::cout << "Teacher::showInfo()" << std::endl;
    }
    void output()const
    {
        std::cout << "Teacher::output()" << std::endl;
    }

protected:
    int tId;
};


class Student : public Person
{
public:
    Student() { std::cout << "Student()" << std::endl; }
    Student(std::string name_, int age_, int sId_) : Person(name_, age_), sId(sId_) { std::cout << "Student(para)" << std::endl; }
    ~Student() { std::cout << "~Student()" << std::endl; }
    void showInfo()const override
    {
        std::cout << "Student::showInfo()" << std::endl;
    }
    //void input()const override
    //{
    //    std::cout << "Student::intput()" << std::endl;
    //}

protected:
    int sId;
};


void test1(Person* person)
{
    person->showInfo();
    delete person;
}

int main()
{
    // 多态举例
    test1(new Teacher());
    std::cout << "=====================" << std::endl;

    Person* person1 = new Student("lisi", 20, 1);
    person1->showInfo();
    delete person1;

    std::cout << "=====================" << std::endl;
    // 调用的非虚函数，静态绑定，子类虽然重写了函数，但是还是调用父类的函数
    Person* person2 = new Teacher();
    person2->output();
    delete person2;

    std::cout << "=====================" << std::endl;
    // 调用的虚函数，动态绑定，但是因为子类没有重写虚函数，所以还是调用父类的虚函数
    Person* person3 = new Student();
    person3->input();
    delete person3;
}



```
