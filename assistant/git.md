##### create a new repository on the command line

```
echo "# flink-mongodb-test" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:xiaoxiaoleng/flink-mongodb-test.git
git push -u origin master
```

##### …or push an existing repository from the command line

```
git remote add origin git@github.com:xiaoxiaoleng/flink.git
git push -u origin master
```

##### 添加所有变更

```
git add -A
```

##### 添加当前变更

```
git add .
```

##### commit

```
git add -A stages all changes
git add . stages new files and modifications, without deletions
git add -u stages modifications and deletions, without new files
```

##### Create a new branch:

```
git checkout -b feature_branch_name
```

##### Push your branch to the remote repository:

```
git push -u origin feature_branch_name
```

##### 撤销缓冲区提交但没有推送的内容

```
git reset的作用是修改HEAD的位置，即将HEAD指向的位置改变为之前存在的某个版本
适用场景： 如果想恢复到之前某个提交的版本，且那个版本之后提交的版本我们都不要了，就可以用这种方法。
git reset --soft HEAD^

查看历史提交
git log
git log --pretty=oneline
查看每一次命令记录
git reflog

git reset –-soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可；
git reset -–hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容，撤销的commit中所包含的更改被冲掉；

git revert commit_id
```

##### 在 tag 代码的基础上做修改，你需要一个分支： 

```
git checkout tags/v1.0 -b v1.0-branch
git branch --set-upstream-to=origin/v1.0 v1.0
git fetch --tags

git checkout -b R16.90 RT-V2.0.R16.90.20200928


git clone -b branch_name url
git clone -b R-2.0.R16.40.20191204 https://git.uyunsoft.cn/earth/banda.git
```

##### 查看tag

```
git fetch --tags

git tag
```

##### 创建Tag

```
git tag -a RT-V2.0.R16.71.20200619  -m "修改校验"
git push origin RT-V2.0.R16.71.20200619
```

##### 通过账号和密码获取远程仓库

```
http://oauth2:33UYAqb6rTZSmnQrNFRv@10.30.30.3/business-builder/open-platform/iop-walle-base-front.git

git clone https://username:password@github.com/username/repository.git
```

##### 查看分支创建者时间

```
列出远程Git分支按作者排序的committerdate：
git for-each-ref --format='%(committerdate) %09 %(authorname) %09 %(refname)' | sort -k5n -k2M -k3n -k4n
然后比如你想查看某个分支branch_A, 那么就再后面加上|grep branch_A:
git for-each-ref --format='%(committerdate) %09 %(authorname) %09 %(refname)' | sort -k5n -k2M -k3n -k4n|grep branch_A

git log --oneline master | cut -d " " -f 1 | tail -1 | xargs git log
```

##### 直接使用账号密码进行clone

```
git clone http://oauth2:33UYAqb6rTZSmnQrNFRv@10.30.30.3/business-builder/open-platform/iop-walle-base-front.git
```

#####将旧仓库分支推送到新仓库某一分支

```
确保仓库代码已经是在最新状态
git pull
远程仓库重命名/删除远程仓库
git remote rename origin old-origin
git remote rm origin
添加新远程仓库地址/修改远程分支地址
git remote add origin ssh://xxxxx.git(ssh地址)

git remote set-url origin ssh://git@10.10.3.189:2222/x86/centos-logos.git

提交旧仓库的temp分支的代码到新仓库master底下
git push origin temp:master
```

##### clone指定分支

```
git clone --branch <branchname> <remote-repo-url>
git clone -b <branchname> <remote-repo-url>
```

