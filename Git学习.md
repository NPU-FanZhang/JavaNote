# Git学习

> 分布式版本控制系统

## 简单理论

四个工作区域：

- 工作目录
- 暂存区（Index\Stage）
- 仓库区（Repository）
- 远程仓库（Remote）

![image-20210407140840726](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20210407140840726.png)



## 基本配置

`$ git config -l` 查看基础配置

> 本地存放位置 Git\etc\gitconfig中

```shell
$ git config -l
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
http.sslbackend=openssl
http.sslcainfo=D:/Software/Git/mingw64/ssl/certs/ca-bundle.crt
core.autocrlf=true
core.fscache=true
core.symlinks=false
pull.rebase=false
credential.helper=manager-core
credential.https://dev.azure.com.usehttppath=true
init.defaultbranch=master
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
```



`$ git config --global --list` 查看本地配置文件

> 本地存放位置 C:\Users\Admin\gitconfig中

> 第一次进入会显示如下，需要配置用户名和email

```shell
$ git config --global --list
fatal: unable to read config file 'C:/Users/Admin/.gitconfig': No such file or directory
```
> 配置用户名和邮箱

```shell
$ git config --global user.name "SlowHot"
$ git config --global user.email  "244167780@qq.com"
```



`ssh-keygen`绑定SSH公匙

```shell
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Admin/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Admin/.ssh/id_rsa
Your public key has been saved in /c/Users/Admin/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:vLaGaZt7hUVVXHSNKgqLw5j3DwqPSScs0+JcuUkz7lo Admin@DESKTOP-UKF002H
The key's randomart image is:
+---[RSA 3072]----+
|           ..o.+=|
|          .   o o|
|         .   .   |
|      ..  o .    |
|   + . oS+ .     |
| oo * . o..      |
|+ *E.o.oo.       |
|o==B=.=+o.       |
| +==o.+*o        |
+----[SHA256]-----+
```

> C:\Users\Admin\.ssh\id_rsa.pub中生成SHA256公匙。将公匙复制到GitHub的setting中，来绑定本地电脑。

![image-20210407143451407](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20210407143451407.png)



## 工作流程

> 初始有两种选择选择1.创建一个本地仓库 或2.直接克隆

1. 本地仓库搭建

   ```shell
   $ git init
   ```
   
2. 直接克隆clone

   ```shell
   $ git clone [url]
   ```

3. 

4. 
