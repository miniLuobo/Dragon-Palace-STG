# STG小游戏开发日记
根据龙神录作者公开的学习网站学习用VS编写一个完整的STG游戏   
自己的基础：基本的C语言知识（可以做些OJ网站基础题的水平）和一点点Java知识，有面向对象的概念，教程主要使用的还是C语言
## 第一天
配置好VS环境，第一次正式使用VS2017，下载DXLib   
新知识1：Lib 静态库，DXLib是日本制作同人游戏非常常用的一个库   
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
int WINAPI WinMain    
新知识2：C程序中的进入点是函数main，Windows程序的进入点是WinMain，这个可以暂不深入    
DxLib_Init()  DxLib进行初始化处理    
WaitKey() 等待输入    
DxLib_End() 结束    

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
ChangeWindowMode(TRUE) 当值为TRUE时变成窗口模式，FALSE时为全屏模式   
1.2章结束    



