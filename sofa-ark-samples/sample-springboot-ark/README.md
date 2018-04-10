# How to build an executable-ark-jar
## Introduction
This sample project shows how to build an executable-ark-jar based on 
a springboot project with the tool of `sofa-ark-maven-plugin`.

## Preparation
As this project depends on the ark-plugin generated by the project of 
[`sample-ark-plugin`](../sample-ark-plugin/README.md), please ensure the 
sample sample-ark-plugin installed in your local maven repository before run 
this project.

## Tools
The `Maven plugin` of `sofa-ark-maven-plugin` is provided to build a standard
`executable-ark-jar`, and just needs some simple configurations, Details please
refer to [doc](https://alipay.github.io/sofastack.github.io/docs/build-ark.html)

## Step By Step
### Create a Spring Boot Web Project
Visit [`https://start.spring.io/`](https://start.spring.io/) and generate a standard springboot-web project

### Import sample-ark-plugin
Add a dependency in main `pom.xml` as follows:
```
<dependency>
     <groupId>com.alipay.sofa</groupId>
     <artifactId>sample-ark-plugin</artifactId>
     <classifier>ark-plugin</classifier>
     <version>0.2.0</version>
 </dependency>
```

### Configure sofa-ark-maven-plugin
The maven plugin of sofa-ark-plugin-maven-plugin is configured in main pom.xml, it configures as follows:

```
build>
    <plugins>
        <plugin>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-ark-maven-plugin</artifactId>
            <executions>
                <execution>
                    <id>default-cli</id>
                    
                    <!--goal executed to generate executable-ark-jar -->
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                    
                    <configuration>
                        <!--specify destination where executable-ark-jar will be saved, default saved to ${project.build.directory}-->
                        <outputDirectory>./target</outputDirectory>
                        
                        <!--default none-->
                        <arkClassifier>executable-ark</arkClassifier>
                        
                        <!-- all class exported by ark plugin would be resolved by ark biz in default, if 
                        configure denyImportClasses, then it would prefer to load them by ark biz itself -->
                        <denyImportClasses>
                            <class>com.alipay.sofa.SampleClass1</class>
                            <class>com.alipay.sofa.SampleClass2</class>
                        </denyImportClasses>
                       
                       <!-- Corresponding to denyImportClasses, denyImportPackages is package-level -->
                        <denyImportPackages>
                            <package>com.alipay.sofa</package>
                            <package>org.springframework</package>
                        </denyImportPackages>
                       
                       <!-- denyImportResources can prevent resource exported by ark plugin with accurate 
                       name to be resolved -->
                        <denyImportResources>
                            <resource>META-INF/spring/test1.xml</resource>
                            <resource>META-INF/spring/test2.xml</resource>
                        </denyImportResources>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

The configured items are explained as follows:
+ `outputDirectory`: specify destination where `executable-ark-jar` will be saved, 
default saved to ${project.build.directory}.
+ `arkClassifier`: specify classifier in the maven location of `executable-ark-jar`, default none.
+ `denyImportClasses`: all class exported by ark plugin would be resolved by ark biz in default, if configure denyImportClasses, then it would prefer to load them by ark biz itself.
+ `denyImportPackages`: corresponding to denyImportClasses, denyImportPackages is package-level.
+ `denyImportResources`: denyImportResources can prevent resource exported by ark plugin with accurate name to be resolved by ark biz.

## package/install/deploy
+ package: execute `mvn package`, the `executable-ark-jar` file generated would be saved to the configured 
value of `outputDirectory`.
+ install: execute `mvn install`, the `executable-ark-jar` would be installed local maven repository.
+ deploy: execute `mvn deploy`, just as usual practice.

## run in command line
we support command such as `java -jar executable-ark.jar` to startup application, similar to what this sample
project does, execute command line: `java -jar target/sofa-ark-sample-springboot-ark-0.2.0-executable-ark.jar`
to startup this demo. 

## run in IDE
if you want to run application in IDE, some additional dependency need to be imported. 

+ Spring Boot Project
you should add dependency as follows:

```
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
    <version>0.2.0</version>
</dependency>
```
+ Common Java Project
you should add dependency as follows:

```
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-support-starter</artifactId>
    <version>0.2.0</version>
</dependency>
```

besides, you should add `SofaArkBootstrap.launch(args);` in `Main` Class of Application, do as follows:

```java
public class Application{
    
    public static void main(String[] args) { 
        SofaArkBootstrap.launch(args);
    }
    
}
```

## How to run test cases
Considering to integrate with [JUnit4 FrameWork](https://github.com/junit-team/junit4/wiki/getting-started) and [SpringBoot Test](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html), SOFAArk provided two implementations of runners for using with `@RunWith` include:

+ `ArkJUnit4Runner`

+ `ArkBootRunner`

Now, we would show how to run test case in `JUnit4 FrameWork` and `Spring Boot`;

### Run test case in JUnit4 FrameWork
`ArkJUnit4Runner` is similar to `JUnit4`, the latter is the default implementation of runner in JUnit4 Framework; In other words, if you want run normal junit4 test case in SOFAArk container, you should use `@RunWith(ArkJUnit4Runner.class)` instead of the default one. Code as what the following shows:

```java
@RunWith(ArkJUnit4Runner.class)
public class UnitTest {

    @Test
    public void test() {
        Assert.assertTrue(true);
    }

}
```

If you want use other features of JUnit4 Framework, such as `@Before`, it's not a problem, just do as before.

### Run test case in SpringBoot Test
`ArkBootRunner` is similar to `SpringRunner`, you can visit [this site](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html) for usage of the latter one. For running SpringBoot test case in SOFAArk container, just use `@RunWith(ArkBootRunner.class)` instead of `@RunWith(SpringRunner.class)`. Code as what the following shows:

```java
@RunWith(ArkBootRunner.class)
@SpringBootTest(classes = SpringbootDemoApplication.class)
public class IntegrationTest {

    @Autowired
    private SampleService sampleService;

    @Test
    public void test() {
        Assert.assertTrue("A Sample Service".equals(sampleService.service()));
    }

}
```

If you want use other features of SpringBoot Test, just do as before.