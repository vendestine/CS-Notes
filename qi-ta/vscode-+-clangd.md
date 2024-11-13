# vscode + clangd

clangd的代码提示和补全，非常地强大，这里记录一下 vscode如何配置clangd



## windows





## ubuntu

(1) vscode 安装插件 clangd

<div align="left">

<figure><img src="../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

</div>

(2) 然后`Ctrl + shift + p` reload window，一般这个时候会弹出install clangd的窗口，install即可。如果弹出的窗口显示fail，可以试着关闭代理reload window后再次尝试。

(3) 如果isntall失败，那么我们手动install，[https://clangd.llvm.org/installation.html](https://clangd.llvm.org/installation.html)

<figure><img src="../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

由于我们是ubuntu，所以安装clangd-linux-18.1.3.zip

<div align="left">

<figure><img src="../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

</div>

安装好后，得到一个压缩包，我们解压后，将clangd\_18.1.3放入对应install目录下

```json
"clangd.path": "/home/xxxx/.vscode-server/data/User/globalStorage
/llvm-vs-code-extensions.vscode-clangd/install/18.1.3/clangd_18.1.3/bin/clangd",
```

\
vscode下，`ctrl + ,` 打开settings界面，然后输入proxy，打开settings.json (注意一定要选择正确的主机)，最后设置clangd.path

<figure><img src="../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

(4) 检查是否安装成功，vscode打开panel，然后输入clangd，check for language server update，如果已经安装会显示安装的版本。然后download language server，会提示发现installed clangd，直接set default，最后restart language server，reload window

到此，vscode上配置clangd完成。

<div align="left">

<figure><img src="../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

</div>
