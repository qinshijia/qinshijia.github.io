---
title: Flutter之扫盲篇
date: 2018-08-06 16:12:39
toc: true
tags: 
	- Flutter
	- 学习
categories: 
	- Flutter
description: 
	Flutter是Google推出的跨平台SDK，同一份代码即可在Android和IOS系统上运行，使用Dart作为开发语言。2018年6月Flutter发布了首个预览版，到目前为止正式版本还未发布。Flutter是在GitHub上开源，SDK代码和相关支持库也在不断更新中，可以持续关注。
---
## 什么是Flutter

Flutter是Google推出的跨平台SDK，同一份代码即可在Android和IOS系统上运行，使用Dart作为开发语言。2018年6月Flutter发布了首个预览版，到目前为止正式版本还未发布。Flutter是在GitHub上开源，SDK代码和相关支持库也在不断更新中，可以持续关注。

官方简介：
>Flutter 是 Google 用以帮助开发者在 iOS 和 Android 两个平台开发高质量原生 UI 的移动 SDK。Flutter 兼容现有的代码，免费且开源，在全球开发者中广泛被使用。

Flutter拥有以下特点：
 
>-  **快速开发**：Flutter的热重载可以快速地进行测试、构建UI、添加功能并更快地修复错误。
>-  **富有表现力，漂亮的用户界面**：自带的Material Design和Cupertino（iOS风格）widget、丰富的motion API、平滑而自然的滑动效果。
>-  **响应式框架**：使用Flutter的现代、响应式框架，和一系列基础widget，轻松构建您的用户界面。
>-  **访问本地功能和SDK**：Flutter可以复用现有的Java、Swift或ObjC代码，访问iOS和Android上的原生系统功能和系统SDK。
>- **统一的应用开发体验**：Flutter拥有丰富的工具和库，可以帮助开发者轻松地同时在iOS和Android系统中实现想法和创意。
>-  **原生性能**：Flutter包含了许多核心的widget，如滚动、导航、图标和字体等，这些都可以在iOS和Android上达到原生应用一样的性能。

## Flutter和React Native有什么区别？

React Native (简称RN)是Facebook于2015年4月开源的跨平台移动应用开发框架，目前支持iOS和安卓两大平台，使用JavaScript语言开发。相较于Flutter，RN发展更早，使用的人数更多，第三方库也更加丰富。但是在使用RN时，需要编写一些桥接代码，不能完全实现跨平台。加上Facebook使用的开源许可证可能带来的法律风险，AirBnb、阿里巴巴和百度等大公司也相继宣布不再采用RN。

Flutter在跨平台方面做得会更加彻底，例如RN的UI组件最终都会转化为原生UI组件，而Flutter是自己实现，直接丢弃了原生UI组件，因此在各个平台上的表现一致性更高。Flutter在调用系统服务时，仍然需要根据平台配置，也不能完全做到平台无关。目前和RN相比较，Flutter并没有太大优势，不过Flutter还很年轻，Google与及广大开发者一起不断的维护和更新中。有消息称，Flutter作为Google开发的全新系统Fuchsia的开发框架，未来可期。

Flutter和RN的对比图：
![](https://www.mindinventory.com/blog/wp-content/uploads/2018/05/flutter-react-native-comparison.jpg)

## Flutter怎么用？

Flutter官网文档很全面，从入门到精通看 [这里](https://flutter.io/) 就够了。

### Flutter初体验
1. 安装环境

在Android Studio中安装插件`Flutter`和`Dart`，安装完成后重启Android Studio即可使用Flutter。
![](images/beb787b6-5aa1-4083-8da0-68fe95091b09.png)

2. 创建Flutter工程并运行

创建Flutter工程与创建Android工程方式一样，选择`File >New Flutter Project`，按照指引创建，运行则点击运行图标。
![](images/2ee80568-b9de-421e-a613-0c31c285d28d.png)

3. Flutter热重载

修改代码后保存即可在手机上看到修改后的效果。
![Flutter热重载](https://flutter.io/images/intellij/hot-reload.gif)

### Flutter组件简介

一切皆组件。

组件(Widget)是构建Flutter UI的基础。在Flutter中，组件是响应式的，描述了当前状态下该界面显示位置和内容，当组件的状态发生变化时会重建(rebuild)。组件分为有状态组件(StatefulWidget)和无状态组件(StatelessWidget)。有状态组件是指有可能会发生变化的组件，例如显示的内容、位置发生变化。无状态组件是指显示之后就不会发生变化。

Flutter 版Hello World：

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    new Center(
      child: new Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```
这是Flutter 应用最小的代码结构，`runApp()`是Flutter应用的入口，需要传入一个组件。`Center`和`Text`都是一个组件，描述了界面显示的位置和内容。

Flutter 版计数器：
```
import 'package:flutter/material.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Flutter Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

//有状态组件
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => new _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {

    //更新组件状态，刷新界面
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),

      //界面内容
      body: new Center(
        child: new Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            new Text(
              'You have pushed the button this many times:',
            ),

            //显示当前计数器的值
            new Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),

      //点击浮动按钮递增
      floatingActionButton: new FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: new Icon(Icons.add),
      ),
    );
  }
}

