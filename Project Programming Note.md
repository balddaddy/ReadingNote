# Project Programming Note

## Eigen矩阵运算库

### VC++

**下载与安装**

> 1. 网址<http://eigen.tuxfamily.org>。下载解压后，将*<u>根目录\Eigen</u>文件夹* Copy到*<u>项目代码所在文件夹</u>*；或者将下载文件放置在<u>*C盘根目录*</u>，重命名后，在Project—>Properties—>Configuration Properties—>C/C++—>General—>Include Direction 添加*<u>Eigen根目录\Eigen</u>*
> 2. C++文件头添加：<code>#include \<Eigen/Dense\></code>；以及`using namespace Eigen`

用Eigen进行矩阵计算

> * 矩阵元素编号从<u>`0`</u>开始
> * MatrixXd mx_A(4,5)`产生double型任意大小矩阵；
> * `Matrix3d mx_1 = Matrix3d::Random()`产生`3*3 double`矩阵，并赋值为随机数；
> * `Matrix3cd mx_2`为`3*3 double`型复数矩阵; `mx_2.real()`实部矩阵；`mx_2.imag()`虚部矩阵；`mx_2(0,0) =complex<double>(1,2)`矩阵第一元素复制为`1+2j`；<u>注意</u>：用`complex`文件头要添加`#include <complex>`，以及`using namespace std`；
> * `mx_2.inverse()`矩阵求逆；`mx_2.transpose()`转置；`mx_2.adjoint()`共轭转置；`mx_2.conjuate()`共轭；

## DLL编写与调用

### VC++

#### DLL生成

新建项目

> 1. 选择*文件—>新建—>项目*，打开<u>新建项目</u>对话框，在左侧选择*Visual C++—>Win32—>Win32项目*，输入<u>项目名称</u>（比如<u>ExampFuncs</u>）等参数，点击**<u>确定</u>**；
> 2. 点击**<u>下一步</u>**；*应用程序类型*：Dll；*附加选项*：导出符号；点击<u>**完成**</u>

头文件

> 在<u>stdafx.h</u>中添加需要的头文件，常用的头文件包括：
>
> * 数学计算：`#include<math.h>`
> * 矩阵计算：`#include<Eigen/Dense>`

namespace

> * 选择<u>ExampFuncs.h</u>，将整个*结构体、变量、函数、类*等声明全都包在`namespace ExampFuncs{}`当中；
> * 选择ExampFuncs.cpp, 将整个*结构体、变量、函数、类*等实现都包在`namespace ExampFuncs{}`当中；

生成后事件

> 调用DLL时，必须把DLL文件与运行程序放在同一目录下，可以通过生成后事件命令行将生成的DLL文件拷贝过去。
>
> > * *项目—>属性—>配置属性—>生成事件—>生成后事件—>命令行*
> >
> >
> > * 假设调用DLL项目目录为<u>TestDll</u>，命令：`copy  $(TargetPath)  $(SolutionDir)..\TestDll\Debug`

#### DLL调用

生成项目后，设置项目依赖目录：

* 选择*项目—>属性—>配置属性—>C/C++—>常规*，在*附加包含目*录中，添加DLL工程源文件目录；
* 选择*项目—>属性—>配置属性—>连接器—>常规*，在*附加库目录*中，添加DLL工程Debug输出目录；
* 选择*项目—>属性—>配置属性—>连接器—>输入*，在*附加依赖项*中，添加DLL工程输出ExampFuncs.lib；
* 将<u>ExampFuncs.h</u>拷贝至调用DLL项目的源文件目录，并将该文件添加至项目中；

在测试函数cpp文件中：

* 添加头文件：`#include "ExampFuncs.h"`; `#include "windows.h"`
* 使用复数，添加头文件：`#include <complex>`
* 若调用 `cout << dA`来查看<u>dA</u>参数，添加头文件`#include <iostream>`

namespace

* `using namespace Eigen;`
* `using namespace std;`
* `using namespace ExampFuncs;`

## C++

### 函数参数的默认值

* Class中函数参数使用Class成员变量为默认值，应将该成员变量定义为静态成员变量。

> 例子：
>
> ```
> class CClassName {
> 	private:
> 		static type_def m_memberPara;
> 	public:
> 		void m_funFuncName(type_def para = m_memberPara);
> };
> ```

### 类成员变量的默认值

* 类定义中
* 类实现中

### CString to char[]

```
CString str = _T("hello");
char ch[20];
int n = str.GetLength();
memcpy(ch,str.GetBuffer(),str.GetLength()*2); // 这里str长度*2是因为copy时每个字母中间会插入0
```

> 或者

```
char *ch;
CString str=_T("hello");
ch=(LPSTR)(LPCTSTR)str;
```

### 全局变量

在XXX.cpp文件中定义全局变量，比如：

> `BOOL gbSimuOn = TRUE;``
>
> ``Class* gpClass = NULL;`

然后在public.h最后做出extern声明：

> `include"Class.h"`
>
> `extern BOOL gbSimuOn;`
>
> `extern Class* gpClass;`

### new, delete

