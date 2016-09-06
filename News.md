  
# Owner 1.0.9 发布
2015-07-22
### v1.0.9 版本包含以下改进和 bug 修复：
#### 改进：
* `Accessible` 接口增加 `fill(java.util.Map)` 方法。
* 增加预处理功能。请看[#120](https://github.com/lviggiano/owner/issues/120)，感谢[@a1730](https://github.com/skarnet/s6)的反馈。
  
#### 网站改进
* 无  
  
#### bug修复：
* 在 Windows 环境下 Config.Sources 中使用 ~ 没有生成正确的 URI。请看[#123](https://github.com/lviggiano/owner/issues/123)，感谢[@outofrange](https://github.com/lviggiano/owner/issues/123)报告了这个 bug。  
  
可下载文件已经发布到[Github](https://github.com/lviggiano/owner/releases/tag/owner-parent-1.0.9)和[Maven Central Repository](http://repo1.maven.org/maven2/org/aeonbits/owner/owner-assembly/1.0.9/)。
  
  
# Owner 1.0.8 发布  
2015-03-31  
```
1.0.7版本未能成功发布到 Maven Central Repository。
一些所需的 POM 被调过，如果你想在项目中作为依赖使用，它可能会抛出一些 Maven 错误或其他问题。所以修补程序 1.0.8 发布了！
```  
### v1.0.8 版本包含以下改进和 bug 修复：  
#### 改进：
* 修复了包含在 tarball/zip 版本中 javadoc 文档。
  
#### 网站改进
* 无  
  
#### bug修复：
* 在 Maven Central Repository 中没有 `owner-parent` POM。请看[#121](https://github.com/lviggiano/owner/issues/121)，感谢[@rajatvig ](https://github.com/rajatvig)快速地报告了这个问题。
  
可下载文件已经发布到[Github](https://github.com/lviggiano/owner/releases/tag/owner-parent-1.0.9)和[Maven Central Repository](http://repo1.maven.org/maven2/org/aeonbits/owner/owner-assembly/1.0.9/)。  

  
# Owner 1.0.7 发布  
2015-03-30  
```
1.0.7版本未能成功发布到 Maven Central Repository。
一些所需的 POM 被调过，如果你想在项目中作为依赖使用，它可能会抛出一些 Maven 错误或其他问题。所以避免使用 1.0.7，请使用 1.0.8！
```  
### v1.0.7 版本包含以下改进和 bug 修复：  
#### 改进：
* 增加 JMX 支持。请看[#107](https://github.com/lviggiano/owner/pull/107)和[#19](https://github.com/lviggiano/owner/issues/19)。感谢[@robinmeiss](https://github.com/robinmeiss)。我还需要编写文档来说明如何使用这个功能（很抱歉）。
* 增加 examples 模块，包括一些 Maven java 项目的 example 来展示一些 API 的功能。这些都打包在[发布文件](https://github.com/lviggiano/owner/releases/tag/owner-parent-1.0.7)中。
  
#### 网站改进
* 无  
  
#### bug修复：
* 修复打包：`owner-extras.jar` 丢失了必要的 class 文件。请看[#114](https://github.com/lviggiano/owner/issues/114)，感谢[@ksaritek](https://github.com/ksaritek)的耐心。
  
可下载文件已经发布到[Github](https://github.com/lviggiano/owner/releases/tag/owner-parent-1.0.9)和[Maven Central Repository](http://repo1.maven.org/maven2/org/aeonbits/owner/owner-assembly/1.0.9/)。  

  
  
