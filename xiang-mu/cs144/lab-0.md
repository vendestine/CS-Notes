# lab 0



### Debug&#x20;

(1) make format失败

参考：[https://blog.csdn.net/qq\_38161520/article/details/135886343](https://blog.csdn.net/qq\_38161520/article/details/135886343)

我是用的ubuntu环境，没有安装 clang-format，所以先安装clang-fortmat

```sh
sudo apt update
sudo apt install clang-format
clang-format --version    # 查看clang-format版本，验证是否安装成功
```

安装后仍然make format失败，cmake工程的配置文件有问题，打开sponge/etc/clang\_format.cmake修改配置文件

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