```
这段代码中的`MyHomePage`是有状态的组件，会动态刷新页面中间的数字。点击一次浮动按钮`floatingActionButton`时，会触发`onPressed`事件执行`_incrementCounter`方法，在`_incrementCounter`方法中，计数器加1，并调用有状态组件的`setState`方法，刷新组件显示的内容。

运行的截图如下：

![](images/6eded676-e66c-4d13-a8d6-a523915d257d.jpg)

### Flutter与Android原生开发的区别？

本节主要参考官方文档[Flutter for Android Developers](https://flutter.io/flutter-for-android/)

1.Flutter中有`View`吗？

在Android里，`View`是屏幕上显示的基础，Flutter与之类似的是组件`widget`。在Flutter中没有XML的布局文件，而是在代码中编写组件树`widget tree`来完成界面布局。当界面中显示的内容有变化时，通过更新有状态组件`StatefulWidget`的`State`来刷新界面。

2.Activity 和 Fragment 呢？

一切皆组件，Activity和Fragment在Flutter里面也属于组件`widget`的范畴。组件的生命周期里有初始化、状态改变、销毁这三种状态。
![](https://segmentfault.com/img/bVbbYQc?w=499&h=712)

在Flutter中弱化了生命周期的概念，[文档](https://flutter.io/flutter-for-android/#how-do-i-listen-to-android-activity-lifecycle-events)中写道：
> If you need to observe the lifecycle to acquire or release any native resources, you should likely be doing it from the native side, at any rate.

3.没有Activity，怎么跳转页面？

Flutter 没有 `Intent`,可以通过`Navigator`和`Route`来实现页面间的跳转。
```
void main() {
  runApp(new MaterialApp(
    home: new MyAppHome(), // becomes the route named '/'
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => new MyPage(title: 'page A'),
      '/b': (BuildContext context) => new MyPage(title: 'page B'),
      '/c': (BuildContext context) => new MyPage(title: 'page C'),
    },
  ));
}

//跳转页面
Navigator.of(context).pushNamed('/b');
```
4.怎么使用相机、GPS之类的功能？

使用Flutter插件(plugins)，也就是通过第三方库来使用这些功能。Flutter的第三方库都可以在[这里](https://pub.dartlang.org/packages)搜索。

5.怎么切换主线程和后台线程？

Flutter默认在主线程上运行，访问网络等需要异步线程的，则使用`async`和`await`,例如下面这个请求网络并刷新界面的例子：
```
loadData() async {
  String dataURL = "https://jsonplaceholder.typicode.com/posts";
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = json.decode(response.body);
  });
}
```


## 参考文档

[Flutter Vs. React Native: Let's See Who the Winner is](https://www.mindinventory.com/blog/flutter-vs-react-native/)

[Flutter框架研究和与RN对比](http://szuwest.github.io/flutterkuang-jia-yan-jiu-he-yu-rndui-bi.html)

[Flutter - 不一样的跨平台解决方案](https://juejin.im/post/5afd77466fb9a07aab2a12da)

[Flutter Technical Overview](https://flutter.io/technical-overview/#everythings-a-widget)

[flutter中的生命周期](https://segmentfault.com/a/1190000015211309)