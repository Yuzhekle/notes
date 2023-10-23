### 管理多个github账号的 ssh key

我们大多时候是通过ssh key的方式来进行github代码库的权限管理，如何生成一个ssh key以及如何在github设置网络上有各类的说明，不是本文的重点。本文要解决的是在一个机器上管理多个账号的方法。

出于各种原因，有些人会有多个github账号，比如一个个人账号，一个工作账号。而github是不允许两个账号出现相同的SSH KEY的。那么问题来了，我们为了方便，往往都是用ss-keygen命令，一路默认参数在~/.ssh目录下生成一对名为id_rsa和id_rsa.pub的密钥，然后把id_rsa.pub贴到github的SSH and GPG keys设置中去。

如何生成一个新的密钥给另一个账号，并且在使用的过程中尽量减少麻烦呢，我这里给出一种相对简便的方法。

#### 1. 生成一对命名的ssh key
首先生成一对新的ssh key，依然是用ssh-keygen命令，只是这次不用默认的参数。

```
> ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/YourHomeDir/.ssh/id_rsa):/YourHomeDir/.ssh/account1
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /YourHomeDir/.ssh/account1.
Your public key has been saved in /YourHomeDir/.ssh/account1.pub.
The key fingerprint is:
SHA256:...
The key's randomart image is:
+---[RSA 2048]----+
| . .o+.oo        |
|. oo. ..ooo o    |
|o+o.+o .oo.+ =   |
|=+ .o.Eo... = .  |
|+    .  S. o .   |
| .    o . o      |
|       B *       |
|      o X *      |
|      .o B..     |
+----[SHA256]-----+
```

需要关注的是上边命令中的第3行，我们输入了/YourHomeDir/.ssh/account1，也就是我们所希望的ssh密钥的名字以及路径，其他步骤基本都一样，一路默认参数回车就可以了。这是我们在/YourHomeDir/.ssh/路径下生成了一对名为account1和account1.pub的新秘钥。

> 一定要注意新秘钥的命名，不要覆盖掉旧的秘钥造成不必要的麻烦。

#### 2. 更改本地的SSH配置

```
cd ~/.ssh
touch config
vim config
```

上面的命令在ssh配置目录创建(如果不存在)一个config文件，并用vim打开编辑。通过vim编辑加入如下配置：


```
# 配置示例1
Host rename_github
    HostName github.com
    IdentityFile ~/.ssh/account1
    UserName example
```

其中第1行中的 `rename_github` 是一个代替 `github.com` 的名字，你可以用一个自己比较容易记得域名，比如我就比较喜欢这样：

```
# 配置示例2
Host my-github-name.github.com
    HostName github.com
    IdentityFile ~/.ssh/account1
```

其中 `my-github-name` 是对应我生成的这个 `ssh key` 的 `github` 账号的名字。

#### 3. 将新生成的ssh key加到github账号配置下
将第一步生成的秘钥对中的account1.pub的内加入github账号的SSH and GPG keys设置项中。因为是一个全新的秘钥，自是不会再出现添加不进去的问题。

#### 4. 克隆新的项目
一般情况下，我们是通过如下的方式克隆一个项目：

```ssh
git clone git@github.com:your-account/your-prj.git
```

我们需要对这个语句中的域名部分做一下修改：

```ssh
# 对应配置示例1
git clone git@xxxx:your-account/your-prj.git


# 对应配置示例2
git clone git@my-github-name.github.com:your-account/your-prj.git
```

这时，我们就是通过新的ssh key来clone的代码，在此之后的操作就没有区别了，一切按照之前的使用习惯即可，无论是pull还是push代码等操作都使用新的ssh key来进行了。

这里补充说一个可能跟ssh key的关系不大，但是跟多账号有关的问题，是关于commit代码的账号的设置的。如果默认不处理，提交代码的时候提交信息中的用户和邮箱信息是用户设置的全局账户的信息，当时应该是这样设置的：

```ssh
git config --global user.name "You Name"
git config --global user.email name@example.com
```


我们往往是要给不同项目设置不同的提交信息，毕竟你不想把公司的邮箱带到私人项目的提交记录中去。可以通过下边的方式文每个项目单独设置提交账户信息：

```
cd YourRepoPath
git config user.name "You Name"
git config user.email name@example.com
```

其实很简单，就是去掉--global参数。