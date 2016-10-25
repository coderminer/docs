### 对apk进行系统签名

  签名工具 [网盘下载](http://pan.baidu.com/s/1o8bFh6U) ，需要Android系统的签名的文件  
  `platform.x509.pem` 和 `platform.pk8` 这个两个文件在Android源码中的 `./build/target/product/security`  
  目录下  

  具体的使用方法：  

  > java -jar signapk.jar platform.x509.pem platform.pk8 unsign.apk signed.apk

  最后生成的apk就是已经进行系统签名的apk
