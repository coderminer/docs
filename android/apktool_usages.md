### apktool 的使用

  [Apktool](https://ibotpeaches.github.io/Apktool/)用来反编译Android的Apk文件，最大程度的还原apk中的manifest文件和资源文件  
  还可以将反编译之后的apk重新打包成apk文件，但需要重新签名，才能安装使用

### apktool安装

  最新版本的apktool请下载 [网盘下载](http://pan.baidu.com/s/1miKy4DM)，或者去 [官网](https://ibotpeaches.github.io/Apktool/install/) 查看安装步骤

### 使用详解

  * 反编译APK文件  `apktool d test.apk`

  ```
  $ apktool d test.apk
  I: Using Apktool 2.2.0 on test.apk
  I: Loading resource table...
  I: Decoding AndroidManifest.xml with resources...
  I: Loading resource table from file: 1.apk
  I: Regular manifest package...
  I: Decoding file-resources...
  I: Decoding values */* XMLs...
  I: Baksmaling classes.dex...
  I: Copying assets and libs...
  I: Copying unknown files...
  I: Copying original files...
  ```

  会生成一个和apk同名为文件夹  

  ```
  AndroidManifest.xml
  apktool.yml
  assets/
  original/
  res/
  smali/
  ```

  然后就可以查看manifest文件和资源文件

  * apk重新打包 `apktool b test`

  想把反编译之后的apk重新打包成一个apk文件，执行上面的命令之后，会在原文件夹中生成两个  
  文件夹 build/ dist/ 在dist中有重新生成的apk文件，生成的apk文件需要重新签名
