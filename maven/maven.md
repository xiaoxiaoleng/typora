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

##### 3、强制本地更新

```
mvn clean install -e -U
```

##### 4、生成依赖树

```
mvn dependency:tree -Dverbose >> tree.log
```

##### 5、打印com.fasterxml.jackson.core依赖

```
mvn dependency:tree -Dincludes=com.fasterxml.jackson.core
```

##### 6、通过分析bytecode来输出报告

```
mvn dependency:analyze
```

`dependency:analyze`是通过分析bytecode来输出报告。包含Used undeclared dependencies（使用但未定义的依赖项）和Unused declared dependencies（未使用但却定义的依赖项）。

##### install

```
mvn clean install -DskipTests -Dfast
```

##### deploy 发布jar到仓库

```
mvn deploy:deploy-file -Dmaven.test.skip=true -Dfile=taobao-sdk-java-auto_1479188381469-20210922.jar -DgroupId=com.dingtalk.open -DartifactId=taobao-sdk-java-auto -Dversion=1479188381469-20210922 -Dpackaging=jar -DrepositoryId=nexus-releases -Durl=http://10.30.30.3:8081/repository/maven-releases/ -Duser=development  -Dpassword=1sysc0re@nexus


    <distributionManagement>
        <repository>
            <id>nexus-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://10.30.30.3:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://10.30.30.3:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```

