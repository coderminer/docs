### Android Wi-Fi直连 传输数据

  一旦两个设备之间建立了Wi-Fi直连，那么这两个设备之间就可以通过`socket`传输数据大概得步骤如下：
  * 通过`ServerSocket`创建一个server端，在后台一直坚挺是否有client端连接
  * 通过`Socket`建立一个client端，根据server端的ip和port，连接到server端
  * 建立连接之后，通过client向server端发送数据
  * server端接到数据之后，可以对数据做对应的处理

#### `Client`端的核心逻辑

  * 在服务中创建一个`client`，并发送数据
    ```
      public class FileTransferService extends IntentService{
      ...
      @Override
      protected void onHandleIntent(Intent intent) {
          Context context = getApplicationContext();
          if(intent.getAction().equals(ACTION_SEND_FILE)){
              String fileUri = intent.getExtras().getString(EXTRAS_FILE_PATH);
              String host = intent.getExtras().getString(EXTRAS_GROUP_ADDRESS);
              Socket socket = new Socket();
              int port = intent.getExtras().getInt(EXTRAS_GROUP_PORT);
              try{
                  socket.bind(null);
                  //根据server端的地址和端口建立socket，并设置超时
                  socket.connect(new InetSocketAddress(host, port),SOCKET_TIMEOUT);
                  OutputStream os = socket.getOutputStream();
                  ContentResolver cr = context.getContentResolver();
                  InputStream is = null;

                  is = cr.openInputStream(Uri.parse(fileUri));
                  //发送数据
                  DeviceDetailFragment.copyFile(is, os);
              }catch(Exception ex){

              }finally{
                  if(socket != null){
                      if(socket.isConnected()){
                          try {
                              socket.close();
                          } catch (IOException e) {
                              e.printStackTrace();
                          }
                        }
                    }
                }
            }
        }

      }
    ```

  * 需要从相册中选择图片，并启动service

      ```
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("image/*");
        startActivityForResult(intent, CHOOSE_FILE_RESULT_CODE);
      ```
      在`onActivityResult`中获得选择的图像  
      ```
        Uri uri = data.getData();
        TextView statusText = (TextView)mContentView.findViewById(R.id.status_text);
        statusText.setText("Sending: "+uri);
        Intent i = new Intent(getActivity(), FileTransferService.class);
        i.setAction(FileTransferService.ACTION_SEND_FILE);
        i.putExtra(FileTransferService.EXTRAS_FILE_PATH, uri.toString());
        i.putExtra(FileTransferService.EXTRAS_GROUP_ADDRESS, info.groupOwnerAddress.getHostAddress());
        i.putExtra(FileTransferService.EXTRAS_GROUP_PORT, 8988);
        getActivity().startService(i);
      ```
#### `Server`端核心代码

  在设备连接之后，server就通过`ServerSocket`一直监听client端的连接，一旦有client的请求，将client发来的数据
  保存在本地,需要通过线程处理  

  ```
    public static class FileServerAsnycTask extends AsyncTask<Void,Void,String>{

      private Context context;
      private TextView statusText;

      public FileServerAsnycTask(Context context,View statusText){
          this.context  = context;
          this.statusText = (TextView)statusText;
      }
      @Override
      protected String doInBackground(Void... params) {
          try{
              //创建socket监听
              ServerSocket socket = new ServerSocket(8988);
              Socket client = socket.accept();
              //一旦有client端的连接，就接收client发来的数据并处理
              final File f = new File(Environment.getExternalStorageDirectory()+"/"+
              context.getPackageName()+"/wifishared-"+System.currentTimeMillis()+".jpg");
              File dirs = new File(f.getParent());
              if(!dirs.exists())
                  dirs.mkdirs();
              f.createNewFile();
              InputStream is = client.getInputStream();
              copyFile(is, new FileOutputStream(f));
              socket.close();
              return f.getAbsolutePath();
          }catch(Exception e){
              return null;
          }
      }  
      //接受完成之后，可以通过相册打开查看  
      @Override  
      protected void onPostExecute(String result) {
          if(result != null){
              statusText.setText("File copied - "+result);
              Intent i = new Intent();
              i.setAction(Intent.ACTION_VIEW);
              i.setDataAndType(Uri.parse("file//"+result), "image/*");
              context.startActivity(i);
          }
      }
    }
  ```

#### 完整的代码请查看
  测试可以在两个手机同时安装这个程序，一个手机是client端，一个手机是server端，连接之后，就可以相互传数据  
  [完整的代码](https://github.com/coderminer/Demo_Public/tree/master/Demo_wifi_direct)
