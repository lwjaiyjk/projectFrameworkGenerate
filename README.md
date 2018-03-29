# projectFrameworkGenerate
一键生成代码框架结构
设计和使用说明文档地址：https://www.jianshu.com/p/3910035b8a76

一键生成项目结构框架设计和使用说明
1.	简介
随着项目开发的规范逐渐落地，对应的项目目录结构要亟待需要进行统一，这样就可以使的所有的项目都有相同的工程目录结构，这样代码review，跨系统开发等都相对来说要容易很多。
2.	使用
仅仅使用如下命令就可以生成对应的目录结构，步骤如下：
（1）在命令行输入mvn archetype:generate 命令，如下所示：
mvn archetype:generate -DarchetypeGroupId=com.ctfin.framework -DarchetypeArtifactId=code-framework -DarchetypeVersion=0.0.1-SNAPSHOT -DarchetypeCatalog=local
 
（2）根据命令提示输入对应的项目的groupId和artifactId，以及对应的appName和packageName等参数，就可以生产对应的项目了，如下图所示：
 
生成对应的项目目录结构如下图所示：
 


3.	设计和原理
3.1项目目录结构设计
 
模块之间的依赖关系如下：
 
模块功能介绍：
common-util	基础功能工具模块
Common-dal	数据访问层模块
Common-service-integration	在微服务架构中，引用其他服务的模块（将所有引用其他服务的放在一起）
Common-service-facade	项目对外提供服务的模块，主要是接口，参数和返回值模型的定义
Core-shared	核心领域的公共模块
Core-model	核心领域模型定义模块
Core-service	核心功能服务模块，提供一些基础的功能，供业务层调用
Biz-shared	业务层公共功能模块
Biz-prod1/biz-prod2	业务产品层功能模块
Biz-service-impl	实现façade中接口，以便对外提供服务
Web-home/web-prod1	展示层模块
Test	测试模块：所有的测试（单元测试，集成测试等）都放在这个模块中

3.2实现原理说明
利用mvn来生成对应的项目目录架构，下面将一步一步讲述这个实现的过程：
3.2.1新建项目
在eclipse上新建一个mvn项目，选择maven-archetype-archetype，单机next进行下一步：
 
3.2.2输入项目新建信息
输入项目对应的groupId，artifactId和版本等信息，如下图所示：
 
3.2.3项目结构
单击finish，对应的项目就建好了，如下图所示：
 
3.2.4项目pom.xml新增内容
<distributionManagement>
		<repository>
			<id>thirdparty</id>
			<name>ctfin_thirdparty</name>
			<url>http://192.168.1.202:9091/repository/thirdparty/</url>
		</repository>
	</distributionManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-archetype-plugin</artifactId>
				<version>2.3</version>
			</plugin>

			<plugin>  <!-- 打源码 -->
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-source-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<attach>true</attach>
				</configuration>
				<executions>
					<execution>
						<phase>compile</phase>
						<goals>
							<goal>jar</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
3.2.4 Archetype.xml重命名
由于我们要建多模块的项目，则需要将META-INF/maven/archetype.xml重命名为archetype-metadata.xml,具体参见：https://maven.apache.org/guides/mini/guide-creating-archetypes.html和http://maven.apache.org/archetype/archetype-models/archetype-descriptor/archetype-descriptor.html
 
3.2.5新建模块结构
可以在archetype-resources文件夹中新建新的模块（新的文件夹）
 
对应的archetype-resources/biz-service-impl/pom.xml文件的内容如下：
 
对应的archetype-resources/pom.xml定义：
 
GroupId，artifactId和version，appName在archetype-metadata.xml中定义如下：
 
3.2.6新建模块定义
然后在archetype-metadata.xml中增加对应的模块定义：
注意module定义中的id对应的是模块下面对应pom.xml中的${artifactId}的值
 
3.2.7执行项目编译打包部署命令
模块都定义好之后，在这个项目的根目录下面执行mvn clean install命令
3.2.8根据新建结构生成新的项目
根据项目的groupId，artifactId和version生成对应的新工程，按照这个项目结构，对应的命令如下：
mvn archetype:generate -DarchetypeGroupId=ctfin.framework -DarchetypeArtifactId=InduFramework -DarchetypeVersion=0.0.1-SNAPSHOT -DarchetypeCatalog=local
3.2.9将项目上传私仓
将项目jar包上传的私仓，通过如下命令：
mvn deploy:deploy-file -DpomFile=../pom.xml -DgroupId=com.ctfin.framework -DartifactId=code-framework -Dversion=0.0.1-SNAPSHOT -Dpackaging=jar -Dfile=code-framework-0.0.1-SNAPSHOT.jar -Dsources=code-framework-0.0.1-SNAPSHOT-sources.jar -Durl=http://192.168.1.202:9091/repository/thirdparty/ -DrepositoryId=thirdparty

