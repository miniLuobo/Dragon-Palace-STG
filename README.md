# STG小游戏开发日记
目标：根据四圣龙神录作者公开的学习网站学习用VS编写一个完整的STG游戏。      
自己现有的基础：基本的C语言知识（可以做些OJ网站基础题的水平）、一点点Java知识，有面向对象的概念，但是这次开发用的是C语言。    
编程工具：Visual Studio2017、DXLib       
美术工具：Procreate(iPad)  
音乐特效：    
DXLib官网：https://dxlib.xsrv.jp/

## 第一天20/03/15
先学习DXLib相关知识：https://dixq.net/g/    
配置好VS2017环境，第一次正式使用VS2017，下载DXLib，DXLib是日本人开发的一个静态库。    
> 问题1：VS控制台中文乱码的问题    
> 已解决：错误是因为之前把控制面板里的区域语言改为了日文导致的    

DXLib教程1.2章 首先要做出一个窗口    
示例程序：
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    DxLib_Init();   // 初始化DXLib
    WaitKey();      // 等待键盘输入
    DxLib_End();    // 结束DXLib
    return 0;
}
```
int WINAPI WinMain( ):    
C程序中的进入点是函数Main，Windows程序的进入点是WinMain，这个可以暂不深入    
执行以上示例程序会得到一个全屏窗口，对程序进行下一步修改：   
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    ChangeWindowMode(TRUE); // 设定窗口模式
    DxLib_Init();   // 初始化DXLib
    WaitKey();      // 等待键盘输入
    DxLib_End();    // 结束DXLib
    return 0;
}
```
ChangeWindowMode(TRUE): 当值为TRUE时变成窗口模式，FALSE时为全屏模式。    
1.2章结束    

## 第二天20/03/17
1.3章 让程序窗口出现指定的图像     
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    DxLib_Init();   // 初始化DXLib
    LoadGraphScreen( 50, 100, "图片/vase.png", TRUE ); // 描绘图像
    WaitKey();      // 等待键盘输入
    DxLib_End();    // 结束DXLib
    return 0;
}
```

LoadGraphScreen():    
读取图像并直接描绘到屏幕上。前两个XY位置参数起点是以屏幕左上角为原点（0,0）。  
> 问题2：修改成自己电脑本地的照片后运行程序图片没有显示。
> 已解决：这里我犯了一个错误，放图片的路径不对，右下角调试窗口信息提示有两个图片文件夹，看来得补习一下VS2017的操作方法和目录结构。

## 第三天20/03/18
VS2017教程说新建项目时最好以空项目建立，于是重新新建了一个项目，把配置设置好后运行代码居然报错了。    
`error LNK2019: 无法解析的外部符号 _main，该符号在函数 "int __cdecl invoke_main(void)" (?invoke_main@@YAHXZ) 中被引用`
迷惑了半天找到解决办法：    
![image](https://github.com/miniLuobo/Dragon-Palace-STG/blob/master/diary_resources/%E5%B0%86%E6%8E%A7%E5%88%B6%E5%8F%B0%E6%94%B9%E4%B8%BA%E7%AA%97%E5%8F%A3.jpg)   

搞定这个之后，再测试图片显示也OK了，本章结束。

## 第四天20/03/20
1.4章 LoadGraphScreen()不推荐使用，替代函数为LoadGraph( char FileName )。    
因为从硬盘中读取数据非常低效，要采用将数据传到内存读取的办法。    
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){ 
    ChangeWindowMode( TRUE ); // 设定窗口模式
    DxLib_Init();   // 初始化DXLib

    int Handle;     // 设定变量
    Handle = LoadGraph( "图片/vase.png" ); // 加载画像
    DrawGraph( 50, 100, Handle, TRUE ); // 使用变量来描画图像

    WaitKey();     // 等待键盘输入
    DxLib_End();   // 结束DXLib
    return 0;
} 
```
（晚上太困了先睡觉……）

## 第五天20/03/21
1.5章 监听输入   
之前的程序按任意键就可以结束，这次改为只能按Esc键退出。    
|函数声明|int CheckHitKey( int KeyCode ) ;|
|-----|------|
|说明|取得特定键的输入状态|
|参数|KeyCode：获得输入状态的按键|
|返回值|1：按下按键|
| |0：没有按下|    