> new一个指针就用delete，new一个指针数组，则要用delete []，例如：
>
> `int* pInt = new int;`
>
> `delete pInt;`
>
> `new* pInt = new int[20];`
>
> `delete []pInt;`

### 生成随机数

C++中生成随机数使用rand()函数，如果要产生0~1范围内的随机数可以使用以下代码：

```
#ifndef Rand01
#define Rand01 (rand()/double(RAND_MAX)) // 产生0-1随机数
#endif
```

但是为了增强随机数的随机性，需要喂不同的随机数种子，因此需要调用srand()：

### vector

vector类型的整体copy可以采用两种方式

1. `newVec.insert(newVec.end(),oldVec.begin(),oldVec.end());`
2. `newVec.assign(oldVec.begin(),oldVec.end())`;

## MFC

### 生成基于基于FormView的应用程序

* 在VC中，文件-->新建-->项目，选择<u>MFC应用程序</u>，输入项目名称后**<u>确定</u>**；

* <u>**下一步**</u>，选<u>单文档</u>，其他不变；

* 一直点**<u>下一步</u>**直到<u>**生成的类**</u>这一页，在<u>基类</u>下拉菜单中选择CFormView，**<u>完成</u>**

* 下拉列表

  > `ComboBox：CComboBox m_cbPRIType;`
  >
  > ` m_cbPRIType.SetCurSel(0); `
  >
  > `int nPRIType = m_cbPRIType.GetCurSel();`

* 通过CFormView类构造函数设定默认参数，并在数据交换函数中让界面显示默认值

  ```
  CGeneratePDWView::CGeneratePDWView()
  	: CFormView(CGeneratePDWView::IDD)
  	, m_nTargNum(8)
  	, m_dMainStationL(117.15)
  	, m_dMainStationB(25)
  {
  	// TODO: 在此处添加构造代码
  }
  void CGeneratePDWView::DoDataExchange(CDataExchange* pDX)
  {
  	CFormView::DoDataExchange(pDX);
  	DDX_Text(pDX, IDC_EDIT_TARGNUM, m_nTargNum);
  	DDX_Text(pDX, IDC_EDIT_MAINL, m_dMainStationL);
  	DDX_Text(pDX, IDC_EDIT_MAINB, m_dMainStationB);
  	DDX_Control(pDX, IDC_GNPROGRESS, m_ctlGenPDWProg);
  	DDX_Control(pDX, IDC_SDPROGRESS, m_ctlSndPDWProg);
  	DDX_Control(pDX, IDC_COMBO_PRITYPE, m_cbPRIType);
  }
  ```


### 增加控件



### SetTimer定时器

### 按钮事件

* 获取按钮的文本具体示例如下：

  > `//用于缓冲的临时CString`
  >
  > `CString tempstr`;
  >
  > `//获取ID_SimPause按钮的文本内容，其中ID_SimPause为按钮的ID`
  >
  > `GetDlgItem(ID_SimPause)->GetWindowText(tempstr);`

* 设置按钮的文本具体示例如下：

  > /`/ID_SimPause为按钮的ID,””内为按钮的文字内容`
  >
  > `GetDlgItem(ID_SimPause)->SetWindowText(_TEXT("结束"));`

### AfxMessageBox

1. MessageBox


1. > `MessageBox("这是一个最简单的消息框！");`
   >
   > `MessageBox("这是一个有标题的消息框！","标题");`
   >
   > `MessageBox("这是一个确定 取消的消息框！","标题", MB_OKCANCEL );`
   >
   > `MessageBox("这是一个警告的消息框！","标题", MB_ICONEXCLAMATION );`
   >
   > `MessageBox("这是一个两种属性的消息框！","标题", MB_ICONEXCLAMATION|MB_OKCANCEL );`
   >
   > `if(MessageBox("一种常用的应用","标题",MB_ICONEXCLAMATION|MB_OKCANCEL)==IDCANCEL)return;`

2. AfxMessageBox

   > `AfxMessageBox(“Are you sure?”，MB_YESNO|MB_ICONQUESTION）；`
   >
   > `int  a = MessageBox(TEXT( "是否确认删除？" ), TEXT("Warning!!"),4);`
   >
   > `if (a == 6)`
   >
   > `AfxMessageBox`("Yes");
   >
   > `else`
   >
   > `AfxMessageBox("No");`


### Public.h

```
#pragma once
#define  PI       3.1415926  //
#define  C_0      299792458  // 光速
#define  R_earth  6378137    //地球半径
#define  e2       0.00669437999013  // 曲率
#define  DEG      PI/180     // 弧度
enum PRITYPE{  //PRI调制类型定义
  PRI_Fixed = 1, // 固定
  PRI_Irregular  = 2, // 3参差
  PRI_Random = 3, // 抖动
  PRI_Slip = 4, // 滑变
};
struct ECEF_COORD { // 地固坐标系（包括速度）结构
  // 位置Position
  double dECEF_px;
  double dECEF_py;
  double dECEF_pz;
  // 速度Velocity
  double dECEF_vx;
  double dECEF_vy;
  double dECEF_vz;
};
// =========全局变量===========
static double gdSysTime = 0.0;  // 系统时间   
```

