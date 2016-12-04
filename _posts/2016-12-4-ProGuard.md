---
layout: post
title: ProGuard简述
categories: [Android]
description: 
keywords: ProGuard
---
Proguard的作用是对Java类文件进行压缩、优化、混淆和预确认。压缩包括移除未使用的类、字段、属性。优化包括分析和优化字节码。混淆是指使用语义隐晦的名称来重命名类、字段和方法，从而达到压缩、优化和混淆代码的目的。这样使应用更难以进行逆向工程。

## 配置ProGuard文件

通常情况下，使用proguard.cfg或proguard-android.txt的默认配置可以满足需求。然而，有时Proguard无法正确的判断，可能会移除掉它认为没用而应用实际需要的代码。
此时可以通过修改proguard.cfg或proguard-android.txt来修复因为Proguard删除代码而造成的错误。例如：

```
-keep public class <MyClass>
```
在修改proguard配置时，最好参考[ProGuard官方文档](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html)

### 常用配置
一些常用的配置如下

```
-include {fileame}  从给定的文件中读取配置参数
-libraryjars libs/xxxx.jar 指定库jar包
-keep public class * extends android.app.Activity 保留类不被删除
-keep class className$InnerName{ 保留内部类的属性和方法
    public <fields>;
    public <methods>;
}
-keepclassmembers class * implements java.io.Serializable { 保留类的成员
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}
-dontshrink 不压缩输入的类文件
-dontoptimize 不优化输入的类文件
-keepattributes *Annotation* 保留Annotation
-dontwarn xxx.xxx.** 不检查引用
```
### ProGurad的输出

当混淆后的代码输出堆栈跟踪信息时，方法名称会被混淆，即便仍能进行调试，难度也会很大。幸运的是，ProGuard 在每次运行时都会输出以下文件：

#### dump.txt

描述 .apk 文件中所有类文件的内部结构

#### mapping.txt

列出原始与混淆后的类、方法和字段名称之间的对应关系。

Windows 上的 retrace.bat 脚本以及 Linux 或 Mac OS X 上的 retrace.sh 脚本可以将混淆后的堆栈跟踪信息转换成可读文件，此文件位于 /tools/proguard/ 目录中。执行 retrace 工具的语法如下：
retrace.bat|retrace.sh [-verbose] mapping.txt [<stacktrace_file>]
例如：
retrace.bat -verbose mapping.txt obfuscated_trace.txt
建议发布时应保留mapping.txt文件。

#### seeds.txt

列出未混淆的类和成员

#### usage.txt

列出从 .apk 删除的代码

#### @Keep注解来防止混淆

介绍一种比较新颖、轻快的方法，通过@Keep注解来灵活的防止混淆，用起来非常的灵活、快捷、方便，怎样用呢？像普通的注解一样，如下：


```java
//防止混淆类
@Keep
public class Person {}

//防止混淆变量
@Keep
public String name;

//防止混淆方法
@Keep
public int getAge(){}
```


原因目前Gradle还不支持@Keep混淆，Google只是定义好了一个这种注解，并没有实现它，也就是说@Keep目前只是一个空壳。这里我们来手动开启它，让它支持防止混淆，在你的proguard.cfg或proguard-android.txt配置文件里面加入以下代码：

```
#手动启用support keep注解
-dontskipnonpubliclibraryclassmembers
-printconfiguration
-keep,allowobfuscation @interface android.support.annotation.Keep
-keep @android.support.annotation.Keep class *
-keepclassmembers class * {
    @android.support.annotation.Keep *;
}
```


### 语法DOME
```
##--- For:android默认 ---
-optimizationpasses 5  # 指定代码的压缩级别
-allowaccessmodification #优化时允许访问并修改有修饰符的类和类的成员
-dontusemixedcaseclassnames  # 是否使用大小写混合
-dontskipnonpubliclibraryclasses  # 是否混淆第三方jar
-dontpreverify  # 混淆时是否做预校验
-verbose    # 混淆时是否记录日志
-ignorewarnings  # 忽略警告，避免打包时某些警告出现
-optimizations !code/simplification/arithmetic,!code/simplification/cast,!field/*,!class/merging/*  # 混淆时所采用的算法

-keepattributes *Annotation*
-keep public class com.google.vending.licensing.ILicensingService
-keep public class com.android.vending.licensing.ILicensingService
-keepclasseswithmembernames class * { # 保持 native 方法不被混淆
    native <methods>;
}

-keepclassmembers public class * extends android.view.View {
   void set*(***);
   *** get*();
}

-keepclassmembers class * extends android.app.Activity {
   public void *(android.view.View);
}

-keepclassmembers enum * {  # 保持枚举 enum 类不被混淆
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

-keep class * implements android.os.Parcelable { # 保持 Parcelable 不被混淆
  public static final android.os.Parcelable$Creator *;
}

-keepclassmembers class **.R$* { #不混淆R文件
    public static <fields>;
}

-dontwarn android.support.**
##--- End android默认 ---

##--- For:不能被混淆的 ---
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Fragment
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference

##--- For:保持自定义控件类不被混淆 ---
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
}
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
##--- For:android-support-v4 ---
-dontwarn android.support.v4.**
-keep class android.support.v4.** { *; }
-keep interface android.support.v4.app.** { *; }
-keep class * extends android.support.v4.** { *; }
-keep public class * extends android.support.v4.**
-keep public class * extends android.support.v4.widget
-keep class * extends android.support.v4.app.** {*;}
-keep class * extends android.support.v4.view.** {*;}

##--- For:Serializable ---
-keep class * implements java.io.Serializable {*;}
-keepnames class * implements java.io.Serializable
-keepclassmembers class * implements java.io.Serializable {*;}

##--- For:Gson ---
-keepattributes *Annotation*
-keep class sun.misc.Unsafe { *; }
-keep class com.idea.fifaalarmclock.entity.***
-keep class com.google.gson.stream.** { *; }

##--- For:Remove log ---
-assumenosideeffects class android.util.Log {
    public static boolean isLoggable(java.lang.String, int);
    public static int v(...);
    public static int i(...);
    public static int w(...);
    public static int d(...);
    public static int e(...);
}

##--- For:attributes(未启用) ---
#-keepattributes SourceFile,LineNumberTable # 保持反编译工具能看到代码的行数，以及release包安装后出现异常信息可以知道在哪行代码出现异常，建议不启用
-keepattributes *Annotation* #使用注解
-keepattributes Signature #过滤泛型  出现类型转换错误时，启用这个
#-keepattributes *Exceptions*,EnclosingMethod  #没试过，未知效果
```


