---
title: Flutter之HTTP请求
date: 2018-08-06 16:12:39
toc: true
tags:
	- Flutter
	- 学习
categories:
	- Flutter
description:
	APP开发或多或少都要用到http网络请求，在Flutter中http请求的使用方式非常简单，只需在引入http库，设置url等必要参数后，调用`http.get()`或者`http.post()`方法即可。
---
APP开发或多或少都要用到http网络请求，在Flutter中http请求的使用方式非常简单，只需在引入http库，设置url等必要参数后，调用`http.get()`或者`http.post()`方法即可。
## Get方式请求
首先需要在 pubspec.yaml 中添加依赖：

```
dependencies:
  ...
  http: '>=0.11.3+16'
```
然后调用http.get()方法即可：
```
import 'package:http/http.dart' as http;
[...]
  loadData() async {
    String dataURL = "https://api.bmob.cn/1/classes/book/";
    http.Response response = await http.get(dataURL);
    print("response body : ${response.body}");
  }
}
```

设置http请求头 header：
```
  loadData() async {
    String dataUrl = "https://api.bmob.cn/1/classes/book/";
    Map<String, String> map = new Map();
    map["Content-Type"] = "application/json";
    map["X-Bmob-Application-Id"] = "ce30565639d0fb10cd925b5b024c3408";
    map["X-Bmob-REST-API-Key"] = "4057ff07b1bec5590fb4ff0a16141938";
    http.Response response = await http.get(dataUrl, headers: map);
    print("${response.body}");
  }
```

请求成功后刷新界面：
```
  loadData() async {
    String dataUrl = "https://api.bmob.cn/1/classes/book/";
    Map<String, String> map = new Map();
    map["Content-Type"] = "application/json";
    map["X-Bmob-Application-Id"] = "ce30565639d0fb10cd925b5b024c3408";
    map["X-Bmob-REST-API-Key"] = "4057ff07b1bec5590fb4ff0a16141938";
    http.Response response = await http.get(dataUrl, headers: map);
    print("${response.body}");

    //解析数据
    BookResponse baseResponse = new BookResponse(response.body);

    //通知界面更新状态，刷新界面
    setState(() {
        _books = baseResponse.data;
    });
  }
```

## Post方式请求
将get方式中的http.get() 方法改为 http.post() 方法，并添加参数body即可：
```
void _postData(Book book) async {
    String dataUrl = "https://api.bmob.cn/1/classes/book/";

    //设置请求头
    Map<String, String> map = new Map();
    map["Content-Type"] = "application/json";
    map["X-Bmob-Application-Id"] = "ce30565639d0fb10cd925b5b024c3408";
    map["X-Bmob-REST-API-Key"] = "4057ff07b1bec5590fb4ff0a16141938";
    try {

    //使用Post方式
      http.Response response = await http.post(dataUrl, headers: map, body: json.encode(book));
      print("${response.body}");
    } on Exception catch (e) {
      print("exception $e");
      SnackBar mSnackBar = new SnackBar(content: new Text("上传数据错误"));
      Scaffold.of(context).showSnackBar(mSnackBar);
   }
}
```

更多用法参考[官方示例](https://pub.dartlang.org/packages/http)
