### Android应用签名

#### 生成签名文件

  需要配置好Java的开发环境-`JDK`,JDK的环境配置请自行搜索，配置好之后，需要用到`keytool`  
  命令来生成签名文件，`keytool`的使用方法  

  ![keytool用法]()

  具体的命令如下:  

  ```
  keytool -genkey -alias test -keyalg RSA -validity 20000 -keystore test.keystore                                                                                     
  ```

  这样在当前目录就会生成一个test.keystore的签名文件，`-alias`后面的别名需要记下，后续会使用到  

#### 签名

  * 命令行签名

  使用`jarsigner`命令来执行签名，如果使用`Eclipse`开发，默认情况下`Eclipse`会签一个debug的签名  
  在使用`jarsigner`签名时，不需要这个dubug的签名(我在这里纠结了好久，一直签名不成功)  
  使用`Eclipse`导出没有签名的apk文件

  ![Eclipse导出没有签名的apk]()

  然后使用`jarsigner`命令来对apk签名即可,`jarsigner`命令的使用方法  

  ```
  jarsigner -verbose -keystore test.keystore -signedjar WeixinSdk_signed.apk WeixinSdk.apk test
  ```

  执行之后，会在当前目录生成一个签名之后的文件 WeixinSdk_signed.apk

  * 使用gui工具签名(Eclipse)

    使用`Eclipse`导出签名的apk  
    ![Eclipse导出签名apk]()

    选择之前生成的签名文件(也可以重新生成一个新的签名文件)  
    ![选择签名文件]()

    填写密码  
    ![填写密码]()

    选择alias  
    ![选择alias]()

    选择保存路径  
    ![保存路径]()

#### 常见问题
