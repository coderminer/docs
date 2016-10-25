### 监听外置sdcard(TF卡)的插拔事件  

  在编码的过程中需要监听外置sdcard的插拔事件，在`AndroidManifest.xml`中注册一个静态广播  
  一定要添加上 `<data android:scheme="file" />`  
  
  ```
  <receiver android:name=".SdcardReceiver" android:enabled="true">
  <intent-filter>
      <action android:name="android.intent.action.MEDIA_MOUNTED" />
        <action android:name="android.intent.action.MEDIA_UNMOUNTED" />
        <data android:scheme="file" />
  </intent-filter>

  </receiver>
  ```

  在java代码中继承 `BroadcastReceiver`,监听到sdcard的插拔事件，然后做相应的逻辑操作  

  ```
  public class SdcardReceiver extends BroadcastReceiver{

    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if(action.equals(Intent.ACTION_MEDIA_MOUNTED)){
            Log.d("tag","sdcard mounted");
        }else if(action.equals(Intent.ACTION_MEDIA_UNMOUNTED)){
            Log.d("tag","sdcard unmounted");
        }
    }

  }
  ```
