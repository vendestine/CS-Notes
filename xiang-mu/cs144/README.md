# CS144

### 调试方法

我自己的开发环境是 VSCode + ubuntu22.04，所以调试我使用的是VSCode + gdb调试



#### vscode + gdb调试

(1) VSCode的调试是针对工作区而言的，我们首先在工作区创建launch.json文件

```
ctrl + shift + d       # 快捷键 打开调试界面
create launch.json     # 创建调试配置文件
```



(2) 配置launch.json文件

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "sponge debug",//!挑个容易识别的名字
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/tests/${fileBasenameNoExtension}", //!设置为测试程序源码相对应的目标程序路径
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            //"preLaunchTask": "C/C++: g++-8 build active file",  //!不需要前置任务
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```



(3) 开始调试

CS144的测试文件都存放在 /tests 下，如果哪个测试用例失败了，我们直接去该测试文件中打断点，然后F5开始调试。



调试参考：

(1) [https://blog.csdn.net/zztiger123/article/details/105544640](https://blog.csdn.net/zztiger123/article/details/105544640)

(2) [https://www.cnblogs.com/sheephuan/p/18135136](https://www.cnblogs.com/sheephuan/p/18135136)

(3) [https://www.cnblogs.com/kangyupl/p/stanford\_cs144\_labs.html](https://www.cnblogs.com/kangyupl/p/stanford\_cs144\_labs.html)

(4) AI工具 prompt： vscode gdb调试







## Reference

> 观点：关于CS144项目，网上有很多lab的讲解和代码；但是其实看很多的讲解意义不大，因为lab本身就没有标准答案，而且看过多的讲解还会使自己陷入一种”寻找最好资料或方法”的困境，难以专注于lab本身。所以项目参考在精不在多，选择一个主要参考即可；其他的参考都是配合主要参考进行更好的理解和优化；
>
>
>
> 结论：选择一个主要参考，其他的参考都是基于主要参考的扩展和优化。



主要参考：

(1) LRL52 CS144博客  [https://lrl52.top/category/cs144/](https://lrl52.top/category/cs144/)

(2) LRL52 CS144讲解视频  [https://space.bilibili.com/697699241](https://space.bilibili.com/697699241)

(3) LRL52 CS144 github  [https://github.com/LRL52/CS144-Sponge](https://github.com/LRL52/CS144-Sponge)

(4) 官方lab manual  [https://github.com/LRL52/CS144-Sponge/tree/master/labs\_pdf\_21](https://github.com/LRL52/CS144-Sponge/tree/master/labs\_pdf\_21)

(5) CS144's user-space TCP library[https://cs144.github.io/doc/lab0/index.html](https://cs144.github.io/doc/lab0/index.html)



其他参考：

(1) pku大神的CS144 github实现非常简洁  [https://github.com/PKUFlyingPig/CS144-Computer-Network](https://github.com/PKUFlyingPig/CS144-Computer-Network)

(2) 全英文讲解和代码，代码设计非常优雅 &#x20;

[https://luolibrary.com/categories/CS144/](https://luolibrary.com/categories/CS144/)      [https://github.com/shejialuo/CS144](https://github.com/shejialuo/CS144)

(3) 阿苏的CS144讲解，讲解角度比较照顾小白，切入角度不错 [https://space.bilibili.com/16765968/channel/collectiondetail?sid=696216](https://space.bilibili.com/16765968/channel/collectiondetail?sid=696216)
