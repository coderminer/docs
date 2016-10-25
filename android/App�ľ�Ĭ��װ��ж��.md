### App的静默安装和卸载

  Android系统本身提供了安装卸载功能，但是api接口是`@hide`的，不是公开的接口，所以在应用级别  
  是无法实现静默安装和卸载的，要实现静默安装和卸载需要是系统应用，要有系统签名和相应的权限  


  简单思路如下：  
  1. 通过反射获得安装接口`installPackage`和 卸载接口 `deletePackage`  
  2. 在自己的包中引入两个接口`IPackageInstallObserver`和`IPackageDeleteObserver`的空实现  
  3. 调用安装卸载的方法，回调上面的两个接口
  4. 添加权限 `<uses-permission android:name="android.permission.DELETE_PACKAGES"/>`
    `<uses-permission android:name="android.permission.INSTALL_PACKAGES"/>`
  5. 进行系统签名
  6. 将应用push到系统中，作为系统应用


#### 在`PackageManager`中的提供的接口如下  
  1. 安装接口

  ```
  // @SystemApi
  public abstract void installPackage(
        Uri packageURI, IPackageInstallObserver observer, int flags,
        String installerPackageName);
  ```

  2. 卸载接口

  ```
  // @SystemApi
  public abstract void deletePackage(
        String packageName, IPackageDeleteObserver observer, int flags);
  ```

#### 引入两个回掉的空实现  

  在自己应用的工程中新建一个包`android.content.pm`,并添加两个文件  
  * IPackageDeleteObserver.java  

  ```
  package android.content.pm;
  public interface IPackageDeleteObserver extends android.os.IInterface {
	public abstract static class Stub extends android.os.Binder implements android.content.pm.IPackageDeleteObserver {
		public Stub() {
			throw new RuntimeException("Stub!");
		}

		public static android.content.pm.IPackageDeleteObserver asInterface(android.os.IBinder obj) {
			throw new RuntimeException("Stub!");
		}

		public android.os.IBinder asBinder() {
			throw new RuntimeException("Stub!");
		}

		public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags)
				throws android.os.RemoteException {
			throw new RuntimeException("Stub!");
		}
	}

	public abstract void packageDeleted(java.lang.String packageName, int returnCode)
			throws android.os.RemoteException;
  }
  ```

  * IPackageInstallObserver.java  

  ```
  package android.content.pm;
  public interface IPackageInstallObserver extends android.os.IInterface {

	public abstract static class Stub extends android.os.Binder implements android.content.pm.IPackageInstallObserver {
		public Stub() {
			throw new RuntimeException("Stub!");
		}

		public static android.content.pm.IPackageInstallObserver asInterface(android.os.IBinder obj) {
			throw new RuntimeException("Stub!");
		}

		public android.os.IBinder asBinder() {
			throw new RuntimeException("Stub!");
		}

		public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags)
				throws android.os.RemoteException {
			throw new RuntimeException("Stub!");
		}
	}

	public abstract void packageInstalled(java.lang.String packageName, int returnCode)
			throws android.os.RemoteException;
  }
  ```

#### 自己定义个接口回调

  * OnPackagedObserver.java  

  ```
  package com.example;
  public interface OnPackagedObserver {

	public void packageInstalled(String packageName, int returnCode);
	public void packageDeleted(String packageName,int returnCode);
  }
  ```

#### 实现方法  

  * 反射接口

  ```
  PackageManager pm = context.getPackageManager();

  Class<?>[] types = new Class[] {Uri.class, IPackageInstallObserver.class, int.class, String.class};
  Class<?>[] uninstalltypes = new Class[] {String.class, IPackageDeleteObserver.class, int.class};

	Method method = pm.getClass().getMethod("installPackage", types);
	Method uninstallmethod = pm.getClass().getMethod("deletePackage", uninstalltypes);
  ```

  * 实现回调接口

  ```
  private OnPackagedObserver onInstalledPackaged;
  class PackageInstallObserver extends IPackageInstallObserver.Stub {

  public void packageInstalled(String packageName, int returnCode) throws RemoteException {
    if (onInstalledPackaged != null) {
      onInstalledPackaged.packageInstalled(packageName, returnCode);
    }
  }
  }

  class PackageDeleteObserver extends IPackageDeleteObserver.Stub {

  public void packageDeleted(String packageName, int returnCode) throws RemoteException {
    if (onInstalledPackaged != null) {
      onInstalledPackaged.packageDeleted(packageName, returnCode);
    }
  }
  }
  ```

  * 实现
  卸载接口只需要提供要卸载的应用的包名`packagename` ,安装提供了三个接口  

  ```
  public void uninstallPackage(String packagename) throws IllegalArgumentException, IllegalAccessException, InvocationTargetException {
      uninstallmethod.invoke(pm, new Object[] {packagename, observerdelete, 0});
  }
  public void installPackage(String apkFile) throws IllegalArgumentException, IllegalAccessException, InvocationTargetException {
    installPackage(new File(apkFile));
  }

  public void installPackage(File apkFile) throws IllegalArgumentException, IllegalAccessException, InvocationTargetException {
    if (!apkFile.exists()) throw new IllegalArgumentException();
    Uri packageURI = Uri.fromFile(apkFile);
    installPackage(packageURI);
  }

  public void installPackage(Uri apkFile) throws IllegalArgumentException, IllegalAccessException, InvocationTargetException {
    method.invoke(pm, new Object[] {apkFile, observer, INSTALL_REPLACE_EXISTING, null});
  }
  ```

