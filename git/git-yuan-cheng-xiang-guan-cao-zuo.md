# git远程相关操作



## 本地仓库初次上传到远程仓库

<div align="left">

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

</div>



## clone完仓库后，本地选取对应的远程分支进行开发

```bash
git checkout origin/dev
git checkout -b dev 

git check -b dev origin/dev 
```

上面两条命令组合和下面一条命令效果一样吗?

答案是不一样！



### 解析

1\. `git checkout origin/dev`

这条命令用于切换到远程的 `dev` 分支，但不会在本地创建一个新的分支。你将处于“游离头指针”（detached HEAD）状态，意味着你在查看远程分支的内容，但没有在本地创建一个对应的分支。

2\. `git checkout -b dev`

这条命令用于在本地创建一个新的分支 `dev`，并切换到该分支。此时，`dev` 分支是一个独立的本地分支，与远程分支没有直接关联。

3\. `git checkout -b dev origin/dev`

这条命令用于在本地创建一个新的分支 `dev`，并将其设置为跟踪远程的 `origin/dev` 分支。这样，当你在本地 `dev` 分支上进行更改并推送时，Git 会知道将更改推送到远程的 `dev` 分支。



### 结论

**组合命令**：如果你先执行 `git checkout origin/dev`，然后执行 `git checkout -b dev`，你会得到一个新的本地分支 `dev`，但这个分支的起点是你在 `origin/dev` 上的游离状态。这意味着 `dev` 分支不会自动跟踪 `origin/dev`。

**单条命令**：`git checkout -b dev origin/dev` 直接创建一个新的本地分支 `dev`，并将其设置为跟踪远程的 `origin/dev` 分支。



因此，虽然组合命令和单条命令都可以创建一个名为 `dev` 的本地分支，但它们的效果并不完全相同：

**组合命令**：创建的 `dev` 分支不会自动跟踪 `origin/dev`\
**单条命令**：创建的 `dev` 分支会自动跟踪 `origin/dev`

如果你希望本地分支 `dev` 跟踪远程分支，建议使用 `git checkout -b dev origin/dev`



### 正确的组合命令

那么组合命令，如何设置自动跟踪origin/dev

如果你使用组合命令 `git checkout origin/dev` 和 `git checkout -b dev` 创建了本地分支 `dev`，但希望将其设置为自动跟踪远程的 `origin/dev` 分支，可以使用以下步骤：

1\. 创建本地分支并切换

首先，使用组合命令创建本地分支 `dev`：

```bash
bashgit checkout origin/dev  # 切换到远程分支，处于游离状态  
git checkout -b dev      # 创建本地分支 dev  
```

2\. 设置跟踪关系

接下来，使用以下命令将本地 `dev` 分支设置为跟踪远程的 `origin/dev` 分支：

```bash
bashgit branch --set-upstream-to=origin/dev dev  
```

3\. 验证跟踪关系

你可以使用以下命令来验证本地分支 `dev` 是否正确跟踪远程分支：

```bash
bashgit branch -vv  
```

这将显示所有本地分支及其跟踪的远程分支。



通过以上步骤，你可以将本地分支 `dev` 设置为自动跟踪远程的 `origin/dev` 分支。这样，当你在本地 `dev` 分支上进行更改并推送时，Git 会知道将更改推送到远程的 `dev` 分支





## git push远程仓库authentication出错



<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

很多种解决办法，我认为最好的解决办法



### git credential

(1) 首先就是创建token，创建完后自己可以选择保存下来，方便后续使用

(2) 还是使用git push，但是此时输入的密码就是token了，git输入密码时，不能展示密码，先复制，然后直接右键按一下就是粘贴了

(3) 为了防止每次输入token，使用git config --global credential.helper store，这样就会把token存入\~/.git-credentials中，文件内容类似于https://\<USERNAME>:\<TOKEN>@github.com



参考

\[1] https://stackoverflow.com/questions/68775869/message-support-for-password-authentication-was-removed

\[2] [https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage](https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage)



## git push大文件失败

要解决由于文件大小超过 GitHub 的限制而导致的 `git push` 失败问题，可以考虑以下几种方法：

#### 1. 使用 Git Large File Storage (LFS)

Git LFS 是一个 Git 扩展，专门用于处理大文件。通过使用 Git LFS，您可以将大文件存储在外部服务器上，从而避免 GitHub 的文件大小限制。

**步骤：**

1. **安装 Git LFS**：
   * 根据您的系统，从 [Git LFS 官网](https://git-lfs.github.com/) 下载并安装。
2.  **初始化 Git LFS**：

    ```bash
    bashgit lfs install  
    ```
3.  **跟踪大文件**：\
    假设您的大文件路径是 `Resources/ffmpeg.exe`：

    ```bash
    bashgit lfs track "Resources/ffmpeg.exe"  
    ```
4.  **添加跟踪配置到仓库**：

    ```bash
    bashgit add .gitattributes  
    ```
5.  **提交和推送文件**：

    ```bash
    bashgit add Resources/ffmpeg.exe  
    git commit -m "Add ffmpeg with Git LFS"  
    git push origin main  
    ```

