# Maven介绍

Maven是一个java项目管理和构建工具，可以定义项目结构，项目依赖，并使用同一的方式进行自动化构建。

主要功能：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。

## Maven项目结构

一个使用Maven管理的普通的Java项目，它的目录结构默认如下：

<img src="./image/截屏2023-12-27 16.55.29.png" alt="截屏2023-12-27 16.55.29" style="zoom:50%;" />

项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构。

**项目描述文件pom.xml例子：**

```xml
<project ...>
	<modelVersion>4.0.0</modelVersion>       
	<groupId>com.itranswarp.learnjava</groupId>  类似于Java的包名，通常是公司或组织名称
	<artifactId>hello</artifactId>               类似于Java的类名，通常是项目名称
	<version>1.0</version>
	<packaging>jar</packaging>
	<properties>
        ...
	</properties>
	<dependencies>
        <dependency>      引用其他第三方库,
            <groupId>commons-logging</groupId>              
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
	</dependencies>
</project>
```

Maven工程就是由`groupId`，`artifactId`和`version`作为唯一标识

### 小结

Maven是一个Java项目的管理和构建工具：

- Maven使用`pom.xml`定义项目内容，并使用预设的目录结构；
- 在Maven中声明一个依赖项可以自动下载并导入classpath；
- Maven使用`groupId`，`artifactId`和`version`唯一定位一个依赖。

使用\<dependency>声明一个依赖后，Maven就会自动下载这个依赖包并把它放到classpath中。

# 依赖管理

Maven的一个作用就是解决依赖管理。我们声明了自己的项目需要`abc`，Maven会自动导入`abc`的jar包，再判断出`abc`需要`xyz`，又会自动导入`xyz`的jar包，这样，最终我们的项目会依赖`abc`和`xyz`两个jar包

Maven中的依赖关系：compile,test,runtime,provided

<img src="./image/截屏2023-12-27 17.26.36.png" alt="截屏2023-12-27 17.26.36" style="zoom:50%;" />

Maven使用三个变量**groupId,artifactId,version**来唯一的确定一个jar包

