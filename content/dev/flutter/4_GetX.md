---
title: "GetX"
weight: 5
type: docs
---
GetX是在`Flutter`中创建全局变量/自定义路由/多语言支持等功能的一个依赖

## 安装

在项目的命令行中执行：

```bash
flutter pub add get
```

## 变量

### 创建变量

**建议在一个独立的`dart`文件中创建所有全局变量的类**

> [!warning]
> 注意这个类定义需要添加`extends GetxController`

```dart
import 'package:get/get.dart';
class Controller extends GetxController{
  // 注意在变量的最后添加".obs"用于实时检测变量变化
  RxString paraX="Hello world!".obs;
  RxList list=<String>[].obs;
}
```

### 在Widget中使用

> [!NOTE]
> 对于非`Map`或者`List`类型的GetX变量需要添加上`.value`  
> `Map`或者`List`不需要

#### 一般变量

```dart {hl_lines=[5, "11-15"]}
import 'package:get/get.dart';
import 'package:para.dart';
class MyApp extends StatefulWidget{
  // 获取变量
  final Controller c = Get.put(Controller());
  // 你可以给这个实例化的Controller添加一个Tag，有什么用后面有讲到
  // final Controller c=Get.put(Controller(), tag="initVal");
  @override
  Widget build(BuildContext context){
    return Center(
      child: Obx(()=>{
        Text(
          c.paraX.value
        )
      })
    )
  }
}
```

#### Map或者List变量

```dart
import 'package:get/get.dart';
// 别忘了导入全局变量的文件
import 'package:para.dart';
// 可以使用StatefulWidget
class MyApp extends StatefulWidget{
  // 获取变量
  final Controller c = Get.put(Controller());
  @override
  Widget build(BuildContext context){
    return ListView.builder(
      itemCount: c.list.length,
      itemBuilder: (BuildContext context, int index) => Text(c.list[index])
    )
  }
}
```

列表详细用法见[列表](/dev/flutter/23_列表)

> [!warning]
> 如果`Obx(()=>)`中不包含任何GetX变量会报错

### 更新变量

#### 一般变量赋值

```dart {hl_lines=[11]}
import 'package:get/get.dart';
import 'package:para.dart';
class MyApp extends StatefulWidget{
  final Controller c = Get.put(Controller());
  @override
  Widget build(BuildContext context){
    return Center(
      child: TextButton(
        onPressed: (){
          // 直接赋值更新变量
          c.paraX.value="Hello, Flutter!"
        },
        child: Text("按钮")
      )
    )
  }
}
```

#### Map或者List变量赋值

```dart
import 'package:get/get.dart';
import 'package:para.dart';
class MyApp extends StatefulWidget{
  final Controller c = Get.put(Controller());
  @override
  Widget build(BuildContext context){
    return Center(
      child: TextButton(
        onPressed: (){
          // 不需要使用.value
          c.list[1]="hello";
          c.list[2]="world!";
          // 需要在之后刷新这个变量
          c.list.refresh();
        },
        child: Text("按钮")
      )
    )
  }
}
```

### 监听变量

创建的变量如下：
```dart
import 'package:get/get.dart';
class Controller extends GetxController{
  var paraX="Hello world!".obs;
}
```

在StatefulWidget中监听变量

```dart {hl_lines=["10-12"]}
import 'package:get/get.dart';
import 'package:para.dart';
class MyApp extends StatefulWidget{
  final Controller c = Get.put(Controller());

  @override
  void initState() {
    super.initState();
    // ever([arg],(callback){})
    ever(c.paraX, (callback) {
      // 如果发生了更新，会执行这里的函数 //[!code ++]
    });
  }

  @override
  Widget build(BuildContext context){
    return //...
  }
}
```

> [!warning]
> 如果这个Widget可能会被销毁，务必在销毁的同时销毁监听

```dart {hl_lines=["12-14", "21"]}
import 'package:get/get.dart';
import 'package:para.dart';
class MyApp extends StatefulWidget{
  final Controller c = Get.put(Controller());

  // 注意变量类型
  late Worker listener;

  @override
  void initState() {
    super.initState();
    listener = ever(c.paraX, (callback) {
      // 监听函数
    });
  }

  @override
  void dispose() {
    super.dispose();
    // 注意销毁监听
    listener.dispose();
  }

  @override
  Widget build(BuildContext context){
    return //...
  }
}
```
## 跨Widget调用

在一个Widget中调用另外一个Widget方法

下面示例为`B Widget`调用`A Widget`的`printHello()`方法

```dart {hl_lines=[13, 24]}
// Controller
class Controller extends GetxController{
  var word='hello Flutter'.obs;
  
  void printHello(){
    print(word.value);
  }
}

// A Widget
class A extends StatefulWidget{
  // 如果只调用一次，可以忽略tag属性，这个属性用于防止调用多次导致无法区分
  final Controller c = Get.put(Controller(), tag="controller");
  return // ...其他内容
}

// B Widget
class B extends StatefulWidget{
  return FilledButton(
    onPressed: (){
      // 注意，可能会遇到找不到的情况（如果有），因此使用try-catch
      try{
        // 如果只调用一次，可以忽略tag属性，同上
        final Controller c = Get.find(tag="controller");
        c.printHello();
      }catch(_){}
    },
    child: Text(
      chid: 'printHello()'
    )
  );
}
```

## 跳转路由

GetX可以快捷地跳转到某个路由

你需要先修改`MaterialApp`为`GetMaterialApp`

```dart
return GetMaterialApp(
  home: // 页面内容
)
```

这样跳转路由

```dart
Get.to(()=>newPage());
```

### 混合路由

GetX可以实现页面中只有一部分内容受到路由的影响

```dart
return Navigator(
  // 注意添加一个key
  key: Get.nestedKey(1),
  initialRoute: '/home',
  onGenerateRoute: (settings) {
    switch (settings.name) {
      case "/home":
        return GetPageRoute(page: () => const HomeView());
      case "/settings":
        return GetPageRoute(page: () => const SettingsView());
      // ... 添加更多路由
    }
    return null;
  },
),
```

在跳转的时候注意携带id
```dart
Get.toNamed("/settings", id: 1);
```

## 多语言

> [!IMPORTANT]
> 你需要先前往[本地化](/dev/flutter/15_本地化/)文章中设置本地化

### 基本使用

首先需要创建语言的`Map`，例如这样
```dart
// lang/zh_cn.dart
const Map<String, String> zhCN = {
  'home': '主页',
  'settings': '设置'
  // 其它内容
};
```

```dart
// lang/en_us.dart
const Map<String, String> enUS = {
  'home': 'Home',
  'settings': 'Settings'
  // 其它内容
};
```

在`main.dart`中定义它

```dart
class MainTranslations extends Translations {
  @override
  Map<String, Map<String, String>> get keys => {
    'en_US': enUS,
    'zh_CN': zhCN,
  };
}
```

在`GetMaterialApp`中使用它

```dart
return GetMaterialApp(
  // 在这里调用
  translations: MainTranslations(),
  localizationsDelegates: const [
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate
  ],
  // 默认语言(这里使用的系统语言)
  locale: Get.deviceLocale
  supportedLocales: const [
    Locale('en', 'US'),
    Locale('zh', 'CN'),
  ],
  // 没有匹配支持语言的回退语言
  fallbackLocale: Locale('en', 'US'),
)
```

在Widget中使用翻译

```dart
return Text(
  'home'.tr
)
```

更新语言

```dart
var locale=Locale('en', 'US');
Get.updateLocale(locale);
```

### 如果需要添加切换语言的功能

```dart
// 自定义的语言类
class LanguageType{
  String name;    // 语言名称
  Locale locale;  // 语言

  LanguageType(this.name, this.locale);
}

// 语言列表
List<LanguageType> get supportedLocales => [
  LanguageType("English", Locale("en", "US")),
  LanguageType("简体中文", Locale("zh", "CN")),
  LanguageType("繁體中文", Locale("zh", "TW")),
];

class Controller extends GetxController{
  // 当前语言
  Rx<LanguageType> lang=Rx(supportedLocales[0]);

  late SharedPreferences prefs;

  // 初始化语言
  Future<void> init() async {
    prefs=await SharedPreferences.getInstance();

    int? langIndex=prefs.getInt("langIndex");

    if(langIndex==null){
      final deviceLocale=PlatformDispatcher.instance.locale;  // 获取设备语言
      final local=Locale(deviceLocale.languageCode, deviceLocale.countryCode);
      int index=supportedLocales.indexWhere((element) => element.locale==local);
      if(index!=-1){
        lang.value=supportedLocales[index];
        lang.refresh();
      }
    }else{
      lang.value=supportedLocales[langIndex];
    }
  }

  // 切换语言
  void changeLanguage(int index){
    lang.value=supportedLocales[index];
    prefs.setInt("langIndex", index);
    lang.refresh();
    Get.updateLocale(lang.value.locale);
  }
}

```

在主函数中初始化

```dart
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  final controller=Get.put(Controller());
  await controller.init();
  // ...其它
}
```

在`GetMaterialApp`中添加

```dart
return Obx(()=>
  GetMaterialApp(
    translations: MainTranslations(),
    locale: controller.lang.value.locale,
    debugShowCheckedModeBanner: false,
    localizationsDelegates: [
      GlobalMaterialLocalizations.delegate,
      GlobalWidgetsLocalizations.delegate,
      GlobalCupertinoLocalizations.delegate
    ],
    supportedLocales: supportedLocales.map((item)=>item.locale).toList(),
    fallbackLocale: Locale('en', 'US'),
    // 其它内容
  )
);
```

### 含有变量的语言翻译

使用`@`表示某个变量

```dart
// lang/zh_cn.dart
const Map<String, String> zhCN = {
  'totalPhotos': "共有 @count 张图片"
  // 其它内容
};
```

使用`.trParams`表示

```dart
return Text('totalPhotos'.trParams({'count': count }),)
```

## 全局Context

通过`Get.context`可以获取到根`context`

> [!NOTE]
> Get.context的类型是`BuildContext?`，如果要使用，必须满足根节点是`GetMaterialApp`  
> 如果满足根节点是`GetMaterialApp`，可以这样使用`Get.context!`得到根`context`

## 深色模式识别

GetX还可以判断当前是否处于深色模式

- `Get.isDarkMode`: Material App是否处于深色模式 (根节点需要是`GetMaterialApp`)
- `Get.isPlatformDarkMode`: 系统是否是否处于深色模式