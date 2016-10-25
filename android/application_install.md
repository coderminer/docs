### 监听应用程序的安装、卸载

  在`AndroidManifest.xml`中注册一个静态广播,监听安装的广播
  `android.intent.action.PACKAGE_ADDED`  监听程序卸载的广播  
  `android.intent.action.PACKAGE_REMOVED` ,在广播中一定要加上 `<data android:scheme="package" />`  
  不然就监听不到

  ```
  <receiver
    android:name=".AppInstallReceiver" android:enabled="true">
    <intent-filter>  
       <action android:name="android.intent.action.PACKAGE_ADDED" />  
       <action android:name="android.intent.action.PACKAGE_REMOVED" />  
       <data android:scheme="package" />  
    </intent-filter>
  </receiver>
  ```

  在java代码中，需要写一个类继承 `BroadcastReceiver`   


  ```
  public class AppInstallReceiver extends BroadcastReceiver{


    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if(action.equals(Intent.ACTION_PACKAGE_ADDED)){
          Log.d("tag","app installed ");
        }else if(action.equals(Intent.ACTION_PACKAGE_REMOVED)){
          Log.d("tag","app uninstalled");
        }
    }

  }
  ```


  可以通过`intent`获取应用的报名  
  `String pkgName = intent.getDataString().substring(8)`