### Android Wi-Fi 直连

  Wi-Fi直连是Android 4.0(API level 14)或更高的版本的才加入的新功能，使用Wi-Fi直连相关的  
  API可以发现并连接支持Wi-Fi直连的设备，连接之后设备之间可以通信，传输的距离比蓝牙的传输距离  
  要远很多  


#### API 概述

  * Wi-Fi直连的方法
  类`WifiP2pManager`提供了一些方法来使用Wi-Fi直连的相关的接口来发现连接设备  


  | 方法 | 描述 |
  | :------------- | :------------- |
  | initialize() | 在Wi-Fi框架中注册，必须在其他方法之前调用 |
  | connect() | 和另外的直连设备连接 |
  | cancelConnect() | 取消正在连接的动作 |
  | requestConnectInfo() | 请求已经连接的信息 |
  | createGroup() | 创建直连的设备组 |
  | removeGroup() | 删除当前的设置组 |
  | requestGroupInfo() | 请求当前组的信息 |
  | discoverPeers() | 初始化搜索 |
  | requestPeers() | 请求已经发现的设备的列表 |

  * Wi-Fi直连的监听
  类`WifiP2pManager`中也提供了很多的监听接口，计时的通知当前的 `activity`相关的搜索和连接的  
  结果  


  | 接口 | 相关的操作 |
  | :------------- | :------------- |
  | WifiP2pManager.ActionListener | 相关的操作：connect(), cancelConnect(), createGroup(), removeGroup(), and discoverPeers() |
  | WifiP2pManager.ChannelListener | 相关的操作：initialize() |
  | WifiP2pManager.ConnectionInfoListener | 相关的操作：requestConnectInfo() |
  | WifiP2pManager.GroupInfoListener | 相关的操作：requestGroupInfo() |
  | WifiP2pManager.PeerListListener | 相关的操作：requestPeers() |

  * Wi-Fi直连的`Intent`


  | Intent | 描述 |
  | :------------- | :------------- |
  | WIFI_P2P_CONNECTION_CHANGED_ACTION | 当设备的Wi-Fi的连接状态发生变化时触发 |
  | WIFI_P2P_PEERS_CHANGED_ACTION | 在调用`discoverPeers()`时触发，可以调用`requestPeers()`方法更新设备列表 |
  | WIFI_P2P_STATE_CHANGED_ACTION | Wi-Fi直连的状态发生变化时触发 |
  | WIFI_P2P_THIS_DEVICE_CHANGED_ACTION	| Wi-Fi直连的设备的详细信息发生变化时触发 |

#### 创建Wi-Fi直连的应用

  * 初始化设置

  首先要保证设置支持Wi-Fi直连相关的协议，如果支持，我们就可以获得`WifiP2pManager`的实例，创建并注册相关的广播，使用相关的  
  api  
  在`AndroidManifest`中必须声明相关的权限,Wi-Fi直连是在api level 14及更高的版本才能使用，还要声明`android:minSdkVersion="14"`  

  ```
  <uses-sdk android:minSdkVersion="14" />
  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
  <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
  <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  ```

  * 初始化`WifiP2pManager`的实例，并注册相关的广播，监听Wi-Fi直连的状态  

  ```
  private WifiP2pManager mManager;
  private Channel mChannel;
  private IntentFilter directFilter;
  private WiFiDirectReceiver directReceiver ;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);

      mManager = (WifiP2pManager)this.getSystemService(Context.WIFI_P2P_SERVICE);
      mChannel = mManager.initialize(this, this.getMainLooper(), null);
      directReceiver = new WiFiDirectReceiver(mManager, mChannel, this);

      directFilter = new IntentFilter();
      directFilter.addAction(WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION);
      directFilter.addAction(WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION);
      directFilter.addAction(WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION);
      directFilter.addAction(WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTION);

  }

  //注册广播监听
  @Override
  protected void onResume() {
      super.onResume();
      this.registerReceiver(directReceiver, directFilter);
  }

  //取消注册
  @Override
  protected void onPause() {
      super.onPause();
      this.unregisterReceiver(directReceiver);
  }
  ```


  广播接受  


  ```
  public class WiFiDirectReceiver extends BroadcastReceiver{

      private WifiP2pManager mManager;
      private Channel mChannel;
      private MainActivity mActivity;
      private PeerListListener mListener;
      private WifiP2pConfig mConfig = new WifiP2pConfig();

      public WiFiDirectReceiver(){}

      public WiFiDirectReceiver(WifiP2pManager manager,Channel channel,MainActivity activity){
          this.mManager = manager;
          this.mChannel = channel;
          this.mActivity = activity;

      }

      @Override
      public void onReceive(Context context, Intent intent) {
          String action = intent.getAction();
          Log.e("tag", "===============wifi direct action: "+action);
          if(action.equals(WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION)){
              int state = intent.getIntExtra(WifiP2pManager.EXTRA_WIFI_STATE, -1);
              if(state == WifiP2pManager.WIFI_P2P_STATE_ENABLED){
                  //打开
              }else if(state == WifiP2pManager.WIFI_P2P_STATE_DISABLED){
                  //关闭
              }
          }else if(action.equals(WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION)){

          }else if(action.equals(WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION)){

          }else if(action.equals(WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTION)){

          }
      }

  }
  ```

  * 发现设备

  在调用之后`initialize()`方法之后，会触发`WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION`的广播，在`BroadcastReceiver`  
  中，如果`WifiP2pManager.WIFI_P2P_STATE_ENABLED`的状态已经打开，在`BroadcastReceiver`调用`discoverPeers`方法,如果发现设备  
  会回调`onSuccess`方法  
  ```
  mManager.discoverPeers(mChannel, new WifiP2pManager.ActionListener() {
    @Override
                        public void onSuccess() {
                            Log.e("wuth", "===================discovery success");
                        }

                        @Override
                        public void onFailure(int reason) {
                            Log.e("wuth", "===================discovery failed");
                        }
  });
  ```
  如果发现设备，系统会触发`WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION`这个广播，在这个广播中可以调用`requestPeers`方法可以列出所有的  
  设备  

  ```
  if(null != mManager){
                mManager.requestPeers(mChannel, new WifiP2pManager.PeerListListener() {

                    @Override
                    public void onPeersAvailable(WifiP2pDeviceList peers) {
                        Log.e("tag", "==================peers list size: "+peers.getDeviceList().size());
                        for(WifiP2pDevice device: peers.getDeviceList()){
                            Log.e("tag", "==================device addr: "+device.deviceName+" name: "+device.deviceName);

                        }
                    }
                });
            }
  ```

  * 连接设备

  对于已经发现的设备我们可以调用`connect()`方法连接，需要初始化`WifiP2pConfig`,并设置config的`deviceAddress`  

  ```
  private WifiP2pConfig mConfig = new WifiP2pConfig();  
  mConfig.deviceAddress = device.deviceAddress;
                            mManager.connect(mChannel, mConfig, new WifiP2pManager.ActionListener() {
                                @Override
                                public void onSuccess() {
                                    Log.e("wuth", "==============connnect success");
                                }

                                @Override
                                public void onFailure(int reason) {
                                    Log.e("wuth", "=================connect failed");
                                }
                            });
  ```

  连接成功会回调`onSuccess`方法  

  传输数据待续。。。  
