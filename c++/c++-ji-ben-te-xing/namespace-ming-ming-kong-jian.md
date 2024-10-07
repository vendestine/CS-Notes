# namespace 命名空间

C++经常需要多个团队合作来完成大型项目。多个团队就常常出现起名重复的问题，C++ 就提供了命名空间来解决这个问题；



问题演示：TeamA和TeamB是两个团队，TeamA有代码文件，TeamA.h TeamA.cpp; TeamB有代码文件 TeamB.h TeamB.cpp; 两个团队都定义了test函数，然后main.cpp中引入了这两个团队的头文件；这样在main.cpp中调用test()就会报错；

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<div align="left">

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>



解决方法：每个团队使用各自的命名空间，例如TeamA团队就使用namespace A，而TeamB团队就使用namespace B；然后把团队自己的头文件和源文件都使用namespace包裹住；

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

<div align="left">

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

</div>



对于namespace，我们还可以使用using关键字；using关键字设计的目的之一就是为了<mark style="background-color:orange;">简化命名空间</mark>的；



using 关键字在命名空间方面主要有两种用法：

using 命名空间::变量名；这样以后使用此变量时只要使用变量名就可以了，可以少写命名空间

注意：如果<mark style="background-color:orange;">变量前本来就有命名空间，那么直接使用当前的命名空间</mark>；否则就会使用using里的命名空间；<mark style="background-color:orange;">如果有多个using针对的是相同的变量名，还是会因为无法确定报错；</mark>

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

using namspce 命名空间。这样每一个变量都会在该命名空间中寻找

注意：如果变量前本来就有命名空间，那么直接使用当前的命名空间；否则就会去using里的命名空间里寻找；<mark style="background-color:orange;">如果有多个using namespace，使用的变量又在多个namespace里同名，还是会因为无法确定报错；</mark>

<div align="left">

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

</div>

注意事项：

命名空间的实现原理，C++最后都要转化为 C 来执行程序。在 namespace A 中定义的 Test 类，其实全名是 A::Test。 C++所有特有的库（指 c 没有的库）,都使用了 std 的命名空间。比如最常用的 iostream



头文件中一定不能使用using关键字，因为这样极容易导致命名空间的污染

分析：如果在头文件中使用using 关键字，很有可能在cpp文件中调用一个名称的时候，不知道究竟属于哪一个命名空间（因为头文件可以嵌套include，很难确定使用了哪些命名空间），当文件多的时候排查很麻烦；cpp文件中是可以使用的，因为可以直接确定使用了哪些命名空间，相对来说好排查一些；

