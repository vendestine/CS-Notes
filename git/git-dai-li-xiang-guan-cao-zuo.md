# git代理相关操作

## 设置代理和取消代理

参考：[https://gist.github.com/laispace/666dd7b27e9116faece6](https://gist.github.com/laispace/666dd7b27e9116faece6)

```bash
# 设置代理
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 查看代理配置
~/.gitconfg 查看全局配置
```