#### 签名

  生成一个apk文件，需要对这个apk文件进行系统签名，由于`<uses-permission android:name="android.permission.DELETE_PACKAGES"/>`
    `<uses-permission android:name="android.permission.INSTALL_PACKAGES"/>` 是系统应用需要的权限，在开发应用时，如果加载AndroidManifest.xml  
    中会编译不过，需要先用工具 `apktool` 工具先把apk文件解压出来，用编辑器在AndroidManifest.xml中加入上面的两个权限，然后在用工具`apktool`重新打包

    具体的使用方法参考 [Android实践 -- Apktool 的使用](http://www.jianshu.com/p/1896307da564)

  * 解压  

  ```
  apktool d test.apk
  ```

  * 修改之后，重新打包  

  ```
  apktool b test
  ```

  签名之后的文件，需要在进行系统签名  
  具体的使用方法请参考 [Android实践 -- 对apk进行系统签名](http://www.jianshu.com/p/ab584f4ef629)

  ```
  java -jar signapk.jar platform.x509.pem platform.pk8 test.apk test_signed.apk
  ```

  将签名之后的文件 `push`到手机中，需要`root`权限  


#### 具体的代码实现  

  [源码](https://github.com/coderminer/Demo_Public)

#### 附录，安装卸载错误码速查

  回调中的`returnCode`在`PackageManager`中的相关的定义如下：  
  * 安装错误码  

  ```
  public static final int INSTALL_SUCCEEDED = 1;
  public static final int INSTALL_FAILED_ALREADY_EXISTS = -1;
  public static final int INSTALL_FAILED_INVALID_APK = -2;
  public static final int INSTALL_FAILED_INVALID_URI = -3;
  public static final int INSTALL_FAILED_INSUFFICIENT_STORAGE = -4;
  public static final int INSTALL_FAILED_DUPLICATE_PACKAGE = -5;
  public static final int INSTALL_FAILED_NO_SHARED_USER = -6;
  public static final int INSTALL_FAILED_UPDATE_INCOMPATIBLE = -7;
  public static final int INSTALL_FAILED_SHARED_USER_INCOMPATIBLE = -8;
  public static final int INSTALL_FAILED_MISSING_SHARED_LIBRARY = -9;
  public static final int INSTALL_FAILED_REPLACE_COULDNT_DELETE = -10;
  public static final int INSTALL_FAILED_DEXOPT = -11;
  public static final int INSTALL_FAILED_OLDER_SDK = -12;
  public static final int INSTALL_FAILED_CONFLICTING_PROVIDER = -13;
  public static final int INSTALL_FAILED_NEWER_SDK = -14;
  public static final int INSTALL_FAILED_TEST_ONLY = -15;
  public static final int INSTALL_FAILED_CPU_ABI_INCOMPATIBLE = -16;
  public static final int INSTALL_FAILED_MISSING_FEATURE = -17;
  public static final int INSTALL_FAILED_CONTAINER_ERROR = -18;
  public static final int INSTALL_FAILED_INVALID_INSTALL_LOCATION = -19;
  public static final int INSTALL_FAILED_MEDIA_UNAVAILABLE = -20;
  public static final int INSTALL_FAILED_VERIFICATION_TIMEOUT = -21;
  public static final int INSTALL_FAILED_VERIFICATION_FAILURE = -22;
  public static final int INSTALL_FAILED_PACKAGE_CHANGED = -23;
  public static final int INSTALL_FAILED_UID_CHANGED = -24;
  public static final int INSTALL_FAILED_VERSION_DOWNGRADE = -25;
  public static final int INSTALL_FAILED_PERMISSION_MODEL_DOWNGRADE = -26;
  public static final int INSTALL_PARSE_FAILED_NOT_APK = -100;
  public static final int INSTALL_PARSE_FAILED_BAD_MANIFEST = -101;
  public static final int INSTALL_PARSE_FAILED_UNEXPECTED_EXCEPTION = -102;
  public static final int INSTALL_PARSE_FAILED_NO_CERTIFICATES = -103;
  public static final int INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES = -104;
  public static final int INSTALL_PARSE_FAILED_CERTIFICATE_ENCODING = -105;
  public static final int INSTALL_PARSE_FAILED_BAD_PACKAGE_NAME = -106;
  public static final int INSTALL_PARSE_FAILED_BAD_SHARED_USER_ID = -107;
  public static final int INSTALL_PARSE_FAILED_MANIFEST_MALFORMED = -108;
  public static final int INSTALL_PARSE_FAILED_MANIFEST_EMPTY = -109;
  public static final int INSTALL_FAILED_INTERNAL_ERROR = -110;
  public static final int INSTALL_FAILED_USER_RESTRICTED = -111;
  public static final int INSTALL_FAILED_DUPLICATE_PERMISSION = -112;
  public static final int INSTALL_FAILED_NO_MATCHING_ABIS = -113;
  public static final int NO_NATIVE_LIBRARIES = -114;
  public static final int INSTALL_FAILED_ABORTED = -115;
  ```

  * 卸载错误码  

  ```
  public static final int DELETE_SUCCEEDED = 1;
  public static final int DELETE_FAILED_INTERNAL_ERROR = -1;
  public static final int DELETE_FAILED_DEVICE_POLICY_MANAGER = -2;
  public static final int DELETE_FAILED_USER_RESTRICTED = -3;
  public static final int DELETE_FAILED_OWNER_BLOCKED = -4;
  public static final int DELETE_FAILED_ABORTED = -5;
  ```
