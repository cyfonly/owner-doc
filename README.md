# Welcome 
本文旨在对 owner 提供综合性的指导，包括基本功能、高级用法等主题，并且文中附有详细 demo。  

owner github：[https://github.com/lviggiano/owner](https://github.com/lviggiano/owner)  
英文文档地址：[http://owner.aeonbits.org/](http://owner.aeonbits.org/)  
中文文档地址：[http://www.cnblogs.com/cyfonly/p/5616489.html](http://www.cnblogs.com/cyfonly/p/5616489.html)  
　　更新列表：<a href="/update-list.md">update-list.md</a>

  

---  
#####             　　　　　　　　              　　　作者：Yunfeng Cheng  
#####　　　　　　　　　　owner 中文文档谨由本人维护，欢迎提出建议。（邮箱：869827095@qq.com）  
  
  

# 概述
owner 是个超轻量 java 库（jar包）， 旨在摒弃 properties 文件的样板代码，助你轻松管理项目配置： 
* 简便：无需加载多余代码，转换并管理 properties 配置文件，通过最简单的方式使用你的 properties
* 强大：基于注解，自动类型转换，变量扩展，加载策略，热加载，事件通知，等等
* 灵活：选择你需要的功能，屏蔽你不需要的；容易上手，具有丰富的文档 

# 目录
- [owner 是什么](https://github.com/cyfonly/owner/wiki/overview)
- [安装](https://github.com/cyfonly/owner/wiki/install)
	+ [maven](https://github.com/cyfonly/owner/wiki/install#user-content-maven)
	+ [java 8](https://github.com/cyfonly/owner/wiki/install#user-content-java-8)
- [基本用法](https://github.com/cyfonly/owner/wiki/basic_usage)
	+ [映射机制](https://github.com/cyfonly/owner/wiki/basic_usage#user-content-映射机制)
	+ [使用 Config 对象](https://github.com/cyfonly/owner/wiki/basic_usage#user-content-使用-config-对象)
	+ [使用 @DefaultValue 和 @Key 注解](https://github.com/cyfonly/owner/wiki/basic_usage#user-content-使用-defaultvalue-和-key-注解)
	+ [未定义属性](https://github.com/cyfonly/owner/wiki/basic_usage#user-content-未定义属性)
- [功能特性](https://github.com/cyfonly/owner/wiki/features)
	+ [加载策略](https://github.com/cyfonly/owner/wiki/features#user-content-加载策略)
	+ [引用属性](https://github.com/cyfonly/owner/wiki/features#user-content-引用属性)
	+ [参数化属性](https://github.com/cyfonly/owner/wiki/features#user-content-参数化属性)
	+ [类型转换](https://github.com/cyfonly/owner/wiki/features#user-content-类型转换)
	+ [变量扩展](https://github.com/cyfonly/owner/wiki/features#user-content-变量扩展)
	+ [加载和热加载](https://github.com/cyfonly/owner/wiki/features#user-content-加载和热加载)
	+ [可访问性和可变性](https://github.com/cyfonly/owner/wiki/features#user-content-可访问性和可变性)
	+ [程序调试](https://github.com/cyfonly/owner/wiki/features#user-content-程序调试)
	+ [禁用功能](https://github.com/cyfonly/owner/wiki/features#user-content-禁用功能)
	+ [配置工厂](https://github.com/cyfonly/owner/wiki/features#user-content-配置工厂)
	+ [XML支持](https://github.com/cyfonly/owner/wiki/features#user-content-xml支持)
	+ [事件支持](https://github.com/cyfonly/owner/wiki/features#user-content-事件支持)
	+ [单例](https://github.com/cyfonly/owner/wiki/features#user-content-单例)


  
