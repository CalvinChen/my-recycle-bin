# 测试代码编写规范

## 命名规范

任何包、类或方法的命名应该在意义明确的前提下保持简短，避免使用模糊的命名或序号命名，如 app、app1、app2、Controller1、doSomeTest等。

### 类名称

包含单元测试用例的测试类名称以 Test 为类名后缀。

针对某一个**具体类**进行测试时，测试类名称为：`被测试类名 + Test`。如 StringUtilTest。

针对**具体功能**进行测试时，测试类名建议为：`逻辑功能名称 + FTest`。如 RestdFTest。 [TODO]

### 方法名称

针对某一个**具体类的方法**进行测试时，测试方法名称为：`test + 被测试类方法名 + 可选的场景名称`。如 testEquals、testEqualsNullAndNull。

针对**具体功能具体逻辑**进行测试时，测试方法名建议为： `should + 测试逻辑`。如 shouldRestdGenerateQueryRoute。

## 测试代码组织结构规范

针对**具体类**进行测试的测试类，放在test代码中与被测试类同路径包下。如 `src/main/java/hello/` 目录下的类的单元测试类都放在对应的 `src/test/java/hello` 包下。

针对**具体功能**进行测试的测试类，放在test代码中对应相关包下，如有一个叫 restd 的功能，源代码主要是在 `leap.web.api.restd` 包下，那么相关测试类也要放在test代码中的 `leap.web.api.restd` 包下。

## 测试相关代码组织结构规范

在一般情况下，包名的命名规范都是从大功能到小功能，由粗到细的。

如 leap.web.api.restd.crud...

因此在写单元测试的过程中如果需要新建一些测试相关的类，可以在对应功能包下新增如下含义的包：

- `test.model`：可放置普通实体、Leap实体、POJO等测试需要用到的模型类；

- `test.controller`：可放置 Controller 类，用于 web 测试；

- `test.util`: 可放置通用于当前功能测试的工具类；

- `test.junit`：可放置单元测试相关 TestBase、TestCase等基础类；

- `test.other`：可放置其他不适合于放入以上包的其他测试相关类；

- `test`：尽量留空，不放置任何类。除非在测试中需要，则可以在这里放置 Global 类。

包结构上的每一个层级都可以拥有自己的上述包，代表在这个功能下做单元测试需要用到的测试相关类。

test包下的包根据需求如果有需要会再新增其他包规范。

## web测试项目组织规范

### Controller 位置

如需新建 Controller ，位置参照上一节的 test.controller。

### Global.java 位置

如需新建 Global，位置参照上一节的 test。

### webapp 位置与命名

在项目的 `src/test/webapps` 文件夹下根据**对应功能点的包路径**新建目录作为测试 webapp 根目录。

如 restd 功能对应的包路径为 `leap.web.api.restd`，这新建目录叫这个名字（注意是建一个目录，目录名就叫 `leap.web.api.restd`，不是根据这个建个四级目录）。

在这个目录下就可以放置 WEB-INF 等 web 项目相关资源文件。

原则上一个功能点里的测试类共用一个 webapp 项目，如果有一些不同的启动配置需要测试，可以新增，名称为：`对应功能点包路径 + 配置说明`。如 `leap.web.api.restd.disabledRestd`。

### web.xml 配置

在 web 项目中的 web.xml 中必须指定如下配置：

- 初始化参数 `base-package` ，必须指定到对应功能包的 test 包下；

- 初始化参数 `base-path` 必须指定一个代表当前功能的访问路径前缀；

- filter-mapping 中的 url-pattern 值，必须带和上面 `base-path` 一致的路径前缀。

如 webapps 下有一个叫 `leap.web.api.restd` 的 webapp 工程则按照以上规范我们需要在 web.xml 里面做如下配置：

- 指定 `base-package` 为 `leap.web.api.restd.test`；

- 指定 `base-path` 为 `/restd`；

- 指定 `filter-mapping.url-pattern` 为 `/restd/*`。

示例配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0">

	<display-name>Archetype Created Servlet 3.0 Web Application</display-name>

    <filter>
    	<filter-name>app-filter</filter-name>
        <filter-class>leap.web.AppFilter</filter-class>
        <init-param>
        	<param-name>base-package</param-name>
        	<param-value>leap.web.api.restd.test</param-value>
        </init-param>
        <init-param>
        	<param-name>base-path</param-name>
        	<param-value>/restd</param-value>
        </init-param>
    </filter>
    
    <filter-mapping>
        <filter-name>app-filter</filter-name>
        <url-pattern>/restd/*</url-pattern>
    </filter-mapping>
</web-app>
```

### Leap 配置

**通用**配置文件放置在 `src/test/resources/conf` 目录内。

各个 webapp **私有**配置放置在 webapp 的 `WEB-INF/conf` 目录内。