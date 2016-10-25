### 使用FileProvider安全的共享文件 简介篇

  `FileProvider`是`ContentProvider`的一个子类，可以通过`content://uri`的方式更安全的共享文件  
  可以在通过`URI`的方式共享文件时，可以通过`Intent`的`setFlags()`赋予文件临时的读写权限，不需要  
  设置全局的读写权限  

#### 定义一个`FileProvider`  

  不需要继承`FileProvider`,只需要在`AndroidManifest.xml`中定义一个`<provider>`的组件即可，设置  
  `android:name`的属性为`android.support.v4.content.FileProvider`,设置`android:authorities`  
  的属性为`packange_name.fileprovider`,设置`android:exported`的属性值为`false`,因为FileProvider  
  不需要公开，设置`android:grantUriPermissions="true"` 这样可以增加临时权限,在`meta-data`中指出  
  需要共享的文件夹路径  

  ```
  <provider
      android:name="android.support.v4.content.FileProvider"
      android:authorities="com.example.demo.fileprovider"
      android:exported="false"
      android:grantUriPermissions="true">
      <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/filepaths" />
  </provider>
  ```

#### 可以共享的文件

  在`meta-data`中需要指定需要共享的文件，有哪些文件可以通过`FileProvider`来共享呢，需要在  
  xml/filepaths.xml中配置相关的路径，一般的格式如下  

  ```
  <paths xmlns:android="http://schemas.android.com/apk/res/android">
    <files-path name="my_images" path="images/"/>
    ...
  </paths>
  ```

  `paths`中可以包含一个或多个子元素，`name="name"`这个属性相当于`path`的别名可以隐藏子目录  
  `path="path"`就是你要共享的目录  

  * 应用的内部资源

    可以通过`Context.getFilesDir()`获得的路径的子目录  

    ```
    <files-path name="name" path="path" />
    ```

  * 应用内部的缓存文件夹  

    可以通过`getCacheDir()`获得的路径的子目录    

    ```
    <cache-path name="name" path="path" />
    ```

  * sdcard文件  

    可以通过 `Environment.getExternalStorageDirectory()`获得的子目录  

    ```
    <external-path name="name" path="path" />
    ```

  * 应用的外部储存目录

    可以通过`Context.getExternalFilesDir(String)`获得的子目录  

    ```
    <external-files-path name="name" path="path" />
    ```

  * 应用的外部缓存目录

    可以通过`Context.getExternalCacheDir()`获得的子目录  

    ```
    <external-cache-path name="name" path="path" />
    ```

#### 生成文件的URI  

  为了和另外一个app利用`content URI`的方式共享文件，我们需要生成这个文件的URI，可以通过  
  `getUriForFile()`方法生成文件的URI  

  ```
  File imagePath = new File(Context.getFilesDir(), "images");
  File newFile = new File(imagePath, "default_image.jpg");
  Uri contentUri = getUriForFile(getContext(), "com.mydomain.fileprovider", newFile);
  ```

#### 赋予URI临时权限  

  * `Intent`的`setData()`方法，将数据放进`Intent`中
  * 通过`Intent`的`setFlags()`方法设置临时权限 `FLAG_GRANT_READ_URI_PERMISSION or FLAG_GRANT_WRITE_URI_PERMISSION`或者both
  * 将`Intent`发送到另一个app

#### 接受处理Intent

  另一个app接受处理对应的文件即可
