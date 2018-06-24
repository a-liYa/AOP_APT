# 安卓AOP三剑客之APT

> APT(Annotation Processing Tool 的简称)，可以在代码编译期解析注解，并且生成新的 Java 文件，减少手动的代码输入。现在有很多主流库都用上了 APT，比如 Dagger2, ButterKnife, EventBus3 等。


通常创建三个Library：

* apt-annotations `Java library `

* apt-api `Android library` `compile 'apt-annotations'`

* apt-compiler `Java library` `compile 'apt-annotations'`


## 2 JavaPoet
> JavaPoet是square推出的开源java代码生成框架，提供Java Api生成.java源文件。这个框架功能非常有用，我们可以很方便的使用它根据注解、数据库模式、协议格式等来对应生成代码。通过这种自动化生成代码的方式，可以让我们用更加简洁优雅的方式要替代繁琐冗杂的重复工作。

项目主页及源码：https://github.com/square/javapoet

#### 2.2 关键类说明

|class | 说明 | |
| :-------- | :------- | :--------|
|JavaFile	|A Java file containing a single top level class	|用于构造输出包含一个顶级类的Java文件|
|TypeSpec	|A generated class, interface, or enum declaration	|生成类，接口，或者枚举|
|MethodSpec	|A generated constructor or method declaration	|生成构造函数或方法|
|FieldSpec	|A generated field declaration	|生成成员变量或字段|
|ParameterSpec	|A generated parameter declaration	|用来创建参数|
|AnnotationSpec	|A generated annotation on a declaration	|用来创建注解|

#### 3 相关使用

##### 简单示例

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