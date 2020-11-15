Maven is still the reality standard of Java building tool, most projects are using maven to build.

I am using maven everyday, I can use it to do something, but I don't think I really understand it. that's why I write this post.



### Basic Concepts

#### POM

This POM is not pom.xml, even they are related.

POM: Project Object Model. That is, model the project that is built. The project that is being built is an Object, PO is short for it. 

PO has attributes. First, we know the project has a unique coordinate. The coordinate is consist of groupId, artifactId, version, classifier, type. So PO have these five attributes. Second, a project has dependencies, so PO has dependencies attribute. So PO will be like:

```java
class PO{
    private String groupId;
    private String artifactId;
    private String version;
    private String classifier;
    private String type;
    private Set<PO> dependencies;
}
```

XML has powerful syntax, a Java object can be written in XML, as an example:

```xml
<PO>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
    <classifier><classifier>
    <type></type>
    <dependencies>
        <PO></PO>
        <PO></PO>
        ...
    </dependencies>
</PO>
```

Above looks like pom.xml, so pom.xml is the description of PO.

Above PO is not completed. We know Java class can be inherited. If we define a super class field in a subclass, the super object will also be created. And, one project may have multiple sub-project: modules, each module is an independent project, those modules will have its super module's attributes to build project. Now PO is like:

```java
class PO{
    private String groupId;
    private String artifactId;
    private String version;
    private String classifier;
    private String type;
    private Set<PO> dependencies;
    private PO parent;
    private Set<PO> modules;
}
```
Using XML:

```xml
<PO>
    <parent></parent>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
    <classifier><classifier>
    <type></type>
    <dependencies>
        <PO></PO>
        <PO></PO>
        ...
    </dependencies>
    <modules>
        ...
    </modules>
</PO>
```

The build process is corresponding to "build" attribute. Build process has another concepts: Lifecycle

### Lifecycle

A whole build process is one Lifecycle. One Lifecycle has multiple phases. The following are all phases:

```
validate： 用于验证项目的有效性和其项目所需要的内容是否具备
initialize：初始化操作，比如创建一些构建所需要的目录等。
generate-sources：用于生成一些源代码，这些源代码在compile phase中需要使用到
process-sources：对源代码进行一些操作，例如过滤一些源代码
generate-resources：生成资源文件（这些文件将被包含在最后的输入文件中）
process-resources：对资源文件进行处理
compile：对源代码进行编译
process-classes：对编译生成的文件进行处理
generate-test-sources：生成测试用的源代码
process-test-sources：对生成的测试源代码进行处理
generate-test-resources：生成测试用的资源文件
process-test-resources：对测试用的资源文件进行处理
test-compile：对测试用的源代码进行编译
process-test-classes：对测试源代码编译后的文件进行处理
test：进行单元测试
prepare-package：打包前置操作
package：打包
pre-integration-test：集成测试前置操作   
integration-test：集成测试
post-integration-test：集成测试后置操作
install：将打包产物安装到本地maven仓库
deploy：将打包产物安装到远程仓库
```

In maven, run any phase, all previous phases will also be run. In each phase, there is a concept: Goal.

### Goal

Phases provide a sequence, but did not say what to do in each phase. For example, "compile" phase define a project must be compiled in building process, but we don't know how to compile, what are the inputs, what compiler will be used. All the details are defined in goal of that phase. One goal is a "Mojo"(Maven old java object). In Mojo abstract class, there is a "execute()" method. The behavior of one goal is defined inside "execute()". But where is concrete Mojo class? It's in Maven plugin. A maven plugin is also a maven project(it has attributes), but this maven project is making use of maven API.

When we are going to run build, we need to bind a goal for every phase. For example, compile phase, we have to have a maven-compile-plugin, it has a concrete goal, which is using javac to compile source files to .class file. So how to bind the concrete goal to compile phase?

```xml
<build>
<plugins>
  <plugin>
    <artifactId>maven-myquery-plugin</artifactId>
    <version>1.0</version>
    <executions>
      <execution>
        <id>execution1</id>
        <phase>test</phase>
        <configuration>
          <url>http://www.foo.com/query</url>
          <timeout>10</timeout>
          <options>
            <option>one</option>
            <option>two</option>
            <option>three</option>
          </options>
        </configuration>
        <goals>
          <goal>query</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
</plugins>
</build>
```

