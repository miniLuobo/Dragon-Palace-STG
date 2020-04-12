# STG小游戏开发日记-DXLib学习篇
目标：根据四圣龙神录作者Dixqさん公开的学习网站学习用VS编写一个完整的STG小游戏。      
现有的基础：基本的C语言知识。    
编程工具：Visual Studio2017、DXLib       
美术工具：Procreate(iPad)  
音乐特效：    
DXLib官网：https://dxlib.xsrv.jp/

## 第一天20/03/15
先学习DXLib相关知识：https://dixq.net/g/    
配置好VS2017环境，第一次正式使用VS2017，下载DXLib。    
DXLib是日本作者山田巧2001年开发的一个开源库，至今仍在更新，目前除了windows平台手机平台甚至支持PS4和Switch平台。        
DXLib函数是之后正式编写程序的重要基础！
> 问题1：VS控制台出现中文乱码？    
> 已解决：因为之前把控制面板里的区域语言改为了日文没改回来。（之后还需要解决多语言的问题）    

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
1.2章完    

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

LoadGraphScreen( ):    
读取图像并直接描绘到屏幕上。前两个XY位置参数起点是以屏幕左上角为原点（0,0）。  
> 问题2：修改成自己电脑本地的照片后运行程序图片没有显示？    
> 已解决：这里我犯了一个错误，放图片的路径不对，右下角调试窗口信息提示有两个图片文件夹，看来得补习一下VS2017的操作方法和目录结构。

