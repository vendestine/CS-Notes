# CS144

## 搭建实验环境

### 使用官方镜像 + Virtual Box

这里推荐用官方镜像 + virtual Box，我lab0-lab4使用的是自己的ubuntu22.04 + vmware，但是到了lab4，有一些test始终无法通过，最后结合网上的讨论，应该是环境问题，所以建议一开始就使用官方镜像 + virtual box配置环境

<div align="left">

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

</div>

上面的四个选项都来自于 [https://stanford.edu/class/cs144/vm\_howto/](https://stanford.edu/class/cs144/vm\_howto/)\
我们选择第一个[https://stanford.edu/class/cs144/vm\_howto/vm-howto-image.html](https://stanford.edu/class/cs144/vm\_howto/vm-howto-image.html) 然后就按照教程走就行。



需要注意的几个点

(1) 一定要确认端口转发，localhost的2222端口转发到22端口；vmware有的版本没有端口转发的功能，所以我们最好使用virtual box。设置完后，ssh -p 2222 cs144@localhost，检验是否设置成功。\
[https://stanford.edu/class/cs144/vm\_howto/vm-howto-iso.html](https://stanford.edu/class/cs144/vm\_howto/vm-howto-iso.html) 具体看Set up SSH port forwarding to your VM这一个section。

(2) 最好修改虚拟机的cpu和内存配置，官方设置为1h1g，远程vscode连接经常卡死，所以至少使用2h2g。

(3) 镜像导入好后，用户名和默认密码都是cs144，第一次输入密码后，会提示修改密码

(4) 登陆后，先检查已经安装的包   apt list --installed | less\
[https://stanford.edu/class/cs144/vm\_howto/vm-howto-iso.html](https://stanford.edu/class/cs144/vm\_howto/vm-howto-iso.html)Install the required packages和 [https://stanford.edu/class/cs144/vm\_howto/vm-howto-byo.html](https://stanford.edu/class/cs144/vm\_howto/vm-howto-byo.html)\
主要就是看这两个地方里提到的包是否安装。



### clone远程仓库

如果你做的是最新的lab，那么直接clone官方仓库即可，这样就可以把官方远程仓库的所有分支拉取下来，之后我们每做一个lab，就merge对应的origin/labx-starter分支就行。



但是由于我做的是sponge版本的lab，sponge版本的lab，官方已经关闭了。所以我们只能曲线救国。

(1) 拉取别人的sponge仓库，然后创建同名本地分支和远程分支一一对应

(2) 将本地的所有分支，上传到自己的远程仓库，此时我们的远程仓库，相当于一个“官方”的远程仓库。

(3) 之后就是正常开发，此时我们的远程仓库 既是“官方”仓库又是我们自己的远程仓库。

具体流程请看lab0的搭建环境 section。

注意：如果是中途更换环境，那么我们直接clone自己的远程仓库，接着开发就行。



### 配置vscode远程开发

我个人习惯使用vscode远程开发，步骤也很简单。

(1) 打开remote explorer，add new remote

<div align="left">

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

</div>

添加之后，会在ssh config文件里看到该主机的信息，我们可以修改Host信息，方便自己之后找到想要的虚拟主机，其余的信息不能更改！

<div align="left">

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

</div>

之后为了方便开发，给远程主机下载一些常用的插件，比如c++ extension pack, clangd, git graph.

&#x20;



## 调试方法

我自己的开发环境是 VSCode + ubuntu22.04，所以调试我使用的是VSCode + gdb调试

### vscode + gdb调试

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



(4) 调试参考：

\[1] [https://blog.csdn.net/zztiger123/article/details/105544640](https://blog.csdn.net/zztiger123/article/details/105544640)

\[2] [https://www.cnblogs.com/sheephuan/p/18135136](https://www.cnblogs.com/sheephuan/p/18135136)

\[3] [https://www.cnblogs.com/kangyupl/p/stanford\_cs144\_labs.html](https://www.cnblogs.com/kangyupl/p/stanford\_cs144\_labs.html)

\[4] AI工具 prompt： vscode gdb调试







## 项目参考

> 观点：关于CS144项目，网上有很多lab的讲解和代码；但是其实看很多的讲解意义不大，因为lab本身就没有标准答案，而且看过多的讲解还会使自己陷入一种”寻找最好资料或方法”的困境，难以专注于lab本身。所以项目参考在精不在多，选择一个主要参考即可；其他的参考都是配合主要参考进行更好的理解和优化；
>
>
>
> 结论：选择一个主要参考，其他的参考都是基于主要参考的扩展和优化。



### 主要参考

(1) LRL52 CS144博客  [https://lrl52.top/category/cs144/](https://lrl52.top/category/cs144/)

(2) LRL52 CS144讲解视频  [https://space.bilibili.com/697699241](https://space.bilibili.com/697699241)

(3) LRL52 CS144 github  [https://github.com/LRL52/CS144-Sponge](https://github.com/LRL52/CS144-Sponge)

(4) 官方lab manual  [https://github.com/LRL52/CS144-Sponge/tree/master/labs\_pdf\_21](https://github.com/LRL52/CS144-Sponge/tree/master/labs\_pdf\_21)

(5) CS144's user-space TCP library[https://cs144.github.io/doc/lab0/index.html](https://cs144.github.io/doc/lab0/index.html)



### 其他参考

(1) pku大神的CS144 github实现非常简洁  [https://github.com/PKUFlyingPig/CS144-Computer-Network](https://github.com/PKUFlyingPig/CS144-Computer-Network)

(2) 全英文讲解和代码，代码设计非常优雅 &#x20;

[https://luolibrary.com/categories/CS144/](https://luolibrary.com/categories/CS144/)      [https://github.com/shejialuo/CS144](https://github.com/shejialuo/CS144)

(3) 阿苏的CS144讲解，讲解角度比较照顾小白，切入角度不错 [https://space.bilibili.com/16765968/channel/collectiondetail?sid=696216](https://space.bilibili.com/16765968/channel/collectiondetail?sid=696216)
