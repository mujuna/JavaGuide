# Git认证协议

### HTTPS协议

使用HTTPS协议进行认证和传输数据。它是基于TLS/SSL的安全协议，通过加密和身份验证来保护数据的传输。`HTTPS使用用户名和密码进行认证`。

### SSH协议 (推荐)

使用SSH协议进行认证和传输数据。SSH是一种网络协议，提供了安全的远程登录和文件传输功能。`SSH使用公钥和私钥对进行身份验证`，而不需要每次都输入密码。（如果你使用GitHub或其他类似的Git托管服务，推荐使用SSH认证。SSH提供了更高的安全性，并且不需要每次都输入密码）

# 远程仓库的免密登陆 (SSH)

### 生成SSH密钥对

> 直接使用`ssh-kengen -t rsa`命令并一路回车，会默认生成名为`id_rsa`的密钥

在本地计算机上生成SSH密钥对，可以使用以下命令生成（`-b 4096`是设定的常规密码位数，`-f`选项重命名自定义密钥，也可以在后面的配置提示中重命名）：

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f user_gitee_rsa
```

根据提示输入文件名和密码。（设置保护私钥文件的强加密密码）

```
Enter file in which to save the key (/Users/mujuna/.ssh/id_rsa): /Users/mujuna/.ssh/user_gitee_rsa （指定密钥名称和保存位置，直接回车则使用默认id_rsa名称）
Enter passphrase (empty for no passphrase):（设置私钥保护强密码）
```

> 输入密码短语是为了保护您的私钥文件。当您使用私钥进行身份验证时，系统会要求您输入密码短语以解锁私钥。这样，即使有人获取了您的私钥文件，他们也无法使用它，除非他们知道密码短语。

### 更新config配置文件

```
vim ~/.ssh/config
添加如下信息：
Host gitee.com
    HostName gitee.com
    User username
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/username_gitee_rsa
```

- Host：虚构出来的地址，使用浏览器无法访问，该 Host 值将用作配置仓库 url，也可以命名为tesla
- User：Gitee中的用户名，如bangl
- IdentityFile：指定local主机中密钥文件地址

### 添加SSH公钥到Gitee

- 登录到Gitee帐户。
- 点击右上角的头像，选择 "Settings"。
- 在左侧导航栏中，选择 "SSH公钥"。
  - 点击 "添加SSH密钥"。
- 复制本地生成的公钥（一般在 `~/.ssh/id_rsa.pub` 文件中，可通过`cat`查看复制），粘贴到 "Key" 字段中。
- 给SSH密钥命名，并点击 "确认"。

### 验证SSH连接

1. 未修改密钥名的情况下，可以使用以下命令验证SSH连接是否正常(默认情况下，SSH会使用名为`id_rsa`的私钥):

   ```
   ssh -T git@gitee.com
   ```

   如果修改了密钥名，你需要在SSH命令中指定私钥的路径：

   ```
   ssh -i /path/to/private_key -T git@gitee.com
   ```

   如果连接成功，终端会打印successful字样

2. 同时也可以尝试运行`git clone git@gitee.com:your_username/your_repository.git`命令，若能够成功克隆，则说明已建立正常连接。

### 安全性🔒

#### 安全措施

要确保密钥的安全性，建议您采取以下措施：

1. 使用强密码保护您的私钥文件。
2. 不要将私钥文件共享给其他人。
3. 定期更换密钥对，以增加安全性。

#### 取消SSH私钥密码

使用强密码保护您的私钥文件，每次执行Git的push或pull命令都要求输入私钥保护密码😂，为了方便，可以取消私钥保护密码（取消后一定要保护好自己的私钥）：

1. 进入密钥所在文件夹：一般为`cd ~/.ssh`
2. 执行`ssh-keygen -f id_rsa -p`，输入旧密码，设置新密码为空

