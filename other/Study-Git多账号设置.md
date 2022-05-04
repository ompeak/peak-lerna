1.取消global

git config --global --unset user.name

git config --global --unset user.email

2.配置rsa文件
cd ~/.ssh
ssh-keygen -t rsa -C "gaofeng.zgf@alibaba-inc.com"  # 之后会提示输入文件名 id_rsa_alibaba
ssh-keygen -t rsa -C "peakcary@163.com"  # 之后会提示输入文件名 id_rsa_peakcary

3 添加 ssh key
cd ~/.ssh
ssh-add -D
ssh-add ~/.ssh/id_rsa_alibaba
ssh-add ~/.ssh/id_rsa_peakcary
ssh-add -l

4 修改ssh config

cd ~/.ssh

touch config

vim config





 

```
# alibaba
Host gitlab.alibaba-inc.com
HostName gitlab.alibaba-inc.com
User gaofeng.zgf@alibaba-inc.com
IdentityFile ~/.ssh/id_rsa_alibaba

# peakcary
Host peakcary
HostName github.com
User peakcary
IdentityFile ~/.ssh/id_rsa_peakcary
```



参考

http://tmyam.github.io/blog/2014/05/07/duo-githubzhang-hu-she-zhi/ 

http://jacob110.github.io/2015/09/15/git-multiple-accounts/ 

http://www.barretlee.com/blog/2016/03/09/config-in-ssh-after-troubling-git-connection/ 





继续添加帐号

------

1 配置rsa文件
cd ~/.ssh
ssh-keygen -t rsa -C "peakcary@163.com"  # 之后会提示输入文件名 id_rsa_gitlab

2 添加 ssh key
cd ~/.ssh
ssh-add ~/.ssh/id_rsa_gitlab



3 修改ssh config

cd ~/.ssh

touch config

vim config



 

```
 #gitlab
 Host gitlab.com
 HostName gitlab.com
 User peakcary
 IdentityFile ~/.ssh/id_rsa_gitlab
```

4 添加 ssh-keys

cd ~/.ssh

cat id_rsa_gitlab.pub

5 设置 git config

进入project根目录

git config --local user.name 'peakcary'

git config --local user.email 'peakcary@163.com'

 

```
# alibaba
Host gitlab.alibaba-inc.com
HostName gitlab.alibaba-inc.com
User gaofeng.zgf@alibaba-inc.com
IdentityFile ~/.ssh/id_rsa_alibaba

# peakcary
Host peakcary.github.com
HostName github.com
User peakcary
IdentityFile ~/.ssh/id_rsa_peakcary

# peakcary
Host peakcary.gitee.com
HostName gitee.com
User peakcary
IdentityFile ~/.ssh/id_rsa_peakcary

#gitlab
Host peakcary.gitlab.com
HostName gitlab.com
User peakcary
IdentityFile ~/.ssh/id_rsa_gitlab
```