# 前置知识

* 本地 Git 链接 GitHub
* GitHub Actions 配置工作流（CI）
* Hexo 为框架的静态博客

# git 链接 GitHub （ssh 方式）

> 参考链接：[博客园](https://www.cnblogs.com/linshengqian/p/15065553.html)

## 1.本地配置

* 输入 git 命令：
```bash
git config --global user.name "XXXX"  # 用户名
git config --global user.email "xxxx@xxx.com"  # 邮箱
```

* 创建 rsa key：
```bash
ssh-keygen -t rsa
```

密钥保存在 `~/.ssh/id_rsa.pub`

## 2. GitHub 配置

* 打开 [GitHub 设置页面](https://github.com/settings/keys)，点  `New SSH key` 复制密钥，配置成功。

*Note：可能会遇到报错*
```error
Error: Host key verification failed.
As a security precaution, SSH keeps track of which hosts it has previously seen.
```
*参见 [github docs](https://docs.github.com/zh/authentication/troubleshooting-ssh/error-host-key-verification-failed)*

* 添加仓库
```bash
git remote add origin '你的仓库 ssh 地址'
# Note：创建两个远程仓库，一个放源码，一个发布页面。本地仓库应链接至源码仓库。
```

# 配置自动推送

## 方法一 配置 hooks

> Git 钩子是 Git 仓库中的一些脚本文件，它们会在特定的 Git 事件发生时自动执行。例如，`post-receive` 钩子会在每次接收到推送请求时执行。

- 进入 hooks 目录
```bash
cd .git/hooks
```

* 创建 post-commit 文件
```bash
touch post-commit
chmod +x post-commit
```

* 编辑 post-commit 文件，添加以下内容
```bash
#!/bin/sh
git push
```

## 方法二 Git 别名

```bash
git config --global alias.cmpt '!f() { git commit -m "$1" && git push; }; f'

git cmpt 'commit info'
```

# 配置 Hexo

> 可参见：[夜梦的博客](https://tech.yemengstar.com/hexo-tutorial-deploy-githubpages-beginner/)

# 配置 GitHub Actions

## 加 keys

* 创建 deploy key
```bash
ssh-keygen -t rsa -f ~/.ssh/hexo_deploy_key -C "your_email@example.com"
# Note： rsa 可更改为 ed25519（椭圆曲线） 前面创建 ssh 密钥同理。
```

* 加 deploy key
	在 **发布仓库** 的设置页面：[https://github.com/_YourUsername_/_username_.github.io/settings/keys/new](https://github.com/username/username.github.io/settings/keys/new)

	**记得勾选 `Allow write access` ！**

* 配置 **源码仓库**
	1. 进入 **源码仓库** 的 Settings → Secrets and variables → Actions → New repository secret。
	2. 添加私钥：Name：`HEXO_DEPLOY_KEY` ； Secret：粘贴 **私钥** !
	3. 配置 GitHub Actions ：使用了 [这个配置](https://github.com/marketplace/actions/hexo-action)

大功告成！

# 验证 CI 流程

本地修改 Hexo 源文件（如新增一篇文章 `_posts/test.md`），推送到源码仓库：

```bash
git add .
git cmpt -m "Add test article"
```



