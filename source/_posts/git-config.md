git有一个工具被称为git config，它允许你获得和设置配置变量；这些变量可以控制Git的外观和操作的各个方面。

<!-- more -->

#### 配置文件存储所在位置
- /etc/gitconfig 文件：包含了适用于系统所有用户和所有库的值。如果你传递参数选项’--system’ 给 git config，它将明确的读和写这个文件。

- ~/.gitconfig 文件 ：具体到你的用户。你可以通过传递--global 选项使Git 读或写这个特定的文件。

- 位于git目录的config文件 (也就是 .git/config) ：无论你当前在用的库是什么，特定指向该单一的库。每个级别重写前一个级别的值。因此，在.git/config中的值覆盖了在/etc/gitconfig中的同一个值。

#### 配置用户名和密码
此用户名和邮箱是git提交代码时用来显示你身份和联系方式的，并不是github用户名和邮箱。只是用来识别用的，而git每次提交都会使用这个信息。git会使用这个信息来处理你在系统中所作的操作。如：提交代码时，会附带上当前操作者信息。

- 全局配置
```bash
    git config --global user.name "double"
    git config --global user.email "1772629@qq.com"
```

- 在特定项目中使用不同的信息
进入特定的项目（拥有.git文件）

```bash
    git config user.name "dsd"
    git config user.email "fdf@xx.ds"
```

- 如果全局的配置和当前项目出现相同的配置项。在该项目中进行git操作，会采用当前项目的配置内容

#### 检查配置
```bash
    git config --list # 会把不同文件中的数据都读取出来
    git config user.name # 检查特定关键字目前的值 git config {key}
    #仅查看git/config文件，可以通过git config -e / vim .git/config
```

#### 解决提交拉取代码需要输入用户名密码的问题
```bash
    git config --global credential.helper store # 其会在.gitconfig文件中添加[credential] helper = store 这时候再拉取或提交等其他需要输入用户名、密码的操作。会生成.git-credential文件用于记录用户的用户名和密码信息，下次就不用再输入这些内容了。
```

#### 使用SSH密钥来连接
需要把remote url的https协议修改为git协议
我们会使用SSH提供的公钥登录，方便省去输入密码的步骤。

- 公钥登录的理解：就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

```bash
    # 这个需要用户提供自己的公钥，没有的话，可以新增一个
    ssh-keygen # 一路回车下去，期间会有一个问题，要不要对私钥设置口令(passphrase)，如果担心私钥的安全，倒是可以设置一个。
    ssh-keygen -t rsa -C "xxx" # -t rsa 表示使用rsa算法进行加密，还有就是dsa加密算法 -C是公钥文件中的备注

    # 会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。
```

- 将公钥添加到远程仓库
```bash
    cat ~/.ssh/id_rsa.pub # 查看公钥内容，复制下来

    ssh -T 远程仓库git协议的地址 # 校验是否连接成功

    git remote -v # 查看项目相关联的远程仓库地址，可以看到使用的协议

    git remote set-url origin git@xxxx # git@xxxx是自己复制到的git协议的地址
```
