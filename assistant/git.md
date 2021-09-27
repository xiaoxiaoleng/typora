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
git reset --soft HEAD^
```

##### 在 tag 代码的基础上做修改，你需要一个分支： 

```
git checkout tags/v1.0 -b v1.0-branch
git fetch --tags

git checkout -b R16.90 RT-V2.0.R16.90.20200928


git clone -b branch_name url
git clone -b R-2.0.R16.40.20191204 https://git.uyunsoft.cn/earth/banda.git
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

