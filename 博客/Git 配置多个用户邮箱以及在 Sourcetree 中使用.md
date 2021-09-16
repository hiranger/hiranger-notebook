###  Git 配置多个用户邮箱以及在 Sourcetree 中使用

时间：2021.09.15		作者：游侠舒迟

> 目前常用的是 Gitee ，但也会经常逛 GitHub ，学习前辈们如何写代码，学习新知识。原本有 GitHub 账号，但从来没建过仓库、提交过代码，平时以 fork 、star 、download 居多。今天用新邮箱又注册了一个 GitHub 账号，配置好了 Git ，也算迈出一小步。关键处记录如下。


> **提纲**：
>
> >1.  注册Github账号
> >2.  生成新的ssh key 
> >3.  Sourcetree本地设置邮箱
> >3.  github新建仓库，Sourcetree克隆，修改提交
> >4.  解决github访问慢问题及有时clone、push报错 OpenSSL SSL_read: Connection was reset, errno 10054的问题
> >6. 注： 上述步骤中有时 GitHub 打开慢，需要科学上网解决。


#### 一 、注册GitHub账号

在 [GitHub 官网](https://github.com/) 用邮箱注册即可。


#### 二、生成新的ssh key

> SSH key 提供了一种与 GitHub 通信的方式。通过这种方式，能够在不输入密码的情况下，将 GitHub 作为自己的 remote 端服务器，进行版本控制。

原来已经有 Git ssh key ，且同时关联了Gitee、GitHub、GitLab等，网上有相关教程不再赘述，当时是为了图方便让所有的都使用同一个ssh key，现在的需求是新的 GitHub 账号也要单独生成一个 ssh key，注意 GitHub 注册邮箱 和此处用于提交的邮箱不一定要是同一个邮箱，因为我之前的邮箱绑过 GitHub，用原来的 ssh GitHub 会报错，所以采用了注册邮箱当提交邮箱，在后续步骤中 验证令牌后 会发现两个GitHub 提交邮箱都是可以提交的。为方便区分才设置仓库 local 设置。步骤如下：

参考博客 [如何生成 ssh key](https://www.jianshu.com/p/31cbbbc5f9fa/)  , 

1. 进入  `C:\Users\[username]\.ssh`  目录，如果没有  `.ssh`  需要在上方查看中勾上 『隐藏的项目』、『文件扩展名』这两个。

   <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e21679c89c44cd0a02a118e3dc5c41b~tplv-k3u1fbpfcp-zoom-1.image" style="zoom: 80%;" />

2. cmd 中输入  `ssh-keygen -t rsa -C "your_email@example.com"`  。回车；在是要输入新的ssh文件名，输入  `rsb`  回车；要求输入 passphrase 本步骤可以跳过，直接回车两次，最后生成新的 ssh。

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ccaeafcc25d4335b3be45a555891beb~tplv-k3u1fbpfcp-zoom-1.image" alt="image-20210915210206736" style="zoom:80%;" />

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f8cc0d536c154beb9810ca527bdd21c9~tplv-k3u1fbpfcp-zoom-1.image" alt="image-20210915210556189" style="zoom:80%;" />

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c1ade790eecb4696bcb28cf924d3e031~tplv-k3u1fbpfcp-zoom-1.image" alt="image-20210915210659352" style="zoom: 50%;" />

3. 将  `id_rsb.pub`  中的字符复制到 GitHub 的 ssh key 中。

   <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb588c239d924aeaba9057bbc5756ab9~tplv-k3u1fbpfcp-zoom-1.image" alt="image-20210915211138724" style="zoom: 33%;" />


#### 三、Sourcetree 新增邮箱

参考博客 [解决Sourcetree 推 GitHub 不停弹登录窗Logon failed](https://segmentfault.com/a/1190000038492416) ，工具 =>选项 =>验证 =>添加 => GitHUb HTTPS OAuth  刷新令牌，验证邮箱账户。


#### 四、github新建仓库，Sourcetree克隆

GitHub新建仓库，Sourcetree克隆，参考博客 [Git配置多用户和邮箱](https://blog.csdn.net/U_riel/article/details/102727018)，了解到Git用户信息配置一共有三个级别：system(系统级)、global(用户级)和local(版本库)。system 的配置整个系统只有一个，global 的配置每个账户只有一个，local 的配置取决于Git版本库数量，在版本库才能看到。从Git官网的资料来看，这三个级别是逐层覆盖的。首先去查找system配置，其次查找 global 配置，最后查找 local 配置。逐层查找的过程中若查到配置值，则会覆盖上一层的配置。假如三个级别都配置了用户信息，则最后生效的配置是 local (版本库)级的。仓库设置自己的提交邮箱优先级最高，在 Sourcetree  设置如下：

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/36273705b4244ee9bb788b35e5caee02~tplv-k3u1fbpfcp-zoom-1.image" alt="image-20210915213424883" style="zoom: 80%;" />



#### 五、测试 Sourcetree 中 pull/push/clone

参考博客 [clone、push报错 OpenSSL SSL_read: Connection was reset, errno 10054的问题](https://www.cnblogs.com/hereisdavid/p/15177953.html)，很有可能是网络不稳定，连接超时导致的，解决方法：

 打开 cmd，取消代理，

```git
git config --global --unset http.proxy
git config --global --unset https.proxy
```

更改网络认证设置，

```git
git config --global http.sslVerify "false"
```


增加缓冲到500M，

```git
git config --global http.postBuffer 524288000
```

GitHub 访问、克隆、推送十分不稳定，经常报这个错，即使科学上网也是如此，只好耐心等待，反复尝试。


最后就可以愉快的使用 GitHub 版本控制写代码啦，像那些认真总结经验、无私分享知识、每天都有提交、贡献开源项目的前辈们学习！