### IP地址控件

> IP地址在计算机中表示是32位二进制，通过GetAddress()来获得IP时，得到的是一个由二进制所转化的长整型IP地址，比如IP地址202.201.112.98的长整型表示就是：3402199138，同样也可以使用http://3402199138/来访问，所以在进行套接字绑定时要进行相应的转换。
>
> ```
> //将CString型IP地址在IPAddressCtrl中显示
> char cIP[30] = "192.168.0.10";
> DWORD dwIP = inet_addr(cIP);
> unsigned char *pIP = (unsigned char*)&dwIP;
> m_ipAddress.SetAddress(*pIP, *(pIP+1), *(pIP+2), *(pIP+3));
> //将IPAddressCtrl中的IP地址获得并转换成CString型
> unsigned   char   *pIP;
> CString   strIP;
> DWORD   dwIP;
> m_ipAddr.GetAddress(dwIP);
> pIP   =   (unsigned   char*)&dwIP;
> strIP.Format( "%u.%u.%u.%u ",*(pIP+3),   *(pIP+2),   *(pIP+1),   *pIP); //C语言实现代码
>
> ```

### 去除菜单、工具栏

* 去除菜单

> 在在`CMainFrame::PreCreateWindow(CREATESTRUCT& cs)`函数中加入一句：
>
> ​         `cs.hMenu = NULL;`

* 去除工具栏

> 在`CMainFrame::OnCreate(LPCREATESTRUCT lpCreateStruct)`中注释掉一些语句。
>
> `int CMainFrame::OnCreate(LPCREATESTRUCT lpCreateStruct)`
> `{`
>  `if (CFrameWnd::OnCreate(lpCreateStruct) == -1)`
>   `return -1;`
>  `//if (!m_wndToolBar.CreateEx(this, TBSTYLE_FLAT, WS_CHILD | WS_VISIBLE | CBRS_TOP`
>  `// | CBRS_GRIPPER | CBRS_TOOLTIPS | CBRS_FLYBY | CBRS_SIZE_DYNAMIC) ||`
>  `// !m_wndToolBar.LoadToolBar(IDR_MAINFRAME))`
>  `//{`
>  `// TRACE0("Failed to create toolbar\n");`
>  `// return -1;      // fail to create`
>  `//}`
>
>  `if (!m_wndStatusBar.Create(this) ||`
>   `!m_wndStatusBar.SetIndicators(indicators,`
> ​    `sizeof(indicators)/sizeof(UINT)))`
>  `{`
>   `TRACE0("Failed to create status bar\n");`
>   `return -1;      // fail to create`
>  `}`
>
>  `// TODO: Delete these three lines if you don't want the toolbar to be dockable`
>  `//m_wndToolBar.EnableDocking(CBRS_ALIGN_ANY);`
>  `//EnableDocking(CBRS_ALIGN_ANY);`
>  `//DockControlBar(&m_wndToolBar);`
>  `return 0;`
> `}`

### 调试

当程序调用DLL时，在数据监视窗口无法观察到数据的真实值，此时必须将数据输出到Output输出框：

```
#include <windows.h>
CString str;
str.Format(_T("%d %f %f\n"),nPDWNum, dTempPRF, m_dTimeStep);
OutputDebugString(str);
```



## QT

### 如何保持GUI响应流畅

通常耗时较长的操作分为计算密集型操作和IO密集型操作。而上述操作又分为可分解操作和不可分解操作；可分解操作又分为并行操作和串行操作。
提高响应的手段有如下几种：
1. 划分优先级：让较长的任务延后执行；
2. 划分时间段：让较长的任务暂定一段时间后进行；在程序中添加QCoreApplication::processEvents() 方法，让系统去处理消息队里中的事件；
3. 优化程序：减少每个任务的耗时。

最后，对于数据密集型操作，推荐使用ThreadPool来管理，减少线程上下文切换的时间；而对于IO密集型操作，则自己管理一个thread来实现，而这也是我认为thread最应该使用的情景，即让CPU和外设都处于满负荷运转状态，减少总的操作时间；对于并行操作响应时间的减少，在QT中引入了Qt Concurrent的概念，采用Map/Reduce的方式，具体可以参考QT中的Concurrent Programming节。



### QT常见的Debug输出手段

```
//qInfo is qt5.5+ only.
qInfo() << "C++ Style Info Message";
qInfo( "C Style Info Message" );

qDebug() << "C++ Style Debug Message";
qDebug( "C Style Debug Message" );

qWarning() << "C++ Style Warning Message";
qWarning( "C Style Warning Message" );

qCritical() << "C++ Style Critical Error Message";
qCritical( "C Style Critical Error Message" );

// qFatal does not have a C++ style method.
qFatal( "C Style Fatal Error Message" );
```



## 利用按钮调出新对话框

1. 建立UI：在项目中右键选择添加QT Designer Form Class，创建相应的界面和类，比如 CDlg；
2. 设计完成界面；
3. 在按钮响应函数中添加：`DrawWave drawWaveDlg;drawWaveDlg.exec();`
