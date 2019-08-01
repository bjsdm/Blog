# Gradle 学记
## Gradle基础
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1563448886426&di=f6681f9f9194e24b4c93fbf2bf443046&imgtype=0&src=http%3A%2F%2Fwww.liuhaihua.cn%2Fwp-content%2Fuploads%2F2019%2F06%2FyaEFV3u.png)
> 语言基于Groovy的领域专用语言(DSL)
### 项目和任务
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1563448965352&di=953b16291c1b6cf6a4cd6193afa63fce&imgtype=0&src=http%3A%2F%2Failianblog.oss-cn-shenzhen.aliyuncs.com%2Fbolg_gradle_1.png)
- 每一次构建都包括最少一个项目
- 一个项目又包括一个或多个任务
- 一个任务对象包含一系列动作对象
  * 按顺序执行
 
 ### 生命周期
 ![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1563449213326&di=36fca0048cf5787300c5be2e972ad664&imgtype=0&src=http%3A%2F%2Fwww.qingpingshan.com%2Fuploads%2Fallimg%2F161209%2F15540B5a-0.png)
 -  初始化
 -  配置
 -  执行
 ### 构建配置文件
 > 每一个Gradle构建的项目,至少有一个build.gradle文件

**整体项目配置**
 ```Groovy
 buildscript {  
  repositories {  
  //依赖仓库
  google()  
  jcenter()  
     }  
  dependencies {  
  classpath 'com.android.tools.build:gradle:3.5.0-beta05'  
//        classpath 'com.android.tools.build:gradle:3.6.0-alpha03'  
  classpath 'com.jakewharton:butterknife-gradle-plugin:10.1.0'  
  // NOTE: Do not place your application dependencies here; they belong  
 // in the individual module build.gradle files  }
 ```
 **软件模块**
 ```Groovy
//应用相关插件
apply plugin: 'com.jakewharton.butterknife'  
apply plugin: 'com.android.application' //应用安卓插件  
//应用安卓插件,就可以配置安卓特殊约定参数
android {  
  compileSdkVersion 29  
  buildToolsVersion "29.0.0"  
  defaultConfig {  
  applicationId "com.example.b2cmarket"  
  minSdkVersion 26  
  targetSdkVersion 29  
  versionCode 1  
  versionName "1.0"  
  testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"  
  }  
  buildTypes {  
  release {  
  minifyEnabled false  
  proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'  
  }  
 }  compileOptions {  
  sourceCompatibility JavaVersion.VERSION_1_8  
  targetCompatibility JavaVersion.VERSION_1_8  
  }  
}  
  
dependencies {  
  implementation fileTree(dir: 'libs', include: ['*.jar'])  
  implementation 'androidx.appcompat:appcompat:1.0.2'  
  implementation 'androidx.constraintlayout:constraintlayout:1.1.3'  
  testImplementation 'junit:junit:4.12'  
 ```
 ### 项目结构
 ![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3762202780,3235020683&fm=15&gp=0.jpg)
 