## 第三天20/03/18
VS2017教程说新建项目时最好以空项目建立，于是重新新建了一个项目，把配置设置好后运行代码居然报错了。    
`error LNK2019: 无法解析的外部符号 _main，该符号在函数 "int __cdecl invoke_main(void)" (?invoke_main@@YAHXZ) 中被引用`
迷惑了半天找到解决办法：    
![image](https://github.com/miniLuobo/Dragon-Palace-STG/blob/master/diary_resources/%E5%B0%86%E6%8E%A7%E5%88%B6%E5%8F%B0%E6%94%B9%E4%B8%BA%E7%AA%97%E5%8F%A3.jpg)   

搞定这个之后，再测试图片显示也OK了。    
1.3章完

## 第四天20/03/20
1.4章 LoadGraphScreen( )不推荐使用，替代函数为LoadGraph( char \*FileName )。    
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

1.5章完

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

1.6章完

1.7章 使用里画面    
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
1.7、1.8章完

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

2.1、2.2章完

## 第九天20/03/27
2.3章 Alpha混合（透明处理）    
ps中见过alpha通道，这个通道用来储存透明信息，0代表透明，1代表不透明。    
使用函数SetDrawBlendMode( int BlendMode , int Pal ):    
第一个参数指定为DX_BLENDMODE_ALPHA，第二个参数范围为0（完全透明）~255（完全不透明）  

2.4章 叠加混合    
第一个参数指定为DX_BLENDMODE_ADD，第二个参数范围0（完全不叠加）~255（完全叠加）    
查找了一些图像发光原理，是将原图进行模糊处理后再叠加到原图上达成发光效果的。    

## 第十天20/03/29  
2.4章作者给的示例里，alpha混合取值是128，叠加混合取值是255，我还是不太明白两种处理有什么区别。    
设置相同的参数对比,左侧是alpha函数，右侧是叠加函数：    
![image](https://github.com/miniLuobo/Dragon-Palace-STG/blob/master/diary_resources/%E5%8F%96%E5%80%BC128.jpg)
![image](https://github.com/miniLuobo/Dragon-Palace-STG/blob/master/diary_resources/%E5%8F%96%E5%80%BC255.jpg)

叠加混合重合部分是融合在一起的。    
2.3、2.4章完    
 
2.5章 显示文字使用int DrawFormatString( int x , int y , int Color , char \*FormatString , ... )：    
x，y是文字起点坐标，color文字颜色，FormatString文字引用地址。    
其中颜色需要int GetColor( int Red , int Green , int Blue )来获取：    
三个参数为对应色的亮度（0~255）。    
```C
int x=0, y=0;
int Green = GetColor( 0, 255, 0 );      // 设定颜色变量

while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){
    DrawFormatString( x, y, Green, "座標[%d,%d]", x, y ); // “ ”内填写字符串
}
```
2.6章 取得随机数
GetRand( int RandMax ) 获得随机数。    
SRand函数取得随机数初始值，如果不设置DX会自动给出随机初始值。    
这里有个概念初始值指的是随机数的初始种子，种子是序列号，指定了种子也就指定了之后的随机数列。    
计算机并不存在真正的随机数。
```c
int Random[8]; //创建一个8位数组
int Green = GetColor( 0, 255, 0 );  // 设定颜色变量

SRand( 123456 ); // 设定随机数种子

Random[0] = GetRand( 100 ); // 生成0~100的随机数
Random[1] = GetRand( 100 );
Random[2] = GetRand( 100 );
Random[3] = GetRand( 100 );

SRand( 123456 ); // 再次设定随机数种子

Random[4] = GetRand( 100 ); // 生成0~100的随机数
Random[5] = GetRand( 100 );
Random[6] = GetRand( 100 );
Random[7] = GetRand( 100 );

while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){
    for( int i=0; i<8; i++ ){
        DrawFormatString( 0, 20*i, Green, "%d", Random[i] ); // for循环输出数组里的随机数
    }
}
```
2.5章、2.6章完    

## 第十一天20/03/30
2.7章 添加效果音    
声音函数LoadSoundMem（），游戏素材多以ogg格式居多，原因是MP3有专利权尽量回避。    
> 搜索后发现MP3的专利已经到期了    

奇怪的是复制教程代码到本地无法播放声音，路径也没错啊？    
提示`File Open Error`错误

## 第十二天20/03/31
不明白为什么会报错，突然想到把日文的路径名改为中文，声音就出来了……    
先默认VS只支持英文和计算机系统语言吧

## 第十三天20/04/01
啥也没干，换了系统语言又安装了VS日文包还是不支持日文路径。  
总算在一次运行中看到这个信息`warning C4566: ユニバーサル文字名 '\uD55C' によって表示されてい`    
肯定是我本地设置的编码有问题，待解决……
> 04/04补充：
> 2.7章的遇到的问题和一开始配置环境时设置的`多字节字符集`和`Unicode字符集`有关 
> 多字节字符集所用的编码为ANSI编码，中文操作系统使用GBK编码，日文操作系统是Shift_JIS编码。  
> 但是如果使用Unicode字符集，DxLib会报错，这是另一个需要解决的疑问。   
> 如果字符编码不解决，那就不能直接用DrawFormatString（）函数来做多国语言台词。    
> 最坏的情况是，台词系统变成图片，像Va-11一开始做的。

2.7章完

## 第十四天20/04/02
2.8章 分割画像读入 
看到这章让我想起一直以来的一个疑问：
>常常看到游戏素材是一张图片上密密麻麻堆满了各种小素材    
>这种素材是原画师自己拼接的吗，还是系统自动生成？

这样的图片可以用类似的TexturePacker图片打包工具完成。   
要读取这样的图片需要分割则使用LoadDivGraph（）函数
|函数声明|int LoadDivGraph( char \*FileName , int AllNum ,int XNum ,int YNum ,int XSize ,int YSize ,int \*HandleBuf ) ;|
|-----|------|
|参数|FileName：文件名|
| |AllNum：要分割图片的总数|
| |XNum ,YNum：图片的横向分割数和纵向分割数|
| |SizeX ,SizeY：一个分割图像的大小|
| |HandleBuf:　把分割好的图片保存到int型数组|
```C
int image[16]; //创建储存分割图像的数组
    LoadDivGraph( "图片/行走图.png" , 16 , 4 , 4 , 32 , 32 , image ); // 调用函数读取画像

    while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){
        DrawGraph( 0, 0, image[8], TRUE );      // 输出画像
}
```
2.8章完

## 第十五天20/04/04
2.9章 获取所有按键输入状态    
DXLib函数能获取输入按键的信息，但是没有能记录在哪个位置按键的函数。    
示例代码演示了如果不记录位置，显示内容会飞到屏幕外去。    
所以作者自创了一个函数来记录按键次数gpUpdateKey（），记录的数组大小一定要设为256！
```C
#include "DxLib.h"

int Key[256]; // 储存按键按下的帧率，フレーム数→フレームレート（Frame rate）：帧率FPS，Hz

// 更新按键的状态
int gpUpdateKey(){
    char tmpKey[256]; // 储存现在的按键状态
    GetHitKeyStateAll( tmpKey ); // 获取全部的按键状态
    for( int i=0; i<256; i++ ){ 
        if( tmpKey[i] != 0 ){ // 如果按下第i个键码对应的按键，キーコード（KeyCode）：键码
            Key[i]++;     // 自增
        }
        else{              // 如果没有按下
            Key[i] = 0;   // 设定为0
        }
    }
    return 0;
}

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
    ChangeWindowMode(TRUE), DxLib_Init(), SetDrawScreen( DX_SCREEN_BACK );

    while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 && gpUpdateKey()==0 ){

        if( Key[KEY_INPUT_Z] >= 60 ){ // 如果z按下帧率超过60
            DrawFormatString( 0, 0, GetColor(255,255,255), "?!" ); // 显示文字
        }
    }

    DxLib_End();
    return 0;
} 
```
这其实就是模拟出了持续按z连续射击的状态。    
2.9章完

## 第十六天20/04/05
3.1章 根据按键让角色移动    
承接上一章，这章作者划分了两个步骤，分别定义了两个函数，一是计算位移部分，二是描画图像部分。    
```C
int x=320, y=240;
int Handle; //全局变量设定main函数中要加载的图片初始坐标

void gpCalc(){ //定义计算位移函数
	if( Key[ KEY_INPUT_RIGHT ] >= 1 ){
		x++;
	}
	if( Key[ KEY_INPUT_DOWN  ] >= 1 ){
		y++;
	}
	if( Key[ KEY_INPUT_LEFT  ] >= 1 ){
		x--;
	}
	if( Key[ KEY_INPUT_UP    ] >= 1 ){
		y--;
	}
}

void gpDraw(){ //定义重绘图像函数
    DrawRotaGraph( x, y, 1.0, 0.0, Handle, TRUE );
}
```
定义好函数后，在main函数的while函数中添加这两个函数。    
这套代码执行后有个问题是没有边界判定，图片可以移动到窗口外去，之后肯定要修改的。    
3.1章完

## 第十七天20/04/06
3.2章 利用余数做循环    
循环体中经常用余数做循环，这章很好理解。    
3.3章 制作简单的选择画面    
構造体：structure 结构体，顺便打开《C Primer Plus》复习一下结构体知识……    
把开始菜单的选项写进结构体里，如果按下↓方向键则改变选项坐标。    
>问题4： VS的调试不能直接在窗口输入参数？？
```C
// 创建菜单构造体
typedef struct {
    int x, y;       // 菜单坐标
    char name[128]; // 菜单项目
} MenuElement_t;

int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {
    ChangeWindowMode(TRUE), DxLib_Init(), SetDrawScreen(DX_SCREEN_BACK);

    // 创建菜单内容
    MenuElement_t MenuElement[5] = {
        {  80, 100, "开始游戏" }, 
        { 100, 150, "彩蛋" },
        { 100, 200, "帮助" },
        { 100, 250, "设置" },
        { 100, 300, "结束游戏" },
    };
    int SelectNum = 0; // 创建选择序号变量
	
    while (ScreenFlip() == 0 && ProcessMessage() == 0 && ClearDrawScreen() == 0 && gpUpdateKey() == 0) {

        if (Key[KEY_INPUT_DOWN] == 1) { // 按下键盘的瞬间

        SelectNum = (SelectNum + 1) % 5; // 利用余数循环

        for (int i = 0; i < 5; i++) {             
            if (i == SelectNum) {      //选中的菜单
            MenuElement[i].x = 80; // 坐标向左平移，呈现选中的菜单会往前移动的效果
            }
            else {                       
                MenuElement[i].x = 100;// 其他没选中的菜单，x坐标恢复100
            }
        }
    }

    for (int i = 0; i < 5; i++) { // 绘制菜单
    DrawFormatString(MenuElement[i].x, MenuElement[i].y, GetColor(255, 255, 255), MenuElement[i].name);
    }
}
```
3.2、3.3章完

## 第十八天20/04/07
3.4章 制作简单的选择画面2（上下都能移动）    
前章作者介绍如何向下移动菜单，用的循环公式是`SelectNum = ( SelectNum + 1 ) % 5;`    
这样循环顺序会变成0→1→2→3→4→0→1→2→3→4→0，但是如果直接用`SelectNum = ( SelectNum - 1 ) % 5;`    
SelectNum值会变成负数，然后依旧是0→1→2→3→4→0→1→2→3→4→0的循环顺序。  
要将循环变为0→4→3→2→1→0→4→3→2→1→0，则`SelectNum = ( SelectNum + 4 ) % 5;`
在之前判断语句之下添加一段代码：
```C
if( Key[ KEY_INPUT_UP ] == 1 ){ // 如果按了↑键
    electNum = ( SelectNum + 4 ) % 5; 
}
```
+4实际就相当于再多按四次↓键，使之刚好达到原选项上方。    
3.4章完

## 第十九天20/04/08
我想在选项里添加一个彩蛋，结合之前的学习内容试一试。    
输入指定按键，开启特殊模式（待补充）

## 第二十天20/04/11
3.5章 用正弦函数使图像上下/大小光滑变化    
正弦函数一个周期横坐标为2π，纵坐标区间为[-1，1]    
如果一秒内使用0~60的自增变量，sin函数=sin( π\*2 / 60 \* Count )    
```C
#include <math.h>
#include "DxLib.h"

#define PI 3.141592654f  //C语言的单精度浮点数末尾记得要加f

int WINAPI WinMain(HINSTANCE,HINSTANCE,LPSTR,int){
ChangeWindowMode(TRUE), DxLib_Init(), SetDrawScreen( DX_SCREEN_BACK );

    int Handle;    
    int Count = 0;
    Handle = LoadGraph( "图片/角色.png" ); 
    while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){

// 复习DrawRotaGraph( x坐标, y坐标, 放大比例, 旋转角度, 图片 , 透明度 )
        DrawRotaGraph( 100, 240+sin(PI*2/240*Count)*200, 1.0, 0.0, Handle, TRUE ); //画像の描画
        DrawRotaGraph( 500, 240, 1.0+sin(PI*2/120*Count)*0.5, 0.0, Handle, TRUE ); //画像の描画
        Count++;

    }
        
    DxLib_End();
    return 0;
}  
```
π\*2 / 240 \* Count 因为一秒内循环60次，所以count的值可以达到60,4秒后达到240，则完成一个周期。    
所以sin公式=sin( π\* 2 / 周期 * Count ) * 振幅    
3.5章完

## 第二十一天20/04/12
3.6章 用正弦函数进行平滑移动    
正弦函数在[0 ~ π/2]区间的值为[0 ~ 1]，起始部分y轴上升快，接近1的时候平缓。        
就像拿手指弹一颗玻璃弹珠的时候，先是速度快最后慢慢停止运动。    
与之相对的，启动平缓结束也平缓的x轴区间是[2π - π/2　~　2π + π/2]，y轴区间为[-1，1]。    
但是因为有负数，y轴取值范围想要在[0，1]就要在原有区间[-1，1]上加上1再除以2。  
最后变成一条起始和结束都平缓的曲线。    
```C
while( ScreenFlip()==0 && ProcessMessage()==0 && ClearDrawScreen()==0 ){

    DrawRotaGraph( 420, 400-sin(PI/2/120*Count)*300, 1.0, 0.0, Handle, TRUE ); //未修改前的正弦函数

    DrawRotaGraph( 220, 400-(sin(-PI/2+PI/120*Count)+1)/2*300, 1.0, 0.0, Handle, TRUE ); //修改后的正弦函数

    if( Count < 120 ){
    Count++;
    }
    if( CheckHitKey( KEY_INPUT_Z ) == 1 ){
        Count = 0;
    }
}
```
第一个公式的取值范围在[0，π/2]之间，前后坡度不一。    
第二个公式的取值范围在[-π/2，π/2]之间，把sin的值+1再/2后就是平缓曲线了。    
作者建议要做出平稳移动效果的时候要多利用sin函数。    
3.6章完    

3.7章 


