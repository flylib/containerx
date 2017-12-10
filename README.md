# containerx #
## 简介 ##
containerx是一款迷你型的bean容器，IoC框架。是作者学习了Spring源码后，为了进一步学习Spring原理而自行开发的框架。 <br/>
A mini IoC (dependency injection) framework
极少使用其他的第三方jar包。
## 用途 ##
主要用于学习Spring/AOP原理, 或者在自己项目中用于轻量级IoC。
## 开发者 ##
刘少明(Frank Liu) &nbsp;&nbsp;&nbsp; git [https://github.com/flylib](https://github.com/flylib "https://github.com/flylib")
&nbsp;&nbsp;&nbsp; 邮箱 liushaomingdev@163.com <br/>
博客： csdn &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [http://blog.csdn.net/lsm135](http://blog.csdn.net/lsm135 "http://blog.csdn.net/lsm135") <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 开源中国 [https://my.oschina.net/u/3051910/blog](https://my.oschina.net/u/3051910/blog "https://my.oschina.net/u/3051910/blog")
<br/>

## 实现的功能 ##
1. setter注入（目前基于xml配置bean，未来会支持注解配置bean）;
2. 单例bean的注册和获取;
3. AOP面向切面编程（基于xml配置）;

## quick start-快速上手 ##
步骤如下：<br/>
Step 1: 在自己的项目里添加containerx的依赖
```xml
<dependency>
	<groupId>io.github.flylib</groupId>
	<artifactId>containerx</artifactId>
	<version>1.0.1</version>
</dependency>
```
Step2 :在自己的项目里面执行
```shell
mvn compile
```
可以参考containerx-demo项目。<br/>

## 示例 ##
containerx-demo项目 Java代码如下：
```java
package io.github.flylib.containerx.demo.app;

import io.github.flylib.containerx.beans.factory.BeanFactory;
import io.github.flylib.containerx.beans.factory.XmlBeanFactory;
import io.github.flylib.containerx.demo.model.Person;
import io.github.flylib.containerx.demo.service.DemoService;

/**
 * containerx的demo
 * @author Frank Liu(liushaomingdev@163.com)
 *
 */
public class Main {
	public static void main(String[] args) {
		iocDemo();
	}
	
	private static void iocDemo() {
		BeanFactory beanFactory = new XmlBeanFactory("beans.xml");
		Person person = (Person)beanFactory.getBean("myPerson");
		
		System.out.println("name=" + person.getName());
		System.out.println("address=" + person.getAddress());
		
		Person person1 = (Person)beanFactory.getBean("myPerson");
		System.out.println("person=" + person);
		System.out.println("person1=" + person1);
		System.out.println("person == person1? " + (person == person1));
		
		aopDemo(beanFactory);
	}
	
	private static void aopDemo(BeanFactory beanFactory) {
		DemoService demoService = (DemoService)beanFactory.getBean("demoService");
		System.out.println("\n\n---AOP demo---");
		demoService.doSomething();
	}
}

```
运行结果如下：
```log
--> DefaultDocumentLoader-getAopConfig
<?xml version="1.0" encoding="UTF-8"?><aop:aspect-list>
		<aop:aspect bean="demoAspect" id="aop1" target="demoService">
			<before>beforeMethod</before>
			<after>afterMethod</after>
		</aop:aspect>
	</aop:aspect-list>
Security framework of XStream not initialized, XStream is probably vulnerable.
id=aop1, bean=demoAspect, target=demoService
before=beforeMethod, after=afterMethod
鐢ㄤ唬鐞哹ean鍘讳唬鏇縝ean瀹瑰櫒涓師鏈夌殑bean 瀹屾瘯.
name=Frank Liu
address=Shanghai,China(中国-上海)
person=io.github.flylib.containerx.demo.model.Person@15ca7889
person1=io.github.flylib.containerx.demo.model.Person@15ca7889
person == person1? true


---AOP demo---
---aop log- before---
---DemoServiceImpl.doSomething() done---
---aop log- after---
```

## 框架原理 ##
 核心方法是<code>inject(Object bean, Map<String, Object> properties)</code><br/>
而其中核心的一行代码是
```java
method.invoke(bean, methodMap.get(methodName));
```

## 常见问题的解决方案 ##
1. 我自己的控制台程序Java Application程序为何mvn package后提示找不到main class ?


答：在pom.xml文件中没有做项目的打包设置的时候，默认没有致命main()函数所在的class。 对于maven的Java Application项目（控制台程序），可以字在pom.xml中增加如下设置
```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-assembly-plugin</artifactId>
			<version>2.5.5</version>
			<configuration>
				<archive>
					<manifest>
						<mainClass>io.github.flylib.containerx.demo.app.ContainerxDemoApp</mainClass>
					</manifest>
				</archive>
				<descriptorRefs>
					<descriptorRef>jar-with-dependencies</descriptorRef>
				</descriptorRefs>
			</configuration>
		</plugin>
	</plugins>
</build>
```
打包的时候执行
```shell
mvn clean package assembly:single
```
生成了jar包aaa-jar-with-dependencies.jar。这样就可以直接执行jar包了
```shell
cd target
java -jar aaa-jar-with-dependencies.jar
```
