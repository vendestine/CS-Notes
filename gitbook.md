# GitBook

## gitbook node使用

### 环境配置

环境配置，我真的踩了很多坑，这里记录一下，防止以后换了新设备继续踩坑。

(1) 彻底卸载原来的node和npm

因为gitbook框架和高版本的node和npm适配，会出现很多奇怪的bug，所以我们使用较低版本version 10的node和配套的npm，但如果下载新的node和npm前，以前老版本的node和npm没有卸载干净，也会出一些奇奇怪怪的错误，所以首先一定要确保彻底卸载原来的node和npm。

要彻底卸载 Node.js 和 npm，具体步骤取决于你使用的操作系统。以下是针对 Windows 和 macOS/Linux 的详细卸载步骤。

要彻底卸载 Node.js 和 npm，具体步骤取决于你使用的操作系统。以下是针对 Windows 和 macOS/Linux 的详细卸载步骤。

#### 在 Windows 上卸载 Node.js 和 npm

1. **通过控制面板卸载**:
   * 打开“控制面板”。
   * 点击“程序” > “程序和功能”。
   * 在程序列表中找到 **Node.js**，右键点击并选择“卸载”。
   * 按照提示完成卸载。
2. **删除残留文件**:
   * 卸载后，检查以下目录并手动删除 Node.js 和 npm 的相关文件：
     * `C:\Program Files\nodejs`（Node.js 安装目录）
     * `C:\Users\<YourUsername>\AppData\Roaming\npm`（全局 npm 模块）
     * `C:\Users\<YourUsername>\AppData\Roaming\npm-cache`（npm 缓存）
     * `C:\Users\<YourUsername>\AppData\Local\Programs\Nodejs`（如果存在）
3. **检查环境变量**:
   * 右键点击“此电脑”或“计算机”，选择“属性”。
   * 点击“高级系统设置” > “环境变量”。
   * 在“系统变量”中找到 `Path` 变量，确保删除与 Node.js 相关的路径（例如 `C:\Program Files\nodejs\`）。
4. **重启计算机**（可选）:
   * 为确保所有更改生效，可以重启计算机。

#### 在 macOS 上卸载 Node.js 和 npm

1.  **使用 Homebrew 卸载**（如果是通过 Homebrew 安装的）:

    ```bash
    bashbrew uninstall node  
    ```
2.  **手动删除 Node.js 和 npm**:

    * 打开终端，运行以下命令删除 Node.js 和 npm 的相关文件：

    ```bash
    bashsudo rm -rf /usr/local/bin/node  
    sudo rm -rf /usr/local/bin/npm  
    sudo rm -rf /usr/local/lib/node_modules  
    sudo rm -rf /usr/local/include/node  
    sudo rm -rf /usr/local/share/man/man1/node.1  
    sudo rm -rf /usr/local/share/man/man1/npm.1  
    ```
3.  **检查和删除 npm 缓存**:

    ```bash
    bashsudo rm -rf ~/.npm  
    sudo rm -rf ~/.node-gyp  
    ```
4. **检查环境变量**:
   * 打开 `~/.bash_profile`、`~/.bashrc` 或 `~/.zshrc` 文件，确保没有与 Node.js 相关的路径。如果有，手动删除。
5. **重启终端**:
   * 为确保所有更改生效，可以重启终端。

#### 在 Linux 上卸载 Node.js 和 npm

1.  **使用包管理器卸载**:

    * 如果是通过 `apt` 安装的（Debian/Ubuntu）:

    ```bash
    bashsudo apt remove nodejs npm  
    ```

    * 如果是通过 `yum` 安装的（CentOS/RHEL）:

    ```bash
    bashsudo yum remove nodejs npm  
    ```

    * 如果是通过 `dnf` 安装的（Fedora）:

    ```bash
    bashsudo dnf remove nodejs npm  
    ```
2.  **手动删除 Node.js 和 npm**:

    * 运行以下命令删除 Node.js 和 npm 的相关文件：

    ```bash
    bashsudo rm -rf /usr/local/bin/node  
    sudo rm -rf /usr/local/bin/npm  
    sudo rm -rf /usr/local/lib/node_modules  
    sudo rm -rf /usr/local/include/node  
    sudo rm -rf /usr/local/share/man/man1/node.1  
    sudo rm -rf /usr/local/share/man/man1/npm.1  
    ```
3.  **检查和删除 npm 缓存**:

    ```bash
    bashsudo rm -rf ~/.npm  
    sudo rm -rf ~/.node-gyp  
    ```
4. **检查环境变量**:
   * 打开 `~/.bash_profile`、`~/.bashrc` 或 `~/.zshrc` 文件，确保没有与 Node.js 相关的路径。如果有，手动删除。
5. **重启终端**:
   * 为确保所有更改生效，可以重启终端



(2) 下载node version10，验证node和npm的版本

官网下载 node v10：[https://nodejs.org/en/about/previous-releases](https://nodejs.org/en/about/previous-releases)\
根据不同的平台，选择对应的安装程序：[https://nodejs.org/download/release/v10.24.1/](https://nodejs.org/download/release/v10.24.1/)

```bash
node -v
npm -v
```

确认是v10版本的node和配套的npm就可以了

<div align="left">

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

</div>

(3) 安装gitbook

```bash
npm install -g gitbook-cli
gitbook -V  
```

gitbook -V如果gitbook框架没有下载的话，会install，下载完成后再次执行gitbook -V验证是否安装成功

<div align="left">

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

</div>



### gitbook框架使用





## gitbook node和gitbook web

<mark style="background-color:orange;">gitbook最开始只是node端的一个框架</mark>，一般都是在node端下载gitbook框架，然后我们自己组织markdown文件，最后上传到服务器上，其实就和之前使用的hexo框架差不多

之后又出现了web端，<mark style="background-color:orange;">web端其实可以看做一个云笔记</mark>，就是按照web page的方式编辑gitbook，然后编辑完后，会push到对应的gitbook仓库中

两者之间的关系：gitbook web 和 gitbook node其实并不完全相同，gitbook web端实际上是原始的gitbook node + 定制插件和定制样式

<div align="left">

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

</div>

直接把gitbook web的仓库clone下来，然后用gitbook node去打开的时候，两者的界面还是有一些区别的

gitbook web:

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



gitbook node:

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



结论：gitbook node端，可定制化更高，可以下载很多插件，同时页面ui貌似更大气好看一些，所以之后还是使用gitbook node端为主，配上一些好用的插件，提高生产力。



## gitbook的summary.md和README.md

gitbook渲染到web page，主要就是通过所有的md文件 + summary.md + README.md\
其中README.md是gitbook的简介，而summary.md是gitbook的目录

gitbook的编辑方式：

(1) gitbook web端编辑：如果是采用gitbook web端编辑，那么会自动将第一个page的内容作为README.md，然后自动根据现有的page结构，生成目录写入summary.md里\
(2) github仓库编辑：如果是直接在仓库边界，那么我们为了让仓库的md文件渲染到web page上，我们需要手动编辑summary.md文件，也就是说要自己手动组织目录结构。



## gitbook page 和 github markdown

(1) markdown -> page

如果只有一个一级标题，自动提升，一级标题->page标题，二级->一级，依次类推；\
如果有多个一级标题，不会自动提升，markdown文件名是page标题，一级->一级，二级->二级，依次类

(2) page-> markdown&#x20;

都是自动下降，page标题->一级标题，一级标题->二级标题，依次类推、

(3) 示例

<figure><img src=".gitbook/assets/f9900b724d30ad7cf98c2e1bd5eb2fc.png" alt=""><figcaption></figcaption></figure>

(4) 总结

为了保持gitbook上传的markdown，和最后同步到github的markdown的一致性。\
编辑markdown文件，一级标题是文章名字，然后内容用二三四级标题即可。\
编辑page，page标题是文章名字，然后内容用一二三级标题即可。



## gitbook和github之间的同步

(1) 实验结果

gitbook和github之间的同步分为两种，gitbook -> github 和 github -> gitbook，结果测试后我发现，对于这两种方式都是如下的工作模式：

gitbook web端上，edit page后，merge，commit自动push到github仓库上去；\
github仓库，自己手动编辑后，gitbook web端的page会自动pull仓库的内容，进行更新；



(2) 结论

通过上述的实验，我们清楚了gitbook和github同步的本质

无论是在gitbook编辑，还是在github仓库编辑，实际都是把更新的内容add到索引里，然后再提交到commit，最后push到github仓库中。github仓库中，肯定是最新的commit，而gitbook web端会自动检测是否有新的commit，如果有就进行update，同步成最新的commit。<mark style="background-color:orange;">所以同步源其实就是github仓库</mark>



那既然如此，为什么有给出了两种同步方式，这里的同步其实是说gitbook和github建立connection后，第一次同步的方式，因为建立connection的时候，要么是gitbook为空，github仓库有内容；要么是gitbook有内容，github仓库为空。

按照上述机制，如果初始情况是 gitbook有内容，github仓库为空，这个时候作为同步源的github仓库是空的，所以此时必须选择gitbook -> github，将gitbook的内容同步到github仓库中。之后可以随意切换同步模式，因为github仓库不为空了，它可以作为同步源，gitbook和github仓库都是自动同步。





