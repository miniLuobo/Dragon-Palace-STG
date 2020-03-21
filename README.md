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
问题1：VS控制台中文乱码的问题    
已解决：错误是因为之前把控制面板里的区域语言改为了日文导致的    

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
问题2：修改成自己电脑本地的照片后运行程序图片没有显示。
已解决：这里我犯了一个错误，放图片的路径不对，右下角调试窗口信息提示有两个图片文件夹，看来得补习一下VS2017的操作方法和目录结构。

## 第三天20/03/18
VS2017教程说新建项目时最好以空项目建立，于是重新新建了一个项目，把配置设置好后运行代码居然报错了。    
```C
error LNK2019: 无法解析的外部符号 _main，该符号在函数 "int __cdecl invoke_main(void)" (?invoke_main@@YAHXZ) 中被引用
```
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
        if (CheckHitKey(KEY_INPUT_ESCAPE) == 1) { // 当返回值为1且按下的键是esc键时跳出循环
            break;
        }
    }

    DxLib_End();   // 结束DXLib
    return 0;
}
```
接下来作者提到有些情况下程序会死机，为了避免未响应的情况需要添加一个ProcessMessage()函数。    
这个函数是为了防止windows死机，经常需要用，日后可加深理解
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
            break;
        }
    }
        
        DxLib_End() ;   // 结束DXLib
        return 0 ;
} 
```
1.5章结束。
