# 工具

出于实用，我们引入了Lombok 和 Butter Knife。这两个可以帮助我们在Android应用程序中少写许多模板类代码。

### Lombok

Lombok使用注解的方式为你生成许多代码。大多数情况下我们使用其生成`getter/setter`、`toString()`、`equals()`、`hashCode()`。它来自于一个Gradle依赖和Android Studio插件。

### Butter Knife

Butter Knife使用注解的方式来帮助我们免去写`findViewById()`和设置点击监听的痛苦。至于Lombok,我们可以通过导入依赖和安装Android Studio插件来获得更好的体验。

### Retrolambda

最后，我们导入Retrolambda，是因为我们开发的Android是基于Java 1.6，然后我们可以借助它来实现Java 8 Lambda函数从而减少许多模板代码。