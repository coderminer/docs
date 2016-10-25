### Android Support Libray 特性

  `Android Support Libray`是w为Android提供的兼容包，随着Android系统的发展，新的版本会  
  引入一些新的特性，而在低版本的Android系统中无法实现，兼容包就应运而生了，这些兼容包不在Android  
  的Framework中，而是单独独立的库，开发人员可以直接引入到自己的应用程序中，在开发过程中比较  
  常用的应该就是 `v4 support library` 和 `v7 appcompat library`,这两个包的应用比较广泛  
  对各个库做简单介绍，后续会针对每个可以写相应的实例  

### v4 Support Libray

  这个库中包含了大量的api，包括应用组件、UI特性、数据绑定、网络访问等  
  > 在Support library 版本 24.2.0 之前，这个库是一个单独的包，现在由于新的特性越来越多  
  > 这个包显的有些臃肿，为了减小apk的大小，这个包在24.2.0之后的版本中已经被拆分了几个包，
  > 根据自己的需求引入不同的包

#### v4 compat library

  封装了一些framework api，如 `Context.obtainDrawable()`和`View.performAccessibilityAction()`  
  在Android Studio的Gradle的配置中可以通过下面的方式引入这个库

  ```
  com.android.support:support-compat:24.2.1
  ```

#### v4 core-ui library

  主要是和UI相关的组件的封装 `ViewPager`、`NestedScrollView`和`ExploreByTouchHelper`  
  在Gradle中配置  

  ```
  com.android.support:support-core-ui:24.2.1
  ```

#### v4 media-compat library

  主要是封装media相关的兼容包，包含 `MediaBrowser`和`MediaSession`  
  在Gradle中配置  

  ```
  com.android.support:support-media-compat:24.2.1
  ```
#### v4 fragment library

  封装和fragment相关的接口，在Gradle中配置 这个包和 `compat` `core-ui` `core-ui`和  
  `media-compat`有依赖关系

  ```
  com.android.support:support-fragment:24.2.1
  ```

### Multidex Support Library

  这个库为在构建应用时对多个dex文件提供支持，应用程序的体量越来越大，dex文件如果超过64KB  
  就需要分为多个dex文件，gradle中的配置  

  ```
  com.android.support:multidex:1.0.0
  ```

### v7 Support Libraries

#### v7 appcompat library

  这个库中包括了 `Action Bar`,也支持`Material Design`的UI风格，这个库中的关键的类包括  
  * Action Bar
  * AppCompatActivity
  * AppCompatDialog
  * ShareActionProvider

  ```
  com.android.support:appcompat-v7:24.2.1
  ```

#### v7 cardview library

  这个库主要是对`CardView`控件的支持  

  ```
  com.android.support:cardview-v7:24.2.1
  ```

#### v7 gridlayout library

  支持 `GridLayout`  

  ```
  com.android.support:gridlayout-v7:24.2.1
  ```

#### v7 mediarouter library

  这个库主要提供 `MediaRouter` 、`MediaRouteProvider`以及和`Google Cast`相关的特性  

  ```
  com.android.support:mediarouter-v7:24.2.1
  ```

#### v7 palette library

  这个库主要是包含 `Palette`，可以从图像中直接提取颜色值，如：一个音乐应用可以使用`Palette`  
  从专辑封面提取颜色值  

  ```
  com.android.support:palette-v7:24.2.1
  ```

#### v7 recyclerview library

  主要提供了`RecyclerView`类的支持，比`ListView`提供了更大的便利  

  ```
  com.android.support:recyclerview-v7:24.2.1
  ```

#### v7 Preference Support Library

  提供和`Preference`相关的类的API，`CheckBoxPreference`和`ListPreference` `Preference.OnPreferenceChangeListener`  
  `Preference.OnPreferenceClickListener`  

  ```
  com.android.support:preference-v7:24.2.1
  ```

### v8 Support Library

#### v8 renderscript library

  主要提供和`RenderScript`计算相关的API  
  > 在Android Studio中支持`RenderScript`相关的兼容包，这个renderscript的库在 `build-tools/$VERSION/renderscript`文件夹中  

  ```
  defaultConfig {
    renderscriptTargetApi 18
    renderscriptSupportModeEnabled true
  }
  ```

### v13 Support Library

  设计是在Android3.2(API level 13)及以上版本中使用，主要包含`Fragment`和`FragmentCompat`相关的API  

  ```
  com.android.support:support-v13:24.2.1
  ```

### v14 Preference Support Library

  这个库主要支持 `PreferenceFragment.OnPreferenceStartFragmentCallback`和 `PreferenceFragment.OnPreferenceStartScreenCallback`  
  `MultiSelectListPreference`、`PreferenceFragment`等相关的API  

  ```
  com.android.support:preference-v14:24.2.1
  ```

### v17 Preference Support Library for TV

  这个库主要是`LeanbackListPreferenceDialogFragment.ViewHolder.OnItemClickListener` `BaseLeanbackPreferenceFragment`  
  `LeanbackPreferenceFragment`等相关的API，主要是TV设备  

  ```
  com.android.support:preference-leanback-v17:24.2.1
  ```

### v17 Leanback Library

  也主要是和TV设备相关的兼容包  
  * BrowserFragment
  * DetailsFragment
  * PlaybackOverlayFragment
  * SearchFragment

  ```
  com.android.support:leanback-v17:24.2.1
  ```

### Annotations Support Library

  ```
  com.android.support:support-annotations:24.2.1
  ```

### Design Support Library

  主要提供和设计相关的API，主要是material design相关的组件和特性，如 navigation drawers   
  和 floating buttons snackbars 等  

  ```
  com.android.support:design:24.2.1
  ```

### Custom Tabs Support Library

  主要可包含 `Custom Tabs Service` `Custom Tabs Callback`

  ```
  com.android.support:customtabs:24.2.1
  ```

### Percent Support Library

  `PercentLayoutHelper.PercentLayoutParams` `PercentFrameLayout` and `PercentRelativeLayout`  
  等相关的API  
  ```
  com.android.support:percent:24.2.1
  ```

### App Recommendation Support Library for TV

  加入了内容推荐相关的api，主要是TV设备  

  ```
  com.android.support:recommendation:24.2.1
  ```