例：
```C
if( CheckHitKey( KEY_INPUT_ESCAPE ) == 0 )
    {
        // 没有按键
    }
    else
    {
        // 按下去了
    }
```
修改代码：
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {
    ChangeWindowMode(TRUE); // 设定窗口模式
    DxLib_Init();   // 初始化DXLib

    int Handle; // 设定变量
    Handle = LoadGraph("图片/vase.png"); // 加载画像
    DrawGraph(50, 100, Handle, TRUE); // 使用变量来描画图像
    
    while (1) {
        if (CheckHitKey(KEY_INPUT_ESCAPE) == 1) { 
            break; // 
        }
    }

    DxLib_End();   // 结束DXLib
    return 0;
}
```
接下来作者提到有些情况下程序会死机，为了避免未响应的情况需要添加一个ProcessMessage()函数。    
假如程序一直在执行一个耗时很长的循环，如果没有这个函数则用户做其他操作都不会得到回应，像是死机了。    
在循环里添加函数后，可以使得在循环中程序还能来处理用户别的操作。    
多线程知识，日后补充了解。    
修改为：
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    ChangeWindowMode( TRUE ); // 设定窗口模式
    DxLib_Init();   // 初始化DXLib

    int Handle; // 设定变量
    Handle = LoadGraph("图片/vase.png"); // 加载画像
    DrawGraph(50, 100, Handle, TRUE); // 使用变量来描画图像

    while( 1 ){
        if( ProcessMessage() != 0 ){
            break; //消息处理函数
        }
    }
        
        DxLib_End() ;   // 结束DXLib
        return 0 ;
} 
```
> ProcessMessage()返回值0成功，返回值-1错误或者窗口关闭
1.5章结束。

## 第六天20/03/22
第一个示例代码移动图像，上一次移动的画面会残留在屏幕上，变成一个长面条飞出屏幕外……
作者提到显示器表里两个概念，如果直接把图像写入帧缓冲器，那在刷新时间以外的时候会出现没有描画完成的图像。    
所以要在后台描画完成后再一口气传输到显示器上。
之前我看过一个java小游戏案例，其中为了解决图像闪烁问题使用了双缓冲技术。    
这个教程里的表里画面实际应该也是指双缓冲技术。
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {
    ChangeWindowMode(TRUE); // 设定窗口模式
    DxLib_Init(); // 初始化DXLib

    int x = 50;
    int Handle; // 设定变量
    Handle = LoadGraph("图片/vase.png"); // 加载画像

    while (1) {
        if (ProcessMessage() != 0) { 
            break; //消息处理函数
        }
        DrawGraph(x, 100, Handle, TRUE); //描画图像
        x = x + 2; // x以2递增
        Sleep(7); // 7[ms]待机
    }

    DxLib_End(); // 结束DXLib
    return 0;
}
```
> Sleep函数：可以使程序进入休眠，使其在一段时间内处于非活动状态。
> 当函数设定的计时器到期，或者接收到信号、程序发生中断都会导致程序继续执行。
为了让图片可以实现平移效果，添加ClearDrawScreen()函数。
> ClearDrawScreen()返回值0成功，返回值-1错误
1.6章结束

1.7章使用里画面    
1.6章没有处理后台画面，现在添加SetDrawScreen函数。    
把要描绘的对象作为里画面然后ScrenFrip函数将里画面变为表画面。    
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {
    ChangeWindowMode(TRUE); // 设定窗口模式
    DxLib_Init(); // 初始化DXLib
    SetDrawScreen(DX_SCREEN_BACK); //把描画目标设置为里画面

    int x = 0;
    int Handle;     // 设定变量
    Handle = LoadGraph("图片/vase.png"); // 加载画像

    while (1) {
        if (ProcessMessage() != 0) {
            break; //消息处理函数 
        }
        ClearDrawScreen(); // 清除画面
        DrawGraph(x, 100, Handle, TRUE); //描画图像
        x = x + 2; // x以2递增
        ScreenFlip(); // 显示里画面
    }

    DxLib_End(); // 结束DXLib
    return 0;
}
```
> 问题3：上个代码去除了sleep( )函数有何影响？
1.7章完
## 第七天20/03/23
1.7章改进了一下代码，缩短了行数。    
我倒是觉得一行一个函数比较清晰……    
```C
#include "DxLib.h"

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    ChangeWindowMode(TRUE), DxLib_Init(), SetDrawScreen( DX_SCREEN_BACK ); //设定窗口模式、初始化、设定里画面

    int x = 0;
    int Handle;     // 设定变量
    Handle = LoadGraph( "图片/vase.png" ); // 加载图像

    // while(把里画面反应到表画面, 消息处理函数, 清除画面)
    while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){
        DrawGraph( x, 100, Handle, TRUE ); //描画图像
        x = x + 2; // x以2递增
    }
        
    DxLib_End(); // 结束DXLib
    return 0;
} 
```
1.8章呈现多个图片不同速度的代码。    
1.7/1.8章完

## 第八天20/03/25
2.1章 演示了后描画的图像会覆盖在先描画的图像之上。    
2.2章 图像旋转·扩大缩小    
|函数声明|	int DrawRotaGraph( int x, int y, double ExtRate, double Angle, int GrHandle , int TransFlag ) ;|
|-----|------|
|参数|x,y：图像中心坐标，从图像的中心开始，DrawGraph是从左上角开始|
| |ExtRate：放大率（1.0的倍数）|
| |Angle：描画角度，采用弧度制|
| |GrHandle：加载图像的变量|
| |TransFlag：透明度flag，TRUE有效FALSE无效|


