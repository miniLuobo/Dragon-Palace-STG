# STG小游戏开发日记
目标：根据四圣龙神录作者公开的学习网站学习用VS编写一个完整的STG游戏。      
自己现有的基础：基本的C语言知识（可以做些OJ网站基础题的水平）、一点点Java知识，有面向对象的概念，但是这次开发用的是C语言。    
编程工具：Visual Studio2017、DXLib       
美术工具：Procreate(iPad)、PhotoShop(PC)    
音乐特效：
## 第一天
先学习DXLib相关知识：https://dixq.net/g/    
配置好VS2017环境，第一次正式使用VS2017，下载DXLib，DXLib是日本人开发的一个静态库。    
问题1：VS控制台中文乱码的问题    
已解决：错误是因为之前把控制面板里的区域语言改为了日文导致的    

教程1.2章首先要做出一个窗口    
示例程序：
```
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
        DxLib_Init();   // DXライブラリ初期化処理


        WaitKey();      // キー入力待ち
        DxLib_End();    // DXライブラリ終了処理
        return 0;
}
```
include 好理解，学C的时候都看到过头文件    
int WINAPI WinMain( ):    
C程序中的进入点是函数Main，Windows程序的进入点是WinMain，这个可以暂不深入    
DxLib_Init():  DxLib进行初始化处理    
WaitKey(): 等待输入    
DxLib_End(): 结束    

执行以上示例程序会得到一个全屏窗口，对程序进行下一步修改：   
```
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
        ChangeWindowMode(TRUE); // ウィンドウモードに設定
        DxLib_Init();   // DXライブラリ初期化処理


        WaitKey();      // キー入力待ち
        DxLib_End();    // DXライブラリ終了処理
        return 0;
}
```
ChangeWindowMode(TRUE): 当值为TRUE时变成窗口模式，FALSE时为全屏模式。    
1.2章结束    

## 第二天
1.3章 让程序窗口出现指定的图像     
```
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
        DxLib_Init();   // DXライブラリ初期化処理

        LoadGraphScreen( 50, 100, "画像/キャラクタ00.png", TRUE ); // 画像を描画する

        WaitKey();      // キー入力待ち
        DxLib_End();    // DXライブラリ終了処理
        return 0;
}
```

LoadGraphScreen():    
读取图像并直接描绘到屏幕上。    
这里我犯了一个错误放图片的路径不对，试着调试看看问题所在，右下角输出窗口信息提示有两个图片文件夹，看来得补习一下VS2017的操作方法和目录结构。

## 第三天
看了VS2017教程说新建项目时最好以空项目建立，于是重新新建了一个项目，把配置设置好后运行代码居然报错了。    
```
error LNK2019: 无法解析的外部符号 _main，该符号在函数 "int __cdecl invoke_main(void)" (?invoke_main@@YAHXZ) 中被引用
```
迷惑了半天找到解决办法：    
![image](https://github.com/miniLuobo/Dragon-Palace-STG/blob/master/diary_resources/%E5%B0%86%E6%8E%A7%E5%88%B6%E5%8F%B0%E6%94%B9%E4%B8%BA%E7%AA%97%E5%8F%A3.jpg)   

搞定这个之后，再测试图片显示也OK了，本章结束。

