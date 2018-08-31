
## Flash 使用

### 需要安装的软件
1. Adobe_FlashProfessional_CS6 (路径：install file/Adobe_FlashProfessional_CS6)
2. [脚本下载地址可以在官网搜索](https://github.com/Chensigou/pure2d)

### 批处理文件
1. Publish for LWF.jsfl

### 前置条件
1. 动画资源，设计给出fla文件
2. LWFS生成相关（必须放在桌面）
    
    - LWFS(路径：script file\LWF)，将该文件夹复制到桌面
    
    - 在桌面新建LWFS_work文件夹
    
    - LWFS_work_output(路径：script file\LWFS_work_output)，将该文件夹复制到桌面
    
    *注：最终生成的动画资源就在LWFS_work_output\native下*

### 生成动画资源 操作步骤
1. 使用 **adobe flash cs6** 打开动画资源(动画名称.fla)，打开后不要做任何操作
2. 双击 **Publish for LWF.jsfl** 脚本文件（如果提示Flash兼容性，点取消）
3. 处理结束帧，写入‘complete’事件，程序中可以监听到写入的事件作出对应处理，点击时间轴，找到动画运行的最后一帧，右击添加动作
    ```
    fscommand("event","complete")
    ```
4. 完成操作导出Swf文件，生成xxx.swf和xxx.bitmap文件夹（这里放的是最新的动画元素图片），点击文件->发布设置，做如下修改（必须）
    ```
    目标(T) : Flash Player 7
    脚本(S) : ActionScript 1.0
    
    点击确定，等待发布执行
    ```
5. 运行 **start.jse** 脚本，在桌面生成的lwf_work文件夹，将xxx.swf放入，再次 **start-remote.jse** 运行脚本，在弹出网页中确认结果为OK；
6. 打开lwf_work_output文件夹（这里放的是正确顺序的元素名和最终的动画控制文件xxx.lwf），按照图片样子依次xxx.bitmap文件夹中的图片名（相当辛苦的连连看游戏）；
7. xxx.bitmap文件夹中的图片 + lwf_work_output文件夹中的xxx.lwf文件就是完整的一个动画素材了；
8. 接下来就是加上xxxConfig.txt，用户存放动画的大小位置等配置，还有要重命名xxx.lwf为xxxANI.ani（防止被反编译）;

## ActionScript 交互设置

### 1. 设置点击监听
```
// 最上层场景，点击对应图片设置图层名（如："box"）
box.addEventListener(TouchEvent.TOUCH_TAP,OPENMOVIE);
function OPENMOVIE(e:TouchEvent) {
	// 注：此处有个问题，动画开始和结束的时候会默认执行一次，所以需要通过下面的暂停事件加一层判断
	fscommand("event","open_click");
	
	// 注：一般点击事件名： "anim_click"
}
```

### 2. 设置暂停事件
```
// 具体到动画场景，设置动画暂停
stop(); 

// 此处要和java层交互，通知动画已暂停，可以开始响应点击事件
fscommand("event","stop") 

// 注：一般点击事件名： "anim_click_start"
```

### 3. 设置帧名
```
// 在需要跳转的帧上设置名字，可以通过代码直接跳转

1. gotoAndPlay(80); // 同一场景内跳转第x帧
   gotoAndPlay("shake"); // 同一场景内跳转帧名

2. box.open.gotoAndPlay(80); // 不同场景内跳转第x帧
   box.open.gotoAndPlay("shake"); // 不同场景内跳转帧名

3. Lwf.gotoAndPlay("box", "shake"); // 代码中跳转：图片名 + 帧名
```

### 4. 设置结束事件
```
// 与java交互设置动画结束
fscommand("event","complete")
```

### 5. 小技巧

- 执行过 "Publish for LWF.jsfl"脚本，并设置好 Flash和 AS版本后，每次修改代码后可直接导出 .swf文件。


## 动画配置

动画资源分为三个部分：图片资源、动画lwf文件及配置文件，配置文件的内容如下（配置文件需要通过 [bejson](http://www.bejson.com/) 进行格式效验通过才能使用）：
    
```
{
    "displayFrame": {               //设置动画显示大小，height, width（只能配置其中一个）
        "height": {                 //动画大小占屏幕高度的比例
            "multiby": 0.9,
            "offset": 0
        },
        "width": {                  //动画大小占屏幕宽度的比例
            "multiby": 0.9,
            "offset": 0
        }
    },
    "edges": {                      //设置动画显示位置（centerX,centerY 只能存在一个）
        "centerX": {                //垂直居中
            "landMultiby": 0,       //距离左边的距离（横屏）
            "landOffset": 0,
            "portMultiby": 0.2,     //距离左边的距离（竖屏）
            "portOffset": 0
        },
        "centerY": {                //水平居中
            "landMultiby": 0,       //距离顶部的距离（横屏）
            "landOffset": 0,
            "portMultiby": 0.2,     //距离顶部的距离（竖屏）
            "portOffset": 0
        },
    },
    "orignFram": {
        "height": 100,
        "width": 100
    },
    "random": false,                //是否是随机动画
    "textFontSize": "28",           //送礼文字的大小
    "textImgName": "ship_14.png"    //空白图片（用来展示送礼信息文字），每个动画对应的空白图片不一样
}
```


