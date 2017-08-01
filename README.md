## 16.2 快速开始 Hello World
### 16.2.1 运行环境准备
我们直接去 Github上面去下载 kotlin-native 编译器的软件包。下载地址是 ：https://github.com/JetBrains/kotlin-native/releases 。
 

下载解压之后，我们可以看到 Kotlin Native 编译器 konan 的目录如下：
```
-rw-r--r--@  1 jack  staff   6828  6 20 22:47 GRADLE_PLUGIN.md
-rw-r--r--@  1 jack  staff  16286  6 20 22:47 INTEROP.md
-rw-r--r--@  1 jack  staff   1957  6 21 01:03 README.md
-rw-r--r--@  1 jack  staff   4606  6 20 22:47 RELEASE_NOTES.md
drwxr-xr-x@  8 jack  staff    272  6 20 23:04 bin
drwxr-xr-x   6 jack  staff    204  7 28 17:08 dependencies
drwxr-xr-x@  3 jack  staff    102  6 20 23:01 klib
drwxr-xr-x@  5 jack  staff    170  5 12 00:02 konan
drwxr-xr-x@  4 jack  staff    136  5 12 00:02 lib
drwxr-xr-x@ 22 jack  staff    748  6 22 19:04 samples
```

关于这个目录里面的内容我们在后面小节中介绍。

另外，我们也可以自己下载源码编译kotlin-native。

首先从github下载kotlin native源码，然后
cd kotlin-native

执行下载依赖包命令：
./gradlew dependencies:update

编译
./gradlew dist

至此，我们就得到了kotin的native编译器，它们在目录：./dist/bin。

### 16.2.2新建 Gradle 工程

在本小节中，我们先来使用IDEA 来创建一个普通的 Gradle 工程。
1）打开 File -> New -> Project，如图16-1所示。
 
图16-1 新建工程

2）新建Gradle项目。我们直接在左侧栏中选择 Gradle，点击 Next，如图16-2所示。
 
图16-2 选择 Gradle 项目类型

3）设置项目的 GroupId、ArtifactId、Version 信息，如图16-3所示。
 
图16-3 设置项目信息

4）配置 Gradle 项目的基本设置。我们直接选择本地的 Gradle 环境目录，省去下载的时间（有时候网络不好，要下载半天），具体配置如图16-4所示。
 
图16-4 Gradle 配置

5）配置项目名称和项目存放目录，点击 Finish，如图16-5所示。
 
图16-5 配置项目名称目录
6）等待 IDEA 创建完毕，我们将得到的Gradle 工程如图16-6所示。
 
图16-6 标准Gradle 空工程
现在这个工程里面什么都没有。下面我们就来开始原始的手工新建文件编码。

### 16.2.3 配置 KotlinNative Gradle 插件
首先，我们来配置 KotlinNative 的 Gradle 插件，这个插件集成了 Gradle 构建编译 KotlinNative 工程的功能。我们在 build.gradle 里面添加配置如下
```
buildscript {
    repositories {
        mavenCentral()
        maven {
            url "https://dl.bintray.com/jetbrains/kotlin-native-dependencies"
        }
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-native-gradle-plugin:0.3"
    }
}

apply plugin: 'konan'
```

该插件在dl.bintray.com仓库中，所以我们把这个仓库添加到构建仓库中。


### 16.2.4 配置 KotlinNative 运行环境

我们在工程根目录下新建gradle.properties文件，里面添加konan.home的配置
```
konan.home=/Users/jack/soft/kotlin-native-macos-0.3
```

### 16.2.5 编写 Hello.kt

我们编写 Kotlin 代码，放在源文件src/main/kotlin/Hello.kt中，代码内容如下
```
fun main(args: Array<String>) {
    println("Hello world")
}
```

### 16.2.6 konanArtifacts配置

使用 Gradle 构建之前，我们还需要在 build.gradle 中配置konanArtifacts
```
konanArtifacts {
    Hello { // 将会生成Hello.kexe 可执行文件

    }
}
```

### 16.2.7 构建生成kexe 可执行文件

我们直接点击 IDEA 的 Gradle 工具栏 Tasks -> build -> build 执行构建操作
 
此刻我们可以看到控制台输出

```
22:49:13: Executing external task 'build'...
:assemble UP-TO-DATE
:check UP-TO-DATE
:downloadKonanCompiler
:compileKonanHello UP-TO-DATE
:compileKonan UP-TO-DATE
:build UP-TO-DATE

BUILD SUCCESSFUL in 0s
2 actionable tasks: 1 executed, 1 up-to-date
22:49:13: External task execution finished 'build'.
```

### 16.2.8 运行测试

完成构建之后，在项目目录下生成一个 build 目录，这个目录结构如下
```
`$ tree build
build
└── konan
    └── bin
        ├── Hello.kexe
        └── Hello.kt.bc

2 directories, 2 files
```

其中，Hello.kexe就是我们得到的可执行程序。直接在命令行执行

```
$ build/konan/bin/Hello.kexe 
```
我们可以看到输出

Hello world

这就是我们通过使用KotlinNative 编译器，直接把 Kotlin 代码编译成机器码可执行程序的简单过程。
本节示例工程源码：https://github.com/EasyKotlin/chapter16_kotlin_native_helloworld_simple

