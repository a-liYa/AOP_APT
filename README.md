# 安卓AOP三剑客之APT

> APT(Annotation Processing Tool 的简称)，可以在代码编译期解析注解，并且生成新的 Java 文件，减少手动的代码输入。现在有很多主流库都用上了 APT，比如 Dagger2, ButterKnife, EventBus3 等。

## 一、对比

![对比图](./doc/APT_AspectJ_Javassist.jpeg)

## 二、JavaPoet
> JavaPoet是square推出的开源java代码生成框架，提供Java Api生成.java源文件。这个框架功能非常有用，我们可以很方便的使用它根据注解、数据库模式、协议格式等来对应生成代码。通过这种自动化生成代码的方式，可以让我们用更加简洁优雅的方式要替代繁琐冗杂的重复工作。

项目主页及源码：https://github.com/square/javapoet

### 2.1 关键类说明

|class | 说明 | |
| :-------- | :------- | :--------|
|JavaFile	|A Java file containing a single top level class	|用于构造输出包含一个顶级类的Java文件|
|TypeSpec	|A generated class, interface, or enum declaration	|生成类，接口，或者枚举|
|MethodSpec	|A generated constructor or method declaration	|生成构造函数或方法|
|FieldSpec	|A generated field declaration	|生成成员变量或字段|
|ParameterSpec	|A generated parameter declaration	|用来创建参数|
|AnnotationSpec	|A generated annotation on a declaration	|用来创建注解|

## 三、相关使用

### 3.1 简单示例

引入库：
build.gradle

```
compile 'com.squareup:javapoet:1.9.0'
```

例子如下:

```
public final class HelloWorld {

    public static void main(String[] args) {
        System.out.println("Hello, JavaPoet!");
    }

}
```

上方的代码是通过下方代码调用JavaPoet的API生成的：

```
MethodSpec main = MethodSpec.methodBuilder("main")
    .addModifiers(Modifier.PUBLIC, Modifier.STATIC)
    .returns(void.class)
    .addParameter(String[].class, "args")
    .addStatement("$T.out.println($S)", System.class, "Hello, JavaPoet!")
    .build();

TypeSpec helloWorld = TypeSpec.classBuilder("HelloWorld")
    .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
    .addMethod(main)
    .build();

JavaFile javaFile = JavaFile.builder("com.example", helloWorld)
    .addFileComment(" This codes are generated automatically. Do not modify!")
    .build();

javaFile.writeTo(System.out);

```
### 3.2 创建annotations

> 创建Java library : apt-annotations, 专门定义注解的模块；


### 3.3 创建api

> 创建Android library : 依赖 apt-annotations 模块, 主要是api相关模块

### 3.4 创建compiler

> 创建Java library : 依赖 apt-annotations 模块;

```
dependencies {
    compile 'com.google.auto.service:auto-service:1.0-rc4'
    compile 'com.squareup:javapoet:1.9.0'
}
```

* 定义Processor

> 注解处理器（Annotation Processor）是javac的一个工具，它用来在编译时扫描和处理注解（Annotation）。你可以自定义注解，并注册相应的注解处理器（自定义的注解处理器需继承自AbstractProcessor）。

```
@AutoService(Processor.class)
public class HelloProcessor extends AbstractProcessor {
}
```

* auto-service

> Google提供了一个插件来帮助我们更方便的注册注解处理器，你只需要导入对应的依赖包，在自定义的Processor类上方添加@AutoService(Processor.class)即可。

### 3.5 app使用

* annotationProcessor

> 自Android Gradle 插件 2.2 版本开始，官方提供了名为 annotationProcessor 的功能来完全代替 android-apt。 用于处理注解处理器。

* 添加处理声明：
```
annotationProcessor project(':api-compiler')
```

* 代码生成路径
```
app/build/generated/source/apt/debug/
```