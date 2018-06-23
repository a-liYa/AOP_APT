# 安卓AOP三剑客之APT

> APT(Annotation Processing Tool 的简称)，可以在代码编译期解析注解，并且生成新的 Java 文件，减少手动的代码输入。现在有很多主流库都用上了 APT，比如 Dagger2, ButterKnife, EventBus3 等。


通常创建三个Library：

* apt-annotations `Java library `

* apt-api `Android library` `compile 'apt-annotations'`

* apt-compiler `Java library` `compile 'apt-annotations'`


