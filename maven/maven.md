### maven

##### 1、批量修改版本号

```
mvn versions:set -DnewVersion=1.1.3
```

##### 2、生成依赖报告(htm文档)

```
1）导入依赖
<reporting>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId> maven-project-info-reports-plugin </artifactId>
    </plugin>
  </plugins>
</reporting>
2）执行命令
mvn project-info-reports:dependencies
```

##### 3、生成依赖树

```
mvn dependency:tree -Dverbose
```

##### 4、强制本地更新

```
mvn clean install -e -U
```

