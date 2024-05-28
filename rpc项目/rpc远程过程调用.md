

### 新建项目
groupId ：创建项目的企业或团队的唯一标识，定义了项目属于哪个组/团队。groupId一般分为多个段，第一段为域，第二段为公司名称。
artifactId ：是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。
name：声明了一个对于用户更为友好的项目名称，不是必须的，推荐为每个pom声明name，以方便信息交流。
version : 指定了项目的当前版本，SNAPSHOT意为快照，说明该项目还处于开发中，是不稳定的版本。
groupid和artifactId被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，想要找到你的项目就必须根据这两个id去查找。

1、groupid和artifactId被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，你想要找到你的项目就必须根据这两个id去查找。
2、groupId和artifactId是maven管理项目包时用作区分的字段，就像是地图上的坐标。
3、artifactId：artifactId一般是项目名或者模块名。
4、groupId一般分为多个段，这里我只说两段，第一段为域，第二段为公司名称。域又分为org、com、cn等等许多，其中org为非营利组织，com为商业组织。举个apache公司的tomcat项目例子：这个项目的groupId是org.apache，它的域是org（因为tomcat是非营利项目），公司名称是apache，artigactId是tomcat。
5、比如我自己新建的项目，cn.wjm.testProj，cn是域名，wjm是我自己的姓名缩写，testProj是项目名




勾选Spring Web就是"build web, including RESTful, applications using SpringMVC. Uses Apache Tomcat as the default embedded container"


`<packaging>pom</packaging>`代表打包的方式改变了
注意在pom.xml中引入需要的module

### 1. provider
mac上中idea想要新建generate getter