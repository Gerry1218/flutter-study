## Flutter学习笔记

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
# cache.dart文件
```shell
# 生成交互代码
flutter pub run pigeon --input pigeons/cache.dart
```
