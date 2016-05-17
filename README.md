#概述
OWNER旨在摒弃properties配置文件的样板代码 
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


