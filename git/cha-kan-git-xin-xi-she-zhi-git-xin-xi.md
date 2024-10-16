# 查看git信息，设置git信息

在 Git 中，查看和设置用户信息是非常重要的，尤其是在进行版本控制时。以下是如何查看和设置 Git 用户信息的步骤：

## 查看 Git 用户信息

要查看当前 Git 配置的用户信息，可以使用以下命令：

(1) 查看全局配置

```bash
git config --global user.name  
git config --global user.email  
```

(2) 查看本地仓库配置（如果在特定仓库中设置了不同的用户信息）

```bash
git config user.name  
git config user.email  
```

(3) 查看所有配置

```bash
git config --list  
```



## 设置 Git 用户信息

如果你需要设置或更改 Git 的用户信息，可以使用以下命令：

(1) 设置全局用户信息（适用于所有仓库）

```bash
git config --global user.name "Your Name"  
git config --global user.email "your.email@example.com"  
```

(2) 设置本地用户信息（仅适用于当前仓库）

```bash
git config user.name "Your Name"  
git config user.email "your.email@example.com"  
```



下面给出示例

假设你想设置全局用户信息为 "Alice" 和 "[alice@example.com](mailto:alice@example.com)"，可以执行以下命令：

```bash
git config --global user.name "Alice"  
git config --global user.email "alice@example.com"  
```

验证设置

设置完成后，可以再次使用以下命令来验证你的设置：

```bash
git config --global user.name  
git config --global user.email  
```

注意事项

(1) 全局与本地设置：全局设置会影响所有 Git 仓库，而本地设置仅影响当前仓库。如果在本地仓库中设置了用户信息，它将覆盖全局设置。

(2) 确保信息正确：确保你输入的用户名和电子邮件地址是正确的，因为这些信息将出现在你的提交记录中。
