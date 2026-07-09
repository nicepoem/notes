# ssh

### 第一步：完成密钥生成（如果当前命令卡住）

执行 `ssh-keygen -t ed25519 -C "484188846@.com"` 后，系统会提示：

- **Enter file in which to save the key (C:\Users\zhaohui/.ssh/id_ed25519):**
    直接按 **回车**（使用默认路径）。
- **Enter passphrase (empty for no passphrase):**
    可以设置密码（更安全），也可以直接按 **回车** 跳过（不设密码）。
- **再次输入密码**（如果设置了）或直接回车。

完成后，会在 `~/.ssh/` 目录下生成两个文件：

- `id_ed25519`（私钥，**不要泄露**）
- `id_ed25519.pub`（公钥，需要添加到 GitHub）

### 第二步：查看并复制公钥

在 Git Bash 中执行：

```
cat ~/.ssh/id_ed25519.pub
```

会显示一串以 `ssh-ed25519` 开头的文本，**全选复制**（包括末尾的邮箱注释）。

------

### 第三步：将公钥添加到 GitHub

1. 登录 GitHub，点击右上角头像 → **Settings**。
2. 左侧菜单选择 **SSH and GPG keys**。
3. 点击 **New SSH Key**。
4. **Title** 随意填写（如 “My Windows PC”）。
5. **Key** 粘贴刚刚复制的公钥内容。
6. 点击 **Add SSH Key**（可能需要输入 GitHub 密码或二次验证）。

------

### 第四步：测试 SSH 连接

在 Git Bash 中执行：

```
ssh -T git@github.com
```

如果看到类似 `Hi nicepoem! You've successfully authenticated...` 的提示，说明 SSH 配置成功。

------

### 第五步：修改本地仓库的远程地址（从 HTTPS 切换为 SSH）

当前你的远程地址是 `https://github.com/nicepoem/static.git`，需要改为 SSH 格式：

```
git remote set-url origin git@github.com:nicepoem/static.git
```

可以检查是否修改成功：

```
git remote -v
```

应该看到两个 `fetch/push` 地址都以 `git@github.com:` 开头。

------

### 第六步：重新执行推送

```
git push -u origin master:main
```



这次不会再出现连接错误，因为 SSH 协议通常比 HTTPS 更稳定（且不依赖系统代理）。