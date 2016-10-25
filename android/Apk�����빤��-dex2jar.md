### Apk反编译工具-dex2jar

  可以使用dex2-jar 和 工具 jd-gui配合进行apk的反编译和查看工作  

  下载dex2-jar和jd-gui工具 [网盘下载](http://pan.baidu.com/s/1i5mECwH) 下载之后分别解压到  
  对应的文件目录

  apk本身就是一个压缩文件，通过rar或zip工具将apk中的classes.dex文件解压到 dex2-jar的目录下面  
  cd 到 dex2-jar 目录，使用命令 `d2j-dex2jar.bat`(windows) `d2j-dex2jar.sh`(linux)执行完命令  
  之后，会生成一个 classes-dex2jar.jar 文件，apk的内部结构如图  

  ![]()

  然后我们会使用到 jd-gui工具，本事是一个可执行文件，windows下双击就可以打开，然后打开上面  
  生成的classes-dex2jar.jar文件，就可以查看对应的java代码，当然如果代码已经进行了混淆，代码有可能  
  无法查看，请知悉  
