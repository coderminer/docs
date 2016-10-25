### 设置系统日期时间和时区

  设置系统的日期时间和时区，需要 **系统权限和系统签名**，`android:sharedUserId="android.uid.system"`
  需要在manifest文件中添加相应的权限  

  ```
  <uses-permission android:name="android.permission.WRITE_SETTINGS"/>
  <uses-permission android:name="android.permission.WRITE_SECURE_SETTINGS"/>
  ```

* 判断系统使用的是24小时制还是12小时制

  ```
  boolean is24Hour =  DateFormat.is24HourFormat(mContext);
  ```
* 设置系统的小时制


  24小时制  

  ```
  android.provider.Settings.System.putString(mContext.getContentResolver(),
                android.provider.Settings.System.TIME_12_24, "24");

  ```

  12小时制  

  ```
  android.provider.Settings.System.putString(mContext.getContentResolver(),
                android.provider.Settings.System.TIME_12_24, "12");
  ```

* 判断系统的时区是否是自动获取的

  ```
  public boolean isTimeZoneAuto(){
    try {
        return  android.provider.Settings.Global.getInt(mContext.getContentResolver(),
                android.provider.Settings.Global.AUTO_TIME_ZONE) > 0;
    } catch (SettingNotFoundException e) {
        e.printStackTrace();
        return false;
    }
  }
  ```

* 设置系统的时区是否自动获取

  ```
  public void setAutoTimeZone(int checked){
    android.provider.Settings.Global.putInt(mContext.getContentResolver(),
            android.provider.Settings.Global.AUTO_TIME_ZONE, checked);
  }
  ```

* 判断系统的时间是否自动获取的

  ```
  public boolean isDateTimeAuto(){
    try {
        return android.provider.Settings.Global.getInt(mContext.getContentResolver(),
                android.provider.Settings.Global.AUTO_TIME) > 0;
    } catch (SettingNotFoundException e) {
        e.printStackTrace();
        return false;
    }
  }
  ```

* 设置系统的时间是否需要自动获取

  ```
  public void setAutoDateTime(int checked){
    android.provider.Settings.Global.putInt(mContext.getContentResolver(),
            android.provider.Settings.Global.AUTO_TIME, checked);
  }
  ```

* 设置系统日期

  参考系统Settings中的源码  
  
  ```
  public void setSysDate(int year,int month,int day){
    Calendar c = Calendar.getInstance();
    c.set(Calendar.YEAR, year);
    c.set(Calendar.MONTH, month);
    c.set(Calendar.DAY_OF_MONTH, day);

    long when = c.getTimeInMillis();

    if(when / 1000 < Integer.MAX_VALUE){
        ((AlarmManager)mContext.getSystemService(Context.ALARM_SERVICE)).setTime(when);
    }
  }
  ```

* 设置系统时间

  参考系统Settings中的源码

  ```
  public void setSysTime(int hour,int minute){
    Calendar c = Calendar.getInstance();
    c.set(Calendar.HOUR_OF_DAY, hour);
    c.set(Calendar.MINUTE, minute);
    c.set(Calendar.SECOND, 0);
    c.set(Calendar.MILLISECOND, 0);

    long when = c.getTimeInMillis();

    if(when / 1000 < Integer.MAX_VALUE){
        ((AlarmManager)mContext.getSystemService(Context.ALARM_SERVICE)).setTime(when);
    }
  }
  ```

* 设置系统时区

  ```
  public void setTimeZone(String timeZone){
    final Calendar now = Calendar.getInstance();
    TimeZone tz = TimeZone.getTimeZone(timeZone);
    now.setTimeZone(tz);
  }
  ```

* 获取系统当前的时区

  ```
  public String getDefaultTimeZone(){
    return TimeZone.getDefault().getDisplayName();
  }
  ```
