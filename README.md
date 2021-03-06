# ijkplayer

[![pub package](https://img.shields.io/pub/v/flutter_ijkplayer.svg)](https://pub.dartlang.org/packages/flutter_ijkplayer)

ijkplayer,通过纹理的方式接入 [bilibili/ijkplayer](https://github.com/bilibili/ijkplayer)

使用前请完整阅读本 README 并参阅 example/lib/main.dart

有关 android 可能跑不起来的问题会详细解释

iOS 模拟器不显示图像,所以调试请使用真机(iOS10 iOS 12.1.4 亲测可用,其他版本有问题可反馈)
android 模拟器 mac android sdk 自带的 emulator(API28 android9)可用,其他类型的没有亲测不保证

- android: 我这里 sdk 自带的模拟器可用(音视频均正常)
- iOS: 库中包含了真机和模拟器的库文件,但是模拟器有声音,无图像

在正式使用前,可以先 star 一下仓库, download 代码跑一下 example 尝试 (clone 也可以)

## 目录

- [ijkplayer](#ijkplayer)
  - [目录](#%E7%9B%AE%E5%BD%95)
  - [English Readme](#english-readme)
  - [安装](#%E5%AE%89%E8%A3%85)
  - [原生部分说明](#%E5%8E%9F%E7%94%9F%E9%83%A8%E5%88%86%E8%AF%B4%E6%98%8E)
    - [iOS](#ios)
    - [Android](#android)
  - [入门示例](#%E5%85%A5%E9%97%A8%E7%A4%BA%E4%BE%8B)
  - [使用](#%E4%BD%BF%E7%94%A8)
    - [设置](#%E8%AE%BE%E7%BD%AE)
    - [关于销毁](#%E5%85%B3%E4%BA%8E%E9%94%80%E6%AF%81)
    - [控制器的使用](#%E6%8E%A7%E5%88%B6%E5%99%A8%E7%9A%84%E4%BD%BF%E7%94%A8)
      - [设置资源](#%E8%AE%BE%E7%BD%AE%E8%B5%84%E6%BA%90)
      - [播放器的控制](#%E6%92%AD%E6%94%BE%E5%99%A8%E7%9A%84%E6%8E%A7%E5%88%B6)
      - [获取播放信息](#%E8%8E%B7%E5%8F%96%E6%92%AD%E6%94%BE%E4%BF%A1%E6%81%AF)
      - [截取视频帧](#%E6%88%AA%E5%8F%96%E8%A7%86%E9%A2%91%E5%B8%A7)
      - [资源监听](#%E8%B5%84%E6%BA%90%E7%9B%91%E5%90%AC)
      - [IjkStatus 说明](#ijkstatus-%E8%AF%B4%E6%98%8E)
      - [自定义 Option](#%E8%87%AA%E5%AE%9A%E4%B9%89-option)
        - [IjkOptionCategory](#ijkoptioncategory)
      - [释放资源](#%E9%87%8A%E6%94%BE%E8%B5%84%E6%BA%90)
    - [自定义控制器 UI](#%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8E%A7%E5%88%B6%E5%99%A8-ui)
    - [自定义纹理界面](#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BA%B9%E7%90%86%E7%95%8C%E9%9D%A2)
    - [根据当前状态构建一个 widget](#%E6%A0%B9%E6%8D%AE%E5%BD%93%E5%89%8D%E7%8A%B6%E6%80%81%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA-widget)
  - [进度](#%E8%BF%9B%E5%BA%A6)
  - [LICENSE](#license)

## English Readme

https://github.com/CaiJingLong/flutter_ijkplayer/blob/master/README-EN.md

## 安装

[![pub package](https://img.shields.io/pub/v/flutter_ijkplayer.svg)](https://pub.dartlang.org/packages/flutter_ijkplayer)

最新版本请查看 pub

pubspec.yaml

```yaml
dependencies:
  flutter_ijkplayer: ${lastes_version}
```

## 原生部分说明

编译规则可以参考[这个](https://gitee.com/kikt/ijkplayer_thrid_party/blob/master/config/module.sh),如果你有自己的特定需求,可以修改编译选项,这个参考 [bilibili/ijkplayer](https://github.com/bilibili/ijkplayer) 或 [ffmpeg](http://ffmpeg.org/)

自定义编译选项可以看[这里](https://github.com/CaiJingLong/flutter_ijkplayer/blob/master/compile-cn.md)

### iOS

因为 iOS 部分代码的库文件比较大,所以创建了一个 pod 依赖托管 iOS 的 ijkplayer 库  
pod 库托管在 github 仓库内 https://github.com/CaiJingLong/flutter_ijkplayer_pod  
没有采用通用的 tar.gz 或 zip,而是使用 tar.xz 的方式压缩,这个压缩格式压缩率高,但是压缩和解压缩的的速度慢,综合考虑使用高压缩率的方式  
如果有朋友愿意提供 cdn 加速,可以联系我 😁

iOS 的代码来自于 https://github.com/jadennn/flutter_ijk 中的 iOS 代码
在这基础上增加了旋转通知

### Android

现在使用 [GSYVideoPlayer](https://github.com/CarGuo/GSYVideoPlayer)中的 ex-so 的规则, 但源码经过修改(截取视频帧),编译而成

## 入门示例

```dart
import 'package:flutter_ijkplayer/flutter_ijkplayer.dart';


class HomePageState extends State<HomePage> {
  IjkMediaController controller = IjkMediaController();

  @override
  void initState() {
    super.initState();
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Plugin example app'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.videocam),
            onPressed: _pickVideo,
          ),
        ],
      ),
      body: Container(
        // width: MediaQuery.of(context).size.width,
        // height: 400,
        child: ListView(
          children: <Widget>[
            buildIjkPlayer(),
          ]
        ),
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.play_arrow),
        onPressed: () async {
          await controller.setNetworkDataSource(
              'https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4',
              // 'rtmp://172.16.100.245/live1',
              // 'https://www.sample-videos.com/video123/flv/720/big_buck_bunny_720p_10mb.flv',
//              "https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4",
              // 'http://184.72.239.149/vod/smil:BigBuckBunny.smil/playlist.m3u8',
              // "file:///sdcard/Download/Sample1.mp4",
              autoPlay: true);
          print("set data source success");
          // controller.playOrPause();
        },
      ),
    );
  }

  Widget buildIjkPlayer() {
    return Container(
      // height: 400, // 这里随意
      child: IjkPlayer(
        mediaController: controller,
      ),
    );
  }
}
```

## 使用

### 设置

每个 ijkplayer 对应一个 IjkMediaController;

```dart
IjkMediaController controller = IjkMediaController();
```

将 controller 设置给 ijkplayer

```dart
  var ijkplayer = IjkPlayer(
    mediaController: controller,
  );
```

### 关于销毁

用户在确定不再使用 controller 时,必须自己调用 dispose 方法以释放资源,如果不调用,则会造成资源无法释放(后台有音乐等情况),一般情况下,在 ijkplayer 所属的页面销毁时同步销毁

因为一个`controller`可能被多个`IjkPlayer`附着, 导致一个`controller`同时控制多个`IjkPlayer`,所以原则上不能与`IjkPlayer`的`dispose`达成一致,所以这里需要调用者自行 dispose,

```dart
controller.dispose();
```

### 控制器的使用

#### 设置资源

```dart
// 根据你的资源类型设置,设置资源本身是耗时操作,建议await

// 网络
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4");

// 应用内资源
await controller.setAssetDataSource("assets/test.mp4");

// 文件
await controller.setFileDataSource(File("/sdcard/1.mp4"));

// 通过数据源的方式
var dataSource = DataSource.file(File("/sdcard/1.mp4"));
var dataSource = DataSource.network("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4");
var dataSource = DataSource.asset("assets/test.mp4");
await controller.setDataSource(dataSource);

// 还可以添加autoplay参数,这样会在资源准备完成后自动播放
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4",autoPlay : true);

//或者也可以在设置资源完毕后自己调用play
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4");
await controller.play();
```

#### 播放器的控制

```dart
// 播放或暂停
await controller.playOrPause();

// 不管当前状态,直接play
await controller.play();

// 不管当前状态,直接pause
await controller.pause();

// 停止
// 这里要说明,ijkplayer的stop会释放资源,导致play不能使用,需要重新准备资源,所以这里其实采用的是回到进度条开始,并暂停
await controller.stop();

// 进度跳转
await controller.seekTo(0.0); //这里是一个double值, 单位是秒, 如1秒100毫秒=1.1s 1分钟10秒=70.0

// 进度跳转百分比
await controller.seekToProgress(0.0); //0.0~1.0

// 暂停其他所有的播放器(适用于ListView滚出屏幕或界面上有多个播放器的情况)
await controller.pauseOtherController();

// 设置媒体音量,这个可以用于做视频静音而不影响系统音量
controller.volume = 100; //范围0~100

// 设置系统音量
await controller.setSystemVolume(100); // 范围0~100
```

#### 获取播放信息

```dart
  // 包含了一些信息,是否在播放,视频宽,高,视频角度,当前播放进度,总长度等信息
  VideoInfo info = await controller.getVideoInfo();
```

#### 截取视频帧

视频帧的截图

以`Uint8List`的格式导出,可以使用`Image`控件查看

```dart
var uint8List = await controller.screenShot();
var provider = MemoryImage(uint8List);
Widget image = Image(image:provider);
```

#### 资源监听

使用 stream 的形式向外广播一些信息的变化,原则上以 stream 结尾的属性都是可监听的

```dart
// 当纹理id发生变化时的回调,这个对于用户不敏感
Stream<int> textureIdStream = controller.textureIdStream;

// 播放状态的监听,true为正在播放,false为暂停
Stream<bool> playingStream = controller.playingStream;

// 当有调用controller.refreshVideoInfo()时,这个方法会回调,一般用于controllerUI的自定义,以便于监听当前信息(播放进度,播放状态,宽,高,方向变化等)
Stream<VideoInfo> videoInfoStream = controller.videoInfoStream;

// 音量的变化,这里需要注意,这个变化指的是当前媒体的音量变化,而不是系统的音量变化
Stream<bool> volumeStream = controller.playingStream;

// 当前Controller状态的监听,取值范围可以查看
Stream<IjkStatus> ijkStatusStream = controller.ijkStatusStream;
```

#### IjkStatus 说明

| 名称              | 说明                     |
| ----------------- | ------------------------ |
| noDatasource      | 初始状态/调用`reset()`后 |
| preparing         | 设置资源中               |
| setDatasourceFail | 设置资源失败             |
| prepared          | 准备好播放               |
| pause             | 暂停                     |
| error             | 发生错误                 |
| playing           | 播放中                   |
| complete          | 播放完毕后               |
| disposed          | 调用 dispose 后的状态    |

#### 自定义 Option

**本功能可能会出问题,导致不能播放等等情况,**如果发现设置选项后不能使用或出现异常,请停止使用此功能

支持自定义 IJKPlayer 的 option,这个 option 会直接传输至 android/iOS 原生,具体的数值和含义你需要查看[bilibili/ijkplayer](https://github.com/bilibili/ijkplayer)的设置选项

但这个设置后的选项不是即时生效的
只有在你重新 setDataSource 以后才会生效

设置方法`setIjkPlayerOptions`

```dart
void initIjkController() async {
  var option1 = IjkOption(IjkOptionCategory.format, "fflags", "fastseek");// category, key ,value

  controller.setIjkPlayerOptions(
    [TargetPlatform.iOS, TargetPlatform.android],
    [option1].toSet(),
  );

  await controller.setDataSource(
    DataSource.network(
        "http://img.ksbbs.com/asset/Mon_1703/05cacb4e02f9d9e.mp4"),
    autoPlay: true,
  );
}
```

第一个参数是一个数组,代表了你 option 目标设备的类型(android/iOS)

第二个参数是一个`Set<IjkOption>`,代表了 Option 的集合,因为 category 和 key 均相同的情况下会覆盖,所以这里使用了 set

##### IjkOptionCategory

| name   |
| ------ |
| format |
| codec  |
| sws    |
| player |

#### 释放资源

```dart
await controller.reset(); // 这个方法调用后,会释放所有原生资源,但重新设置dataSource依然可用

await controller.dispose(); //这个方法调用后,当前控制器理论上不再可用,重新设置dataSource无效,且可能会抛出异常,确定销毁这个controller时再调用
```

### 自定义控制器 UI

使用`IJKPlayer`的`controllerWidgetBuilder`属性可以自定义控制器的 UI,默认使用`defaultBuildIjkControllerWidget`方法构建

返回的 Widget 会被覆盖在 Texture 上

```dart
IJKPlayer(
  mediaController: IjkMediaController(),
  controllerWidgetBuilder: (mediaController){
    return Container(); // 自定义
  },
);
```

### 自定义纹理界面

使用`textureBuilder`属性自定义纹理界面,在 0.1.8 和之前的版本该属性名是`playerBuilder`

默认的方法`buildDefaultIjkPlayer`接受 `context,controller,videoInfo` 参数返回 Widget

```dart
IJKPlayer(
  mediaController: IjkMediaController(),
  textureBuilder: (context,mediaController,videoInfo){
    return Texture(); // 自定义纹理界面
  },
);
```

### 根据当前状态构建一个 widget

根据 Controller 当时 IjkStatus 的值构建 Widget,这个 Widget 会根据当前 status 变化而呈现出不同的界面

```dart

Widget buildIjkPlayer() {
  return IjkPlayer(
    mediaController: mediaController,
    stateWidgetBuilder: _buildStatusWidget,
  );
}

Widget _buildStatusWidget(
  BuildContext context,
  IjkMediaController controller,
  IjkStatus status,
) {
  if (status == IjkStatus.noDatasource) {
    return Center(
      child: Text(
        "no data",
        style: TextStyle(color: Colors.white),
      ),
    );
  }

  // you can custom your self status widget
  return IjkStatusWidget.buildStatusWidget(context, controller, status);
}
```

## 进度

目前正处于初始开发阶段,可能有各种问题,欢迎提出,但不一定会实现,也不一定会修改 😌

最初准备参考官方 video_player 的 api 方式进行开发,但是觉得调用的方式比较奇怪

需要自定义 LifeCycle 进行管理,而且自定义控制器不太方便,遂决定重写 api 的代码结构,同时清晰逻辑

目前属于公开测试使用阶段,不保证不出 bug,也不保证今后 api 不发生重大变更

目前的进度可以查看[TODOLIST](https://github.com/CaiJingLong/flutter_ijkplayer/blob/master/TODOLIST.md)

UI 控制功能包含常见的播放停止,手势拖动

## LICENSE

MIT
