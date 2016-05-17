#概述
OWNER旨在摒弃properties配置文件的样板代码： 
* 简便：无需加载多余代码，转换并管理 properties 配置文件，通过最简单的方式使用你的 properties
* 强大：基于注解，自动类型转换，变量扩展，加载策略，热加载，事件通知等等
* 灵活：选择你需要的功能，忘记你不需要的；容易上手，丰富的文档 
* 
#Welcome 
本文旨在对 OWNER 提供综合性的指导，包括基本特性、高级用法等主题，并且文中附有详细 demo。 

#OWNER是什么 
OWNER 是一个 java 库，旨在减少通过 properties 文件来处理应用程序配置的代码。它已经打包成一个 jar 文件，你也可以从 Maven 中央仓库下载。开发此 API 是受 GWT i18n 启迪（点此查看）。使用 GWT i18n 来加载配置文件有一个问题，那就是它仅仅适用于客户端代码（JavaScript），不是标准的 java 类。此外，GWT 是一个大的库，它是为了很多用途而设计，并不仅仅针对配置文件。 

#安装 
OWNER 是一个 java 库，即 jar 包。OWNER 可以在 Maven 仓库中获取，当然你也可以从这个连接下载它的库 jar 包、javadoc.jar、sources.jar 或者包含源码及 javadocs 的文件。下载好之后，你就需要配置你的 IDE 了。一般来说你只需要在 CLASSPATH 环境变量中引入它的 jar 包。 

#Maven 
在 Maven 项目中引用非常简单，只需要在项目 pom.xml 文件中添加以下依赖： 
'<dependencies>
    <dependency>
        <groupId>org.aeonbits.owner</groupId>
        <artifactId>owner</artifactId>
        <version>1.0.8</version>
    </dependency>
</dependencies>'
如果有更新的版本，只需要将新版本号替换 1.0.8 即可。写这篇文章时最新版本就是 1.0.8，但在使用时你最好检查下是否有新版本。
当前很多 IDE 都能很好的集成Maven，所以在 pom.xml 文件中增加了上述依赖并刷新项目后，你就可以使用 OWNER 了。

#Java 8 
java 8 引入了一些新的语言特性，比如接口中的 default methods。这种情况下 artifactId  需要使用 owber-java8 ：
'<dependencies>
        <dependency>
            <groupId>org.aeonbits.owner</groupId>
            <artifactId>owner-java8</artifactId>
            <version>1.0.6</version>
        </dependency>
</dependencies>'
由于 owner-java8 依赖于 owner，所以你不需要在 Maven 依赖中同时指定它们两个。 

#基本用法
要使用 OWNER API，你需要先定义一个与 properties 文件相关联的 java 接口类。
假设你定义的 properties 文件名字为 ServerConfig.properties：
'port=80
hostname=foobar.com
maxThreads=100'
你可以在同一个 package 下定义一个简单的 java 接口类 ServerConfig.java 来访问 properties 文件：
'import org.aeonbits.owner.Config;
public interface ServerConfig extends Config {
    int port();
    String hostname();
    @DefaultValue("42")
    int maxThreads();
}'





