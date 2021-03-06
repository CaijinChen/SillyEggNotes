# Deep In Maven

1.建立目录，运行命令，生成Maven标准的项目目录(项目原型)

	mvn archetype:generate//此时以命令行交互的形式生成

	mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
#
	my-app
	|-- pom.xml
	`-- src
	    |-- main
	    |   `-- java
	    |       `-- com
	    |           `-- mycompany
	    |               `-- app
	    |                   `-- App.java
	    `-- test
	        `-- java
	            `-- com
	                `-- mycompany
	                    `-- app
	                        `-- AppTest.java

2.pom.xml

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	  <modelVersion>4.0.0</modelVersion>
	 
	  <groupId>com.mycompany.app</groupId>
	  <artifactId>my-app</artifactId>
	  <version>1.0-SNAPSHOT</version>
	 
	  <properties>
	    <maven.compiler.source>1.7</maven.compiler.source>
	    <maven.compiler.target>1.7</maven.compiler.target>
	  </properties>
	 
	  <dependencies>
	    <dependency>
	      <groupId>junit</groupId>
	      <artifactId>junit</artifactId>
	      <version>4.12</version>
	      <scope>test</scope>
	    </dependency>
	  </dependencies>
	</project>
name|describe
:-|:-
project| This is the top-level element in all Maven pom.xml files.
modelVersion| This element indicates what version of the object model this POM is using. The version of the model itself changes very infrequently but it is mandatory in order to ensure stability of use if and when the Maven developers deem it necessary to change the model.
groupId| This element indicates the unique identifier of the organization or group that created the project. The groupId is one of the key identifiers of a project and is typically based on the fully qualified domain name of your organization. For example org.apache.maven.plugins is the designated groupId for all Maven plugins.
artifactId| This element indicates the unique base name of the primary artifact being generated by this project. The primary artifact for a project is typically a JAR file. Secondary artifacts like source bundles also use the artifactId as part of their final name. A typical artifact produced by Maven would have the form \<artifactId>-、、\<version>.\<extension> (for example, myapp-1.0.jar).
packaging| This element indicates the package type to be used by this artifact (e.g. JAR, WAR, EAR, etc.). This not only means if the artifact produced is JAR, WAR, or EAR but can also indicate a specific lifecycle to use as part of the build process. (The lifecycle is a topic we will deal with further on in the guide. For now, just keep in mind that the indicated packaging of a project can play a part in customizing the build lifecycle.) The default value for the packaging element is JAR so you do not have to specify this for most projects.
version| This element indicates the version of the artifact generated by the project. Maven goes a long way to help you with version management and you will often see the SNAPSHOT designator in a version, which indicates that a project is in a state of development. We will discuss the use of snapshots and how they work further on in this guide.
name| This element indicates the display name used for the project. This is often used in Maven's generated documentation.
url| This element indicates where the project's site can be found. This is often used in Maven's generated documentation.
description|This element provides a basic description of your project. This is often used in Maven's generated documentation.

3.Archetype是Maven的项目模板工具集。可供选择的原型，也可以自定义

Archetype ArtifactIds|	Description
:-|:-
maven-archetype-archetype|	An archetype to generate a sample archetype project.
maven-archetype-j2ee-simple|	An archetype to generate a simplifed sample J2EE application.
maven-archetype-mojo|	An archetype to generate a sample a sample Maven plugin.
maven-archetype-plugin|	An archetype to generate a sample Maven plugin.
maven-archetype-plugin-site|	An archetype to generate a sample Maven plugin site.
maven-archetype-portlet|	An archetype to generate a sample JSR-268 Portlet.
maven-archetype-quickstart|	An archetype to generate a sample Maven project.
maven-archetype-simple|	An archetype to generate a simple Maven project.
maven-archetype-site|	An archetype to generate a sample Maven site which demonstrates some of the supported document types like APT, XDoc, and FML and demonstrates how to i18n your site.
maven-archetype-site-simple|	An archetype to generate a sample Maven site.
maven-archetype-webapp|	An archetype to generate a sample Maven Webapp project.

4.Maven配置文件

settings.xml

5.*-SNAPSHOT

The SNAPSHOT value refers to the 'latest' code along a development branch, and provides no guarantee the code is stable or unchanging. Conversely, the code in a 'release' version (any version value without the suffix SNAPSHOT) is unchanging.

6.plugins

