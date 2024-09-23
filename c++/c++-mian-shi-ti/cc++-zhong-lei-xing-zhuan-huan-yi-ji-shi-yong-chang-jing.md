# C/C++中类型转换以及使用场景

<figure><img src="../../.gitbook/assets/c_c++ 中强制类型转换使用场景.png" alt=""><figcaption></figcaption></figure>



一定要记住总结：

(1) 去掉对象指针或对象引用 使用const\_cast

(2) 基本类型转换 使用 static\_const

(3) 运行时类型转换，常用于多态，使用dynamic\_cast

(4) 不同类型的指针之间进行转换使用 reinterpret\_cast