Maven维护了一个中央仓库（[repo1.maven.org](https://repo1.maven.org/)），所有第三方库将自身的jar以及相关信息上传至中央仓库，Maven就可以从中央仓库把所需依赖下载到本地。Maven也可以使用镜像仓库来下载jar包。

Maven并不会每次都从中央仓库下载jar包。一个jar包一旦被下载过，就会被Maven自动缓存在本地目录（用户主目录的`.m2`目录）

使用镜像仓库，需要在`.m2`目录下创建一个`settings.xml`配置文件

```xml
<settings>
    <mirrors>
        <mirror>
            <id>aliyun</id>
            <name>aliyun</name>
            <mirrorOf>central</mirrorOf>
            <!-- 国内推荐阿里云的Maven镜像 -->
            <url>https://maven.aliyun.com/repository/central</url>
        </mirror>
    </mirrors>
</settings>
```

通过[search.maven.org](https://search.maven.org/)搜索关键字，找到对应的组件后，可以获取jar包对应的`groupId,artifactId,version`

**命令行编译：**

进入`pom.xml`所在的目录，输入命令`mvn clean package`，可以获得编译后自动打包的jar

# 构建流程

Maven不但有标准化的项目结构，而且还有一套标准化的构建流程，可以自动化实现编译，打包，发布，等等。

Maven的==**生命周期**==由一系列的==**阶段**==(phase)组成，内置的生命周期`default`包含以下阶段：

- validate
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- compile             运行mvn clean时，从validate阶段执行到compile阶段
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- test
- prepare-package
- package                运行mvn package时，从validate阶段执行到package阶段
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install
- deploy

生命周期`clean`包含三个阶段：pre-clean      clean       post-clean

例如：mvn clean package，实际执行的phase如下

- pre-clean
- clean (clean声明周期的第二阶段clean)
- validate
- ……
- package

==mvn后面跟的是阶段，Maven会自动根据生命周期运行到指定的阶段==

开发中常见的命令：

- mvn clean    清理所有生成的class和jar
- mvn clean compile    先清理，再执行到compile
- mvn clean test
- mvn clean package 

==**Goal：**==一个阶段phase中又会包含一个或多个goal，goal的命名为`abc:xyz`

大多数情况，我们只要指定phase，就默认执行这些phase默认绑定的goal，只有少数情况，我们可以直接指定运行一个goal，例如，启动Tomcat服务器：`mvn tomcat:run`

LifeCycle，phase，goal是从大到小的包含关系，goal是最小的任务单元，是实际工作的部件

# 使用插件

实际上，执行每个阶段，都是通过对应的插件（plugin）来执行的。比如在执行compile这个阶段的时候，maven会找到对用的compiler插件，然后执行默认的compiler:compile这个goal。

Maven内置的标准插件：

- clean（对应的阶段为clean）   
- compiler（compile）
- surfire（test）
- jar（package）

使用自定义插件，需要在pom.xml中声明：

```xml
<project>
    ...
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
                            ...
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>
```

`<groupId>`、`<artifactId>` 和 `<version>`：这些标签指定了所需插件的 Maven 坐标信息，用来唯一标识 Maven 中央仓库中的插件

`<execution>`：在 `<executions>` 下，`<execution>` 标签定义了对插件的具体执行设置。

`<phase>`：`<phase>` 标签指定了插件执行的阶段。在这里，插件会在 Maven 的 `package` 阶段执行。

`<goals>`：`<goals>` 标签指定了要执行的目标（Goal）。

自定义插件还需要一些配置，例如，`maven-shade-plugin`需要指定Java程序的入口，它的配置是：

```xml
<configuration>
    <transformers>
        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
            <mainClass>com.itranswarp.learnjava.Main</mainClass>
        </transformer>
    </transformers>
</configuration>
```

注意，Maven自带的标准插件例如`compiler`是无需声明的，只有引入其它的插件才需要声明。

# 模块管理

在软件开发中，把一个大项目分拆为多个模块是降低软件复杂度的有效方法

对于一个大项目：

<img src="./image/截屏2024-01-08 16.30.21.png" alt="截屏2024-01-08 16.30.21" style="zoom:50%;" />

可以拆分为三个模块（各模块都有自己的pom.xml）：

<img src="./image/截屏2024-01-08 16.31.02.png" alt="截屏2024-01-08 16.31.02" style="zoom:50%;" />

`parent`的`pom.xml`(可以没有)是各模块中的重复部分，只是为了在各个模块中减少重复的配置

如果模块A依赖模块B，需要在模块A中引入模块B，并且在编译的时候需要在根目录创建一个pom.xml：

```xml
<!--   A模块中需要添加的内容     -->
...
    <dependencies>
        <dependency>
            <groupId>com.itranswarp.learnjava</groupId>
            <artifactId>module-b</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>
```

```xml
<!--     根目录下的pom.xml    -->
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.itranswarp.learnjava</groupId>
    <artifactId>build</artifactId>
    <version>1.0</version>
    <packaging>pom</packaging>
    <name>build</name>

    <modules>
        <module>parent</module>
        <module>module-a</module>
        <module>module-b</module>
        <module>module-c</module>
    </modules>
</project>
```

# mvnw

安装Maven时，默认情况下，系统所有项目都会使用全局安装的这个Maven版本。但是，对于某些项目来说，它可能必须使用某个特定的Maven版本，这个时候，就可以使用Maven Wrapper，它可以负责给这个特定的项目安装指定版本的Maven，而其他项目不受影响

## 安装mvnw

在根目录下运行命令：

```
mvn -N io.takari:maven:0.7.6:wrapper    0.7.6为mvnw的版本
mvn -N io.takari:maven:0.7.6:wrapper -Dmaven=3.3.3    3.3.3为maven的版本
```

安装后的目录结构：

<img src="./image/截屏2024-01-08 16.54.16.png" alt="截屏2024-01-08 16.54.16" style="zoom:50%;" />

```
mvnw clean package    // 使用和mvnw关联的maven
./mvnw clean package     //mac或者Linux下
```

Maven Wrapper的另一个作用是把项目的`mvnw`、`mvnw.cmd`和`.mvn`提交到版本库中，可以使所有开发人员使用统一的Maven版本。

# 发布Artifact

