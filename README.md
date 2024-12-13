## Flutter

1. Apple芯片电脑，需安装Rosetta 2
```
sudo softwareupdate --install-rosetta --agree-to-license
```
*报错处理：*
```
Installing Rosetta 2 on this system is not supported.
```
***请进入mac系统的终端安装，不要用三方的终端***

- [Rosetta 2安装报错](https://machow2.com/rosetta-mac/)



### 问题
1. 编译报错
```shell
flutter build apk --release --dart-define-from-file=.env/prod.json

Font asset "MaterialIcons-Regular.otf" was tree-shaken, reducing it from 1645184 to 5180 bytes (99.7% reduction). Tree-shaking can be disabled by providing the --no-tree-shake-icons flag when building your app.
/Users/gerry/code/ronggao/shengyuapp/android/app/src/main/java/io/flutter/plugins/GeneratedPluginRegistrant.java:19: 错误: 程序包io.github.v7lin.y_kit不存在
      flutterEngine.getPlugins().add(new io.github.v7lin.alipay_kit.AlipayKitPlugin());
                                                                   ^
/Users/gerry/code/ronggao/shengyuapp/android/app/src/main/java/io/flutter/plugins/GeneratedPluginRegistrant.java:24: 错误: 程序包com.amap.fluttertion不存在
      flutterEngine.getPlugins().add(new com.amap.flutter.location.AMapFlutterLocationPlugin());
                                                                  ^
/Users/gerry/code/ronggao/shengyuapp/android/app/src/main/java/io/flutter/plugins/GeneratedPluginRegistrant.java:29: 错误: 程序包dev.fluttercommuplus.connectivity不存在
      flutterEngine.getPlugins().add(new dev.fluttercommunity.plus.connectivity.ConnectivityPlugin());
                                                                               ^
/Users/gerry/code/ronggao/shengyuapp/android/app/src/main/java/io/flutter/plugins/GeneratedPluginRegistrant.java:34: 错误: 程序包dev.fluttercommuplus.device_info不存在
      flutterEngine.getPlugins().add(new dev.fluttercommunity.plus.device_info.DeviceInfoPlusPlugin());
      ......

                                                                          ^
/Users/gerry/code/ronggao/shengyuapp/android/app/src/main/java/io/flutter/plugins/GeneratedPluginRegistrant.java:129: 错误: 程序包io.github.v7linat_kit不存在
      flutterEngine.getPlugins().add(new io.github.v7lin.wechat_kit.WechatKitPlugin());
                                                                   ^
23 个错误

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:compileReleaseJavaWithJavac'.
> Compilation failed; see the compiler error output for details.

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 48s
Running Gradle task 'assembleRelease'...                           48.6s
Gradle task assembleRelease failed with exit code 1
```
A: pubspec.yaml文件添加了plugin, 删掉即可
```yaml
flutter:
  plugin:
    platforms:
      ios:
        default_package: in_app_purchase_storekit
```

2. Android14的机型安装后进首页闪退
   A: 由于新增权限<uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PLAYBACK" />，新增服务类型microphone|mediaPlayback， 都去掉就行，由于首页初始化了前台服务
   ```xml
   <uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PLAYBACK" />

   ......

   <service
            android:name="com.pravera.flutter_foreground_task.service.ForegroundService"
            android:foregroundServiceType="dataSync|remoteMessaging|microphone|mediaPlayback"
            android:exported="false" />
   ```

3. Dio请求报错
```shell
dio Error: FormatException: Unexpected character (at offset 0)Error: FormatException: Unexpected character (at offset 0)
```
A: 接口返回数据不是json格式，但是通过postman看到的都是正常的，可以在post时的options参数里设置为Options(responseType: ResponseType.plain)查看返回结果

4. SmartDialog三方库问题, 不要设置SmartToastType.last, 多个请求报错会导致toast无法消失
  更换三方库： flutter_easyloading
  ```dart
    displayType:SmartToastType.last
  ```

5. `RotatedBox`用于旋转组件
6. 嵌套滑动页面组件`extended_tabs`
7. `animated_text_kit`打印机效果，水波等动效
8. 多线程和异步
9. `yield`异步生成器
10. `Completer`
11. `async*`
    ```dart
    /// 创建异步流
    Stream<int> testAsync() async* {
      for (int i = 0; i < 100; i++) {
        await Future.delayed(const Duration(seconds: 1));
        yield i;
      }
    }

    /// 读取异步流
    await for (var i in testAsync()) {
      debugPrint("Stream data: $i ");
    }
    ```
12. `yield*`生成器,在生成器函数（generator function）中将另一个生成器的值“转发”到当前的生成器
    
    ```dart
    /// 生成器函数
    Iterable<int> generator1() sync* {
      yield 1;
      yield* generator2(); // 转发 generator2 的值
      yield 4;
    }
  
    /// 生成器
    Iterable<int> generator2() sync* {
      yield 2;
      yield 3;
    }
    ```

13. 输入框长按显示英文 复制粘贴
    在MaterialApp下加入如下代码：
    ```dart
    locale: const Locale('zh', 'CN'),
    localizationsDelegates: const [
      GlobalMaterialLocalizations.delegate,
      GlobalWidgetsLocalizations.delegate,
      GlobalCupertinoLocalizations.delegate,
    ],
    supportedLocales: const [
      Locale('zh','CN'),
    ],
    ```
14. Isolate报错
    ```
    Bad state: The BackgroundIsolateBinaryMessenger.instance value is invalid until BackgroundIsolateBinaryMessenger.ensureInitialized is executed.
    ```

    
15. 加载本地assets图片慢导致白屏, initState里面调用，用来缓存图片
```dart
void _loadImage() async {
    final startTime = DateTime.now().millisecondsSinceEpoch;

    // 开始加载 AssetImage 图片
    final image = AssetImage('assets/room/room_default_bgImage.jpg');
    final imageStream = image.resolve(ImageConfiguration());

    // 监听图片加载完成事件
    imageStream.addListener(ImageStreamListener((info, _) {
      final endTime = DateTime.now().millisecondsSinceEpoch;
      setState(() {
        var loadingTime = 'Image loaded in: ${endTime - startTime} ms';
        debugPrint(loadingTime);
      });
    }));
  }
```
1.  [flutter怎么创建一个插件？](https://www.fullstackaction.com/pages/9a078b/)
```shell
# 创建项目
flutter create --template=plugin --platforms=android,ios -i swift -a kotlin video_cache
```
### cache.dart文件
```shell
# 生成交互代码
flutter pub run pigeon --input pigeons/cache.dart
```

### 开源项目
|  项目   | 介绍  | 演示 |
|  ----  | ----  | ----  |
| [loading_indicator](https://github.com/TinoGuo/loading_indicator)  | - | <img src="https://raw.githubusercontent.com/TinoGuo/loading_indicator/master/gif/demo_2021_07_18_02.gif" width=200 height=200/> |
| [flutter_easyloading](https://github.com/nslogx/flutter_easyloading)  | - | <img src="https://raw.githubusercontent.com/nslogx/flutter_easyloading/master/images/gif01.gif" width=200 height=429/> <img src="https://raw.githubusercontent.com/nslogx/flutter_easyloading/master/images/gif02.gif" width=200 height=429/> <img src="https://raw.githubusercontent.com/nslogx/flutter_easyloading/master/images/gif03.gif" width=200 height=429/> <img src="https://raw.githubusercontent.com/nslogx/flutter_easyloading/master/images/gif04.gif" width=200 height=429/> |
| [smart_select](https://github.com/davigmacode/flutter_smart_select)  | - |  <image alt="Single Choice Modal" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/single-modal.gif" width=200 height=429/><image alt="Multiple Choice Modal" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/multiple-modal.gif" width=200 height=429/>      <image alt="Single Choice Chips" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/single-chips.gif" width=200 height=429/><image alt="Multiple Choice Chips" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/multiple-chips.gif" width=200 height=429/><image alt="Multiple Choice Switch" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/multiple-switches.gif" width=200 height=429/><image alt="Customize Tile" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/custom-tile.gif" width=200 height=429/><image alt="Modal Filter" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/modal-filter.gif" width=200 height=429/><image alt="Modal Confirm" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/modal-confirm.gif" width=200 height=429/><image alt="Modal Validation" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/modal-validation.gif" width=200 height=429/><image alt="Modal Selector" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/modal-selector.gif" width=200 height=429/><image alt="Modal Shape" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/modal-shape.gif" width=200 height=429/><image alt="Choice Items" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/choice-item.gif" width=200 height=429/><image alt="Choice Grouped" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/choice-grouped.gif" width=200 height=429/><image alt="Choice Builder" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/screens/choice-builder.gif" width=200 height=429/> <a href="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/build/SmartSelect.apk"><image alt="Demo App" src="https://raw.githubusercontent.com/davigmacode/flutter_smart_select/master/demo/build/qr-apk.png" width=200 height=200/></a> |
| [fluttertoast](https://github.com/PonnamKarthik/FlutterToast)(3832⭐️) | - | <img src="https://raw.githubusercontent.com/ponnamkarthik/FlutterToast/master/screenshot/1.png" width=200 /> <img src="https://raw.githubusercontent.com/ponnamkarthik/FlutterToast/master/screenshot/2.png" width=200 /> <img src="https://raw.githubusercontent.com/ponnamkarthik/FlutterToast/master/screenshot/3.png" width=200/> <img src="https://raw.githubusercontent.com/ponnamkarthik/FlutterToast/master/screenshot/4.png" width=200/> <img src="https://raw.githubusercontent.com/ponnamkarthik/FlutterToast/master/screenshot/11.jpg" width=200/> |
| [draggable_scrollbar](https://github.com/fluttercommunity/flutter-draggable-scrollbar) | - | <img src="https://github.com/fluttercommunity/flutter-draggable-scrollbar/raw/gh-pages/demo.gif" width=200 /> |
| [smooth_page_indicator](https://github.com/Milad-Akarie/smooth_page_indicator) | - | <img src="https://raw.githubusercontent.com/Milad-Akarie/smooth_page_indicator/refs/heads/master/demo/four_squares_demo.gif" width=200 /> |
| [page_transition](https://github.com/kalismeras61/flutter_page_transition) | - | <img src="https://www.yasinilhan.com/page_transition/transition.gif" width=200  /> |
| [expandable_page_view](https://github.com/Limbou/expandable_page_view) | - | <img src="https://github.com/Limbou/expandable_page_view/blob/main/assets/appVideo.gif?raw=true" width=200/> <img src="https://github.com/Limbou/expandable_page_view/blob/main/assets/appVideo2.gif?raw=true" width=200 /> |
| [tab_container](https://github.com/sourcemain/tab_container) | - | <img src="https://camo.githubusercontent.com/d3492e810739b2c9d928a827c82d336e15ed4fe9f887f579c96730dc4fbc199e/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f63456b523139496c4a344d793232356f47672f67697068792e676966" width=200 /> <img src="https://camo.githubusercontent.com/75454f1cf208a4d2bc5b7fe5c55241c27c08cad330cac35142789e52a20a799f/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f3137646d6d64497a536179535a38665a57422f67697068792e676966"  width=200/> |
| [bubble_tab_indicator](https://github.com/vipulasri/flutter_bubble_tab_indicator) | - | <img src="https://github.com/vipulasri/flutter_bubble_tab_indicator/blob/master/images/banner.jpg"  width=200/> |
| [getx](https://github.com/jonataslaw/getx) | - | 啊 |
| [fade_scroll_app_bar](https://github.com/sametcilingir/fadescrollappbar) | - | <img src="https://camo.githubusercontent.com/7fee0fa08640e30508e5b6dded4d6d81db62157e4e8d38c3aa4363b449e93537/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f434f3342554d4935694d5a614a6f46777a4a2f67697068792e676966"  width=200/> <img src="https://camo.githubusercontent.com/6c3ccccaf5e12847dc51567c38053ac40a4e7ec303feee63aad92f33577b12b4/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f4a4733744361637a75736c6144524c6f59302f67697068792e676966"  width=200/> <img src="https://camo.githubusercontent.com/7f50ee52ad5984487f39ab9fe77ae63a28ef63d8e04a7d7d04750b4e212a47d2/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f7633515277597235344b5a313641393755712f67697068792e676966"  width=200/> |
| [extended_text_field](https://github.com/fluttercandies/extended_text_field) | - | <img src="https://github.com/fluttercandies/Flutter_Candies/raw/master/gif/extended_text_field/extended_text_field.gif"  width=200/> <img src="https://github.com/fluttercandies/Flutter_Candies/blob/master/gif/extended_text_field/widget_span.gif"  width=200/> <img src="https://github.com/fluttercandies/Flutter_Candies/blob/master/gif/extended_text_field/extended_text_field_image.gif"  width=200/> <img src="https://github.com/fluttercandies/Flutter_Candies/blob/master/gif/extended_text_field/custom_toolbar.gif"  width=200/> |
| flutter_spinkit | - | 啊 |
| toastification | - | 啊 |
| flutter_styled_toast | - | 啊 |
| oktoast(341⭐️) | 2024-11-27 | 啊 |
| motion_toast(438⭐️) | 2024-11-27 | 啊 |
| cherry_toast(228⭐️) | - | 啊 |
| delightful_toast(177⭐️) | - | 啊 |
| bot_toast(931⭐️) | - | 啊 |
| wolt_modal_sheet | - | 啊 |
| adaptive_dialog | - | 啊 |
| [flutter_chat_ui](https://github.com/flyerhq/flutter_chat_ui) | - | <img src="./images/flutter/1.jpg"> |
| [image](https://github.com/brendan-duncan/image) | 图片格式转换 | - |
| [giffy_dialog](https://github.com/xsahil03x/giffy_dialog) | - | 啊 |
| [syncfusion_flutter_datepicker](https://github.com/syncfusion/flutter-widgets/tree/master/packages/syncfusion_flutter_datepicker) | - | 啊 |
| [image](https://github.com/brendan-duncan/image) | - | Read/Write：JPG、PNG / Animated APNG、GIF / Animated GIF、BMP、TIFF、TGA、PVR、ICO<br> Read Only：WebP / Animated WebP、PSD、EXR、PNM (PBM, PGM, PPM)<br>Write Only：CUR |
| [pigeon](https://github.com/flutter/packages/tree/main/packages/pigeon) | - | 生成iOS和Android等跨平台代码 |
| [animated_text_kit](https://github.com/aagarwal1012/Animated-Text-Kit/) | - | <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/fade.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/typer.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/typewriter.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/scale.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/colorize.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/text_liquid_fill.gif?raw=true" align = "right" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/wavy.gif?raw=true" height = "300px"> <img src="https://github.com/aagarwal1012/Animated-Text-Kit/blob/master/display/flicker.gif?raw=true" height = "300px"> |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |
| 5 | - | 啊 |



### 参考资源
