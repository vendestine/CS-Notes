# C++ Debug

### 构造函数没有定义在public作用域下，导致无法创建对象

这个很容易理解，我们在类的外部创建对象，如果构造函数不是public权限，那么就无法调用构造函数，创建对象失败；`报错:"Test2::Test2() 不可访问`



### [Error: C++ requires a type specifier for all declarations](https://stackoverflow.com/questions/28162788/error-c-requires-a-type-specifier-for-all-declarations)

这种错误一般是没有相应的返回值
