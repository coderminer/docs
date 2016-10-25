### Android文件储存系统 应用篇

  Android系统中为应用储存提供了不同的选择，开发者可以根据自己的需求选择不同类型的储存形式  
  系统本身提供了以下几种储存方式  

#### Shared Preferences

  以`key-value`的形式储存私人的原始数据,系统提供了两种方法来获取`SharedPreferences`对象  
  * getSharedPreferences(String prefer_name,int mode) 需要提供一个preference文件的名字
  * getPreferences(int mode) 不需要提供名字，应该是包名作为perference的名字
  mode 通常是 MODE_PRIVATE,这样其他的应用是无法获得这个文件的数据的,在`SharedPreferences`  
  储存的位置在 `/data/data/应用报名/sharef_pref/对应的perference的名字.xml`中  

  写入数据  
  1. 获得`edit()`一个 `SharedPreferences.Editor`
  2. 使用对应的方法写入不同类型的数据  
  3. 使用`commit()`方法，将数据写入

  `SharedPreferences` 提供的写入数据的方法如下,根据自己的需求使用对应的方法   

  ```
  SharedPreferences preference = this.getPreferences(MODE_PRIVATE);
  SharedPreferences.Editor editor = preference.edit();

  editor.putBoolean(String key, boolean value);
  editor.putFloat(String key, flost value);
  editor.putInt(String key, int value);
  editor.putLong(String key, long value);
  editor.putString(String key, String value);
  editor.putStringSet(String key, Set<String> values);

  editor.commit();
  ```

  读取，只需要使用`SharedPreferences`提供的get方法即可，此外还提供了其他的方法，`contains(String key)`  
  判断是否已经存在对应的key，`registerOnSharedPreferenceChangeListener`和`unregisterOnSharedPreferenceChangeListener`  
  监听对应的key值的变化，**只有当key对应的value值发生变化时，才会触发**，获取所有的值的方法`Map<String,?> getAll()`  

  ```
  SharedPreferences prefer = this.getPreferences(MODE_PRIVATE);
  //获取所有的数据，并遍历
  Map<String,?> all = prefer.getAll();
  for(Map.Entry<String, ?> entry : all.entrySet()){
      Log.e("tag", "prefer: "+entry.getKey()+" "+entry.getValue());
  }
  ```

  监听数据的变化,当key的value值发生变化时，才会触发回调，退出时记得接触注册 `unregisterOnSharedPreferenceChangeListener`

  ```
  preference.registerOnSharedPreferenceChangeListener(new OnSharedPreferenceChangeListener(){

            @Override
            public void onSharedPreferenceChanged(
                    SharedPreferences sharedPreferences, String key) {
                Log.e("tag", "=====================key "+key);
            }

  });
  ```

#### 内部储存

  你可以直接在设备的内部储存文件，默认的情况下，文件是私有的其他的应用无法访问，用户也无法访问，  
  当应用卸载时，对用的文件也将被删除  
  **创建文件**  
  * 调用 `openFileOutput()`,传入对应的文件名和访问模式，
  * 使用`write()`方法向文件写入数据
  * 关闭文件流 `close()`

  ```
  String FILENAME = "test";
  String content = "this a test for internal storage";
  try {
      FileOutputStream fos = this.openFileOutput(FILENAME, Context.MODE_PRIVATE);
      fos.write(content.getBytes());
      fos.close();
  } catch (FileNotFoundException e) {
      e.printStackTrace();
  } catch (IOException e) {
      e.printStackTrace();
  }
  ```
  常用的几种模式  

  * MODE_APPEND //如果文件也将存在，会在文件中追加数据，之前的数据会保留
  * MODE_PRIVATE //这个是默认的模式，只能应用本身才能访问，其他的应用无法访问
  * MODE_WORLD_READABLE //api 17之后也将不在呢使用，会导致安全漏洞，其他的应用也可以访问不建议使用在api 17 之后会引起安全异常
  * MODE_WORLD_WRITEABLE //其他的应用有写的权限，api 17之后也将废除，不建议使用，会引起安全漏洞

  **读取内容**  

  * 调用`openFileOutput` 传入对应的文件名
  * 调用`read()`方法
  * 关闭文件流`close()`

  ```
  try {
    FileInputStream fis = this.openFileInput(FILENAME);
    int len = fis.available();//获取文件长度
    byte[] buffer = new byte[len];
    fis.read(buffer);
    Log.e("tag", "=======result: "+new String(buffer));
    fis.close();
  } catch (FileNotFoundException e) {
      e.printStackTrace();
  } catch (IOException e) {
      e.printStackTrace();
  }
  ```

  **删除文件**  
  调用 `boolean result = this.deleteFile(FILENAME);`  ,只需要传入文件名即可  


#### 外部储存

  就是通常说的sdcard，访问sdcard需要相应的读写权限  
  ```
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
  ```
  在我们访问sdcard的储存之前，需要检查sdcard的状态是否可读,如果是可读的状态，我们就可以创建  
  文件并进行读写操作

  ```
  /* Checks if external storage is available for read and write */
  public boolean isExternalStorageWritable() {
      String state = Environment.getExternalStorageState();
      if (Environment.MEDIA_MOUNTED.equals(state)) {
          return true;
      }
      return false;
  }

  /* Checks if external storage is available to at least read */
  public boolean isExternalStorageReadable() {
      String state = Environment.getExternalStorageState();
      if (Environment.MEDIA_MOUNTED.equals(state) ||
          Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
          return true;
      }
      return false;
  }
  ```

  **外部私有数据**  
  有时也会将一些缓存数据放在外置sdcard中，`/storage/emulated/0/Android/data/com.example.demo/files`中  
  可以通过方法 `this.getExternalFilesDir(null)`方法来获得相应的路径，创建文件并进行读写，这个通过这个方法  
  创建的文件在查询卸载时也会对应的删除，这个方法也接受对应的参数  
