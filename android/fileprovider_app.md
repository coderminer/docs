### 使用FileProvider安全的共享文件 应用篇

  `FileProvider`的具体使用的方式，需要一个`Activity`来接受`client app`发送的请求，首先  
  创建一个`Activity`-`FileSelectActivity`将文件夹中的图片显示出来，另外一个`Activity`-`MainActivity`接收  
  选中的图片，并处理  

  * 设置`AndroidManifest.xml`

    添加组件`provider`  
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

    在`res/xml`文件夹中添加文件`filepaths.xml`，我们要共享的是外置sdcard的文件，paths中设置的是  
    `external-path`

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <paths xmlns:android="http://schemas.android.com/apk/res/android">
        <external-path path="images/" name="myimages" />
    </paths>
    ```

  * `FileSelectActivity`的设置

    新建`FileSelectActivity`,在`AndroidManifest`中设置相关的属性信息  

    ```
    <activity
      android:name=".FileSelectActivity">
      <intent-filter>
          <action android:name="android.intent.action.PICK" />
          <category
                      android:name="android.intent.category.DEFAULT"/>
                  <category
                      android:name="android.intent.category.OPENABLE"/>
                  <data android:mimeType="text/plain"/>
                  <data android:mimeType="image/*"/>
      </intent-filter>
    </activity>
    ```

    在`FileSelectActivity.java`中显示文件列表，接收`PICK`的intent，显示要分享的文件列表  
    具体的逻辑请看代码和相关的注释信息，我们可以看到我们共享的文件的uri信息如下,`myimages`是我们  
    在`res/xml/filepaths.xml`中配置的`name`属性，儿真正的文件夹信息`images`被隐藏了  

    ```
    content://com.example.demo.fileprovider/myimages/Screenshot_2015-08-04-17-44-38.png
    ```

    ```
    private File mRootDir;
    private File mImagesDir;
    private File[] mImageFiles;
    private List<String> mImagesNames = new ArrayList<String>();
    private ListView mFileList ;
    private Uri fileUri;
    private Intent mResultIntent ;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.file_select);

    mFileList = (ListView)findViewById(R.id.file_list);

    mResultIntent = new Intent();

    mRootDir = Environment.getExternalStorageDirectory();
    mImagesDir = new File(mRootDir,"images");
    mImageFiles = mImagesDir.listFiles();

    ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,android.R.layout.simple_list_item_1, getFileNames());
    mFileList.setAdapter(adapter);
    setResult(RESULT_CANCELED, null);

    mFileList.setOnItemClickListener(new OnItemClickListener(){

        @Override
        public void onItemClick(AdapterView<?> parent, View view,
                int position, long id) {
            File requestFile = new File(mImagesNames.get(position)); //获得选择的图片
            fileUri = FileProvider.getUriForFile(FileSelectActivity.this, "com.example.demo.fileprovider", requestFile); //获得文件的uri

            if(null != fileUri){
                mResultIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); //设置临时权限
                mResultIntent.setDataAndType(fileUri, FileSelectActivity.this.getContentResolver().getType(fileUri));//设置data和type信息
                FileSelectActivity.this.setResult(RESULT_OK,mResultIntent);
                finish();
            }
        }

    });
    }

    private List<String> getFileNames(){
        for(File f:mImageFiles){
            mImagesNames.add(f.getAbsolutePath());
        }

        return mImagesNames;
    }
    ```

  * `MainActivity`中接收返回的图片的信息，并处理  

    启动`FileSelectActivity`  
    ```
    mRequestFileIntent = new Intent(Intent.ACTION_PICK);
    mRequestFileIntent.setType("image/*");
    startActivityForResult(mRequestFileIntent, 0);
    ```

    在 `onActivityResult`方法中处理  

    ```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if(resultCode == RESULT_OK){
            Uri returnUri = data.getData();
            if(returnUri != null){
                //FileDescriptor fd = getContentResolver().openFileDescriptor(returnUri, "r").getFileDescriptor();
                //可以通过uri获得文件的FileDescriptor，然后再做相关的文件的处理

                //也可以通过uri获得文件的Cursor信息，并获得文件的相关信息
                String mimeType = getContentResolver().getType(returnUri);
                Log.e("tag", "=============mime type: "+mimeType);
                Cursor c = this.getContentResolver().query(returnUri, null, null, null, null);
                if(null != c && c.moveToFirst()){
                    int nameIndex = c.getColumnIndex(OpenableColumns.DISPLAY_NAME);
                    int sizeIndex = c.getColumnIndex(OpenableColumns.SIZE);

                    Log.e("tag", "=========name: "+c.getString(nameIndex)+" size: "+c.getString(sizeIndex));

                }
            }
        }
    }
    ```
