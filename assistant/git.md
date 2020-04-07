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