Above pom bind the "query" goal with "test" phase. Whenever the test phase is run, the "query" goal will be run. But, we don't even specify the location of java source files, how to compile? Here is maven's design principle. In maven, the principle is **Convention over configuration**. This is very different from ant, in ant, we must specify where is the source file, output file, javac, classpath, etc. But in maven, those are not necessary. If no specification, maven will go to <root>/src/main/java to find source files, compiled file will be put in target/classes. We mentioned that PO is an object, all PO have a super class: super POM. In super POM, it defines all the default configurations. Super POM can be found in maven installation folder.

The following is the relationship of Lifecycle, phase and goal:

![alt](https://upload-images.jianshu.io/upload_images/1312339-adb4a573a6e30c21.png)



### Build process

Here is an example of building a project:

The structure of the example project is:

![alt](https://upload-images.jianshu.io/upload_images/1312339-811cadd3d03e512e.png)

A project is called echo, it has two modules: api, biz. pom.xml of echo is:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.maven</groupId>
    <artifactId>echo</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <modules>
        <module>api</module>
        <module>biz</module>
    </modules>
</project>
```

So what does it mean `<packaging>pom</packaging>`? It means the project is a parent POM, it is used to:

1. Manage sub projects

If we don't have POM for echo, we have to go to api and biz to run `mvn install`, if we do have a super pom in echo for api and biz, we only have to run `mvn install` in echo POM. This way improve the efficiency of build.

2. Mange common attributes

If both aip and biz need a dependency, we can declare it in echo pom.xml.


Effective pom is the PO object of current project. Run `mvn help:effective-pom` to check it for each project.

By the way, look at the command. Syntax of mvn command is:

`mvn [options] [goal(s)] [phase(s)]`, 

We can run multiple goals/phases in one command. But `mvn help:effective-pom` is not a phase. It's a goal. It's full command is:

`org.apache.maven.plugins:maven-help-plugin:2.2:effective-pom`

`:` is delimiter, so it contains groupId, artifactId, version, goal. The command is executing maven-help-plugin's goal: effective-pom.

Effective-pom is the real pom of each module that is used to build. So effective pom contains all information of building. The following is the effective pom of biz module"

```xml
    <?xml version="1.0"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
      <groupId>org.maven</groupId>
      <artifactId>echo</artifactId>
      <version>1.0.0</version>
    </parent>
    <groupId>org.maven</groupId>
    <artifactId>echo-biz</artifactId>
    <version>1.0.0</version>
    <dependencies>
      <dependency>
        <groupId>org.maven</groupId>
        <artifactId>echo-api</artifactId>
        <version>1.0.0</version>
        <scope>compile</scope>
      </dependency>
    </dependencies>
    <build>
      <sourceDirectory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/main/java</sourceDirectory>
      <scriptSourceDirectory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/main/scripts</scriptSourceDirectory>
      <testSourceDirectory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/test/java</testSourceDirectory>
      <outputDirectory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/target/classes</outputDirectory>
      <testOutputDirectory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/target/test-classes</testOutputDirectory>
      <resources>
        <resource>
          <directory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/main/resources</directory>
        </resource>
      </resources>
      <testResources>
        <testResource>
          <directory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/test/resources</directory>
        </testResource>
      </testResources>
      <directory>/Users/allstarw/workspace/own-proj/misc/maven/echo/biz/target</directory>
      <finalName>echo-biz-1.0.0</finalName>
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>2.4.1</version>
          <executions>
            <execution>
              <id>default-clean</id>
              <phase>clean</phase>
              <goals>
                <goal>clean</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.3.1</version>
          <executions>
            <execution>
              <id>default-install</id>
              <phase>install</phase>
              <goals>
                <goal>install</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>2.5</version>
          <executions>
            <execution>
              <id>default-resources</id>
              <phase>process-resources</phase>
              <goals>
                <goal>resources</goal>
              </goals>
            </execution>
            <execution>
              <id>default-testResources</id>
              <phase>process-test-resources</phase>
              <goals>
                <goal>testResources</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.10</version>
          <executions>
            <execution>
              <id>default-test</id>
              <phase>test</phase>
              <goals>
                <goal>test</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>2.3.2</version>
          <executions>
            <execution>
              <id>default-testCompile</id>
              <phase>test-compile</phase>
              <goals>
                <goal>testCompile</goal>
              </goals>
            </execution>
            <execution>
              <id>default-compile</id>
              <phase>compile</phase>
              <goals>
                <goal>compile</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.3.2</version>
          <executions>
            <execution>
              <id>default-jar</id>
              <phase>package</phase>
              <goals>
                <goal>jar</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.7</version>
          <executions>
            <execution>
              <id>default-deploy</id>
              <phase>deploy</phase>
              <goals>
                <goal>deploy</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </build>
    </project>
```

Comparing to pom.xml of biz, we see effective-pom has configurations that are inherited from Super POM, e.g. <sourceDirectory> defined biz source files path, and all the goals bind to phases:

```xml
[phase]     [goal]
compile     maven-compiler-plugin:2.3.2:compile
package     maven-jar-plugin:2.3.2:jar
install     maven-install-plugin:2.3.1:install
... ...
```

Now run `mvn build` under echo project, the logs are:

```xml
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order: 
[INFO] 
[INFO] echo
[INFO] echo-api
[INFO] echo-biz
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building echo 1.0.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-install-plugin:2.3.1:install (default-install) @ echo ---
[INFO] Installing /Users/allstarw/workspace/own-proj/misc/maven/echo/pom.xml to /Users/allstarw/.m2/repository/org/maven/echo/1.0.0/echo-1.0.0.pom
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building echo-api 1.0.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ echo-api ---
[debug] execute contextualize
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/allstarw/workspace/own-proj/misc/maven/echo/api/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:2.3.2:compile (default-compile) @ echo-api ---
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 1 source file to /Users/allstarw/workspace/own-proj/misc/maven/echo/api/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ echo-api ---
[debug] execute contextualize
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/allstarw/workspace/own-proj/misc/maven/echo/api/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:2.3.2:testCompile (default-testCompile) @ echo-api ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.10:test (default-test) @ echo-api ---
[INFO] No tests to run.
[INFO] Surefire report directory: /Users/allstarw/workspace/own-proj/misc/maven/echo/api/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------

Results :

Tests run: 0, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.3.2:jar (default-jar) @ echo-api ---
[INFO] Building jar: /Users/allstarw/workspace/own-proj/misc/maven/echo/api/target/echo-api-1.0.0.jar
[INFO] 
[INFO] --- maven-install-plugin:2.3.1:install (default-install) @ echo-api ---
[INFO] Installing /Users/allstarw/workspace/own-proj/misc/maven/echo/api/target/echo-api-1.0.0.jar to /Users/allstarw/.m2/repository/org/maven/echo-api/1.0.0/echo-api-1.0.0.jar
[INFO] Installing /Users/allstarw/workspace/own-proj/misc/maven/echo/api/pom.xml to /Users/allstarw/.m2/repository/org/maven/echo-api/1.0.0/echo-api-1.0.0.pom
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building echo-biz 1.0.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.5:resources (default-resources) @ echo-biz ---
[debug] execute contextualize
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:2.3.2:compile (default-compile) @ echo-biz ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-resources-plugin:2.5:testResources (default-testResources) @ echo-biz ---
[debug] execute contextualize
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/allstarw/workspace/own-proj/misc/maven/echo/biz/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:2.3.2:testCompile (default-testCompile) @ echo-biz ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.10:test (default-test) @ echo-biz ---
[INFO] No tests to run.
[INFO] Surefire report directory: /Users/allstarw/workspace/own-proj/misc/maven/echo/biz/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------

Results :

Tests run: 0, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.3.2:jar (default-jar) @ echo-biz ---
[INFO] 
[INFO] --- maven-install-plugin:2.3.1:install (default-install) @ echo-biz ---
[INFO] Installing /Users/allstarw/workspace/own-proj/misc/maven/echo/biz/target/echo-biz-1.0.0.jar to /Users/allstarw/.m2/repository/org/maven/echo-biz/1.0.0/echo-biz-1.0.0.jar
[INFO] Installing /Users/allstarw/workspace/own-proj/misc/maven/echo/biz/pom.xml to /Users/allstarw/.m2/repository/org/maven/echo-biz/1.0.0/echo-biz-1.0.0.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] 
[INFO] echo .............................................. SUCCESS [0.461s]
[INFO] echo-api .......................................... SUCCESS [2.581s]
[INFO] echo-biz .......................................... SUCCESS [0.382s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.593s
[INFO] Finished at: Wed Jul 06 00:22:50 CST 2016
[INFO] Final Memory: 10M/156M
[INFO] ------------------------------------------------------------------------
```

It shows that it's building echo, api, biz, and because api is a dependency of biz, so api need to build first. 

When to build each module, run all the goals of phases of Lifecycle.


### Dependency Management

Maven dependency management save developers' time. We don't have to go to our dependencies' website to download, and put them into our classpath. Just only have to declare the coordinate of dependencies:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.2.7.RELEASE</version>

    <type>jar</type>
    <scope>compile</scope>
    <optional>false</optional>
    <exclusions>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

- Transitive dependencies

Maven is able to load the dependencies of our dependencies:
> This allows you to avoid needing to discover and specify the libraries that your own dependencies require, and including them automatically

e.g. Our project depends on spring-core, spring-core depends on commons-logging.

- Dependency conflict

Our project depends on library A version 1.0, and library B, library B also depends on library A version 2.0. In this case we have a dependency conflict.

Two approaches for conflict: 1. Nearer path. 2. First declaration.


Attributes of dependencies:

| Elements                        | Description                                                                                                                       | Ext                                                                                                                                                                     |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| groupId、artifactId and version | Base coordinate                                                                                                                   |                                                                                                                                                                         |
| type                            | Type of the dependency                                                                                                            | Default is jar                                                                                                                                                          |
| scope                           | Ton control the relationship of the dependency with three type of classpath: compile classpath、Test classpath、running classpath | 包含compile、provided、runtime、test、system和import 6种, 详见:Dependency Scope.                                                                                        |
| optional                        | Whether the dependency is optional.                                                                                               | e.g. A jar package may support both MySQL and Oracle, so two drivers must be loaded, but for a user, they only need one, so they can use optional option to select one. |
| exclusions                      | Exclude transitive dependency                                                                                                     | when there is dependency conflict, this tag can exclude the conflicted dependency.                                                                                      |



- Dependency management

Maven provide plugin to check all dependencies: `mvn dependency: tree`

### Maven Repository

> In Maven, all the output of building are called artifact. Maven repository is used to store those artifacts.

Two kinds of repository:

- Local Repository: Default path is `~/.m2/, maven can only use artifacts that in local repository.
- Remote Repository: 
  At beginning, local repository is empty, so local maven must download artifacts from remote repository. Two kinds of remote repository:
  - Central Repository
  - Private repository

#### Private repository

Private repository is a special remote repository, it is set in LAN, and it's the proxy of central repository.

When local repository needs a dependency, it will ask from private repository, if private repository don't have, private repository will ask from central repository. On the other hand, some artifacts that are not in central repository, people can upload to private repository, so that LAN users can share the artifacts.


#### Customized Bind

Except default bind, user may add extra goals to phases. E.g. maven-source-plugin's goal: jar-no-fork, it can package source code into jar file. Then we bind the goal to `verify` phase.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
                <execution>
                    <id>attach-sources</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>jar-no-fork</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

`executions` may have multiple `execution`, each execution is a goal.



#### Maven plugin develop

Most of the time, we don't need develop ourselves plugin, but this example will help me understand maven.

This example is going to develop a maven plugin called **hanford-maven-plugin**.
1. Create plugin project.
`maven-archetype-plugin` can created a plugin project quickly.

Run command `mvn archetype:generate`

Then provide parameters:

```xml
-DgroupId=com.fq.plugins
-DartifactId=hanford-maven-plugin 
-Dversion=0.0.1-SNAPSHOT
-DarchetypeArtifactId=maven-archetype-plugin 
-DinteractiveMode=false 
-DarchetypeCatalog=internal
```

Plugin itself is also a maven project, but the packaging is `maven-plugin`

The pom.xml is like:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.fq.plugins</groupId>
    <artifactId>lc-maven-plugins</artifactId>
    <packaging>maven-plugin</packaging>
    <version>0.0.1-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>19.0</version>
        </dependency>

        <dependency>
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-plugin-api</artifactId>
            <version>3.3.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.plugin-tools</groupId>
            <artifactId>maven-plugin-annotations</artifactId>
            <version>3.3</version>
        </dependency>
    </dependencies>

</project>
```

2. Create Mojo

A plugin is a distribution of one or more related mojos.

```java
@Mojo(name = "hanford", defaultPhase = LifecyclePhase.VERIFY)
public class HanfordMavenMojo extends AbstractMojo {

    private static final List<String> DEFAULT_FILES = Arrays.asList("java", "xml", "properties");

    @Parameter(defaultValue = "${project.basedir}", readonly = true)
    private File baseDir;

    @Parameter(defaultValue = "${project.build.sourceDirectory}", readonly = true)
    private File srcDir;

    @Parameter(defaultValue = "${project.build.testSourceDirectory}", readonly = true)
    private File testSrcDir;

    @Parameter(defaultValue = "${project.build.resources}", readonly = true)
    private List<Resource> resources;

    @Parameter(defaultValue = "${project.build.testResources}", readonly = true)
    private List<Resource> testResources;

    @Parameter(property = "lc.file.includes")
    private Set<String> includes = new HashSet<>();

    private Log logger = getLog();

    @Override
    public void execute() throws MojoExecutionException, MojoFailureException {
        if (includes.isEmpty()) {
            logger.debug("includes/lc.file.includes is empty!");
            includes.addAll(DEFAULT_FILES);
        }
        logger.info("includes: " + includes);

        try {
            long lines = 0;
            lines += countDir(srcDir);
            lines += countDir(testSrcDir);

            for (Resource resource : resources) {
                lines += countDir(new File(resource.getDirectory()));
            }
            for (Resource resource : testResources) {
                lines += countDir(new File(resource.getDirectory()));
            }

            logger.info("total lines: " + lines);
        } catch (IOException e) {
            logger.error("error: ", e);
            throw new MojoFailureException("execute failure: ", e);
        }
    }

    private LineProcessor<Long> lp = new LineProcessor<Long>() {

        private long line = 0;

        @Override
        public boolean processLine(String fileLine) throws IOException {
            if (!Strings.isNullOrEmpty(fileLine)) {
                ++this.line;
            }
            return true;
        }

        @Override
        public Long getResult() {
            long result = line;
            this.line = 0;
            return result;
        }
    };


    private long countDir(File directory) throws IOException {
        long lines = 0;
        if (directory.exists()) {
            Set<File> files = new HashSet<>();
            collectFiles(files, directory);

            for (File file : files) {
                lines += CharStreams.readLines(new FileReader(file), lp);
            }

            String path = directory.getAbsolutePath().substring(baseDir.getAbsolutePath().length());
            logger.info("path: " + path + ", file count: " + files.size() + ", total line: " + lines);
            logger.info("\t-> files: " + files.toString());
        }

        return lines;
    }

    private void collectFiles(Set<File> files, File file) {
        if (file.isFile()) {
            String fileName = file.getName();
            int index = fileName.lastIndexOf(".");
            if (index != -1 && includes.contains(fileName.substring(index + 1))) {
                files.add(file);
            }
        } else {
            File[] subFiles = file.listFiles();
            for (int i = 0; subFiles != null && i < subFiles.length; ++i) {
                collectFiles(files, subFiles[i]);
            }
        }
    }
}
```

- `@Parameter` provide Mojo parameter. Most Maven plugin is configurable, so by parameter, user can define behavior of plugin.

```xml
<plugin>
    <groupId>com.fq.plugins</groupId>
    <artifactId>lc-maven-plugins</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <executions>
        <execution>
            <id>lc</id>
            <phase>verify</phase>
            <goals>
                <goal>lc</goal>
            </goals>
            <configuration>
                <includes>
                    <include>java</include>
                    <include>lua</include>
                    <include>json</include>
                    <include>xml</include>
                    <include>properties</include>
                </includes>
            </configuration>
        </execution>
    </executions>
</plugin>
```

- `execute()`: Execute plugin functionality.
- Exception: `execute()` may have two kinds of exceptions:
  - `MojoExecutionException`
  - `MojoFailureException`

3. Test && Run

`mvn clean install` can install plugin to repository, then we can use it in other maven project:

`mvn com.fq.plugins:lc-maven-plugins:0.0.1-SNAPSHOT:lc [-Dlc.file.includes=js,lua] [-X]`