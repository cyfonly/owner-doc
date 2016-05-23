#Welcome 
本文旨在对 owner 提供综合性的指导，包括基本功能、高级用法等主题，并且文中附有详细 demo。  

owner github：[https://github.com/lviggiano/owner](https://github.com/lviggiano/owner)  
英文文档地址：[http://owner.aeonbits.org/](http://owner.aeonbits.org/)  

#概述
owner 是个超轻量 java 库（jar包）， 旨在摒弃 properties 文件的样板代码，助你轻松管理项目配置： 
* 简便：无需加载多余代码，转换并管理 properties 配置文件，通过最简单的方式使用你的 properties
* 强大：基于注解，自动类型转换，变量扩展，加载策略，热加载，事件通知，等等
* 灵活：选择你需要的功能，屏蔽你不需要的；容易上手，具有丰富的文档 

#功能
> 简单的事情就应该是简单的，复杂的事情就应该是可能的。  
>                           —— 天才计算机大师 艾伦.凯 

这也是开发 owner 的宗旨。  
owner是个功能丰富的 API，但在增加更多的功能之前，我们尽可能的保持它现有基本用法的简单化。 
owner API 支持一系列功能，如下：
* 加载策略
* 引用属性
* 参数化属性
* 类型转换
* 变量扩展
* 加载和热加载
* 可访问性和可变性
* 程序调试
* 禁用功能
* 元配置
* XML支持
* 事件支持
* 单例

新特性的开发不希望将已有特性变得复杂，版本的向后兼容也在我们的目标当中。

#owner 是什么
owner 是一个 java 库，旨在减少通过 properties 文件来处理应用程序配置的代码。它已经打包成一个 jar 文件，你也可以从 Maven 中央仓库下载。开发此 API 是受 GWT i18n 启迪（[点此查看](http://www.gwtproject.org/doc/latest/DevGuideI18nConstants.html)）。使用 GWT i18n 来加载配置文件有一个问题，那就是它仅仅适用于客户端代码（JavaScript），不是标准的 java 类。此外，GWT 是一个大的库，它是为了很多用途而设计，并不仅仅针对配置文件。  

#安装
owner 是一个 java 库，即 jar 包。owner 可以在 Maven 仓库中获取，当然你也可以从这个连接下载它的库 jar 包、javadoc.jar、sources.jar 或者包含源码及 javadocs 的文件。下载好之后，你就需要配置你的 IDE 了。一般来说你只需要在 CLASSPATH 环境变量中引入它的 jar 包。

#Maven
在 Maven 项目中引用非常简单，只需要在项目 pom.xml 文件中添加以下依赖：
```
<dependencies>
　　<dependency>
　　　　<groupId>org.aeonbits.owner</groupId>
　　　　<artifactId>owner</artifactId>
　　　　<version>1.0.8</version>
　　</dependency>
</dependencies>  
```