Whenever you want to customise the build for a Maven project, this is done by adding or reconfiguring plugins.

7.commands

mvn compile(compile application sources)

mvn test(compile and execute unit tests)

mvn install(install the artifact you've generated (the JAR file) in your local repository (${user.home}/.m2/repository is the default location))

mvn site(provide basic information about your project by generate a web site for your project)

mvn clean(This will remove the target directory with all the build data before starting so that it is fresh.)

mvn test-compile(If you simply want to compile your test sources (but not execute the tests))

mvn package(Making a JAR file)

8.lifecycle

> Maven is based around the central concept of a build lifecycle. What this means is that the process for building and distributing a particular artifact (project) is clearly defined.
#
> There are three built-in build lifecycles: default, clean and site.  The default lifecycle handles your project deployment, the clean lifecycle handles project cleaning, while the site lifecycle handles the creation of your project's site documentation.

**A Build Lifecycle is Made Up of Phases**
**A Build Phase is Made Up of Plugin Goals**

default lifecycle comprises of the following phases

- validate	validate the project is correct and all necessary information is available.
- initialize	initialize build state, e.g. set properties or create directories.
- generate-sources	generate any source code for inclusion in compilation.
- process-sources	process the source code, for example to filter any values.
- generate-resources	generate resources for inclusion in the package.
- process-resources	copy and process the resources into the destination directory, ready for packaging.
- compile	compile the source code of the project.
- process-classes	post-process the generated files from compilation, for example to do bytecode enhancement on Java classes.
- generate-test-sources	generate any test source code for inclusion in compilation.
- process-test-sources	process the test source code, for example to filter any values.
- generate-test-resources	create resources for testing.
- process-test-resources	copy and process the resources into the test destination directory.
- test-compile	compile the test source code into the test destination directory
- process-test-classes	post-process the generated files from test compilation, for example to do bytecode enhancement on Java classes. For Maven 2.0.5 and above.
- test	run tests using a suitable unit testing framework. These tests should not require the code be packaged or deployed.
- prepare-package	perform any operations necessary to prepare a package before the actual packaging. This often results in an unpacked, processed version of the package. (Maven 2.1 and above)
- package	take the compiled code and package it in its distributable format, such as a JAR.
- pre-integration-test	perform actions required before integration tests are executed. This may involve things such as setting up the required environment.
- integration-test	process and deploy the package if necessary into an environment where integration tests can be run.
- post-integration-test	perform actions required after integration tests have been executed. This may including cleaning up the environment.
- verify	run any checks to verify the package is valid and meets quality criteria.
- install	install the package into the local repository, for use as a dependency in other projects locally.
- deploy	done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.

clean lifecycle

- pre-clean	execute processes needed prior to the actual project cleaning
- clean	remove all files generated by the previous build
- post-clean	execute processes needed to finalize the project cleaning

site lifecycle

- pre-site	execute processes needed prior to the actual project site generation
- site	generate the project's site documentation
- post-site	execute processes needed to finalize the site generation, and to prepare for site deployment
- site-deploy	deploy the generated site documentation to the specified web server

Built-in Lifecycle Bindings

> Some phases have goals bound to them by default. And for the default lifecycle, these bindings depend on the packaging value. Here are some of the goal-to-build-phase bindings.

Clean Lifecycle Bindings

Phase|	plugin:goal
:-|:-
clean|	clean:clean

Default Lifecycle Bindings - Packaging ejb / ejb3 / jar / par / rar / war

Phase|	plugin:goal
:-|:-
process-resources|	resources:resources
compile|	compiler:compile
process-test-resources|	resources:testResources
test-compile|	compiler:testCompile
test|	surefire:test
package|	ejb:ejb or ejb3:ejb3 or jar:jar or par:par or rar:rar or war:war
install|	install:install
deploy|	deploy:deploy

Site Lifecycle Bindings

Phase|	plugin:goal
:-|:-
site|	site:site
site-deploy|	site:deploy

lifecycle：生命周期，这是maven最高级别的的控制单元，它是一系列的phase组成，也就是说，一个生命周期，就是一个大任务的总称，不管它里面分成多少个子任务，反正就是运行一个lifecycle，就是交待了一个任务，运行完后，就得到了一个结果，中间的过程，是phase完成的，自己可以定义自己的lifecycle，包含自己想要的phase

phase：可以理解为任务单元，lifecycle是总任务，phase就是总任务分出来的一个个子任务，但是这些子任务是被规格化的，它可以同时被多个lifecycle所包含，一个lifecycle可以包含任意个phase，phase的执行是按顺序的，一个phase可以绑定很多个goal，至少为一个，没有goal的phase是没有意义的

goal: 这是执行任务的最小单元，它可以绑定到任意个phase中，一个phase有一个或多个goal，goal也是按顺序执行的，一个phase被执行时，绑定到phase里的goal会按绑定的时间被顺序执行，不管phase己经绑定了多少个goal，你自己定义的goal都可以继续绑到phase中

9.坐标

由groupId、artifactId、version指定jar包的坐标（唯一）

10.仓库

本地仓库

远程仓库

镜像仓库（即中央仓库的镜像，为了提高下载jar包的速度，会选择配置镜像仓库，例如配置国内镜像）

配置本地仓库的位置（查看settings.xml）

11.依赖范围 dependency scope

可选值|描述
:-|:-
compile|This is the default scope, used if none is specified. Compile dependencies are available in all classpaths of a project. Furthermore, those dependencies are propagated to dependent projects.
provided|This is much like compile, but indicates you expect the JDK or a container to provide the dependency at runtime. For example, when building a web application for the Java Enterprise Edition, you would set the dependency on the Servlet API and related Java EE APIs to scope provided because the web container provides those classes. This scope is only available on the compilation and test classpath, and is not transitive.
runtime|This scope indicates that the dependency is not required for compilation, but is for execution. It is in the runtime and test classpaths, but not the compile classpath.
test|This scope indicates that the dependency is not required for normal use of the application, and is only available for the test compilation and execution phases. This scope is not transitive.
system|This scope is similar to provided except that you have to provide the JAR which contains it explicitly. The artifact is always available and is not looked up in a repository.
import|This scope is only supported on a dependency of type pom in the \<dependencyManagement> section. It indicates the dependency to be replaced with the effective list of dependencies in the specified POM's <dependencyManagement> section. Since they are replaced, dependencies with a scope of import do not actually participate in limiting the transitivity of a dependency.

12.依赖传递，依赖冲突

13.模块的聚合与继承

14.POM.xml

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	    <modelVersion>当前pom版本</modelVersion>
	   
	    <groupId>项目组名</groupId>
	    <artifactId>项目名</artifactId>
	    <!-- 
	    	大版本号.分支版本号.小版本号
	    	snapshot 快照版本（不稳定） 
			alpha 内部测试
			beta 公测
			release 稳定版本
			GA 正式发布版本
		-->
	    <version>版本名</version>
	
	    <packaging>打包的格式（jar【默认】/war）</packaging>
	
	    <name>项目描述名</name>
	
	    <url>项目网站地址</url>
	
	    <!-- 项目描述 -->
	    <description></description>
	    <developers></developers>
	    <licenses></licenses>
	    <organization></organization>
	
	    <dependencies>
	        <dependency>
	            <groupId>junit</groupId>
	            <artifactId>junit</artifactId>
	            <version></version>
	            <type></type>
	            <!-- 
	            	依赖范围
	            	test/compile(default)/runtime/..
	            -->
	            <scope></scope>
	            <!-- 设置依赖是否可选 -->
	            <optional></optional>
	            <!-- 排除依赖传递列表 -->
	            <exclusions>
	            	<exclusion>
	            		
	            	</exclusion>
	            </exclusions>
	        </dependency>
	    </dependencies>
	
	    <!-- 依赖的管理 -->
	    <dependencyManagement>
	    	<dependencies>
		        <dependency>
		            
		        </dependency>
	    	</dependencies>
	    </dependencyManagement>
	
	    <build>
	    	<!-- 插件列表 -->
	    	<plugins>
	    		<plugin>
	    			<groupId></groupId>
	    			<artifactId></artifactId>
	    			<version></version>
	    			<executions>
	    				<execution>
	    					<phase></phase>
	    					<goals>
	    						<goal></goal>
	    					</goals>
	    				</execution>
	    			</executions>
	    		</plugin>
	    	</plugins>
	    </build>
	
	    <!-- 父Pom的继承 -->
	    <parent>
	    	
	    </parent>
	
	    <!-- 多个子模块 -->
	    <modules>
	    	<module>
	    		
	    	</module>
	    </modules>
	</project>