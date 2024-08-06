

# 控件

## Panel页面

```c#
panel1.Controls.Clear();
Form3 frm = new Form3()
{
    ShowInTaskbar = false,
    TopLevel = false,
    FormBorderStyle = FormBorderStyle.None,
    Dock = DockStyle.Fill,
};
panel1.Controls.Add(frm);
frm.Show();
```



## 自定义控件-弹出框

创建窗口文件MessageBoxForm

```c#
public partial class MessageBoxForm : Form
{
    public MessageBoxForm()
    {
        InitializeComponent();
        this.FormBorderStyle = FormBorderStyle.None;
    }

    public MessageBoxForm(string title, string message) : this()
    {
        lblTitle.Text = title;
        lblMessAge.Text = message;
    }

    private void btnOK_Click(object sender, EventArgs e)
    {
        this.DialogResult = DialogResult.OK;
    }
}
```

调用弹出框

```c#
using (MessageBoxForm frm = new Controls.MessageBoxForm("这是标题", "这是内容"))
{
    frm.StartPosition = FormStartPosition.CenterParent;
    var result = frm.ShowDialog(this); 
}
```







# 其他

## 检查安装字体

检查字体

```c#
        /// <summary>
        /// 检测某种字体样式是否可用
        /// </summary>
        /// <param name="familyName">字体名称</param>
        /// <param name="fontStyle">字体样式</param>
        /// <returns></returns>
        private bool CheckFont(string familyName, FontStyle fontStyle = FontStyle.Regular)
        {
            InstalledFontCollection installedFontCollection = new InstalledFontCollection();
            FontFamily[] fontFamilies = installedFontCollection.Families;
            foreach (var item in fontFamilies)
            {
                if (item.Name.Equals(familyName))
                {
                    return item.IsStyleAvailable(fontStyle);
                }
            }
            return false;
        }

```

安装字体

```c#
        /// <summary>
        /// 安装字体
        /// </summary>
        /// <param name="fontFilePath">字体文件全路径</param>
        /// <returns>是否成功安装字体</returns>
        /// <exception cref="UnauthorizedAccessException">不是管理员运行程序</exception>
        /// <exception cref="Exception">字体安装失败</exception>
        private bool InstallFont(string fontFilePath)
        {
            try
            {
                System.Security.Principal.WindowsIdentity identity = System.Security.Principal.WindowsIdentity.GetCurrent();
                System.Security.Principal.WindowsPrincipal principal = new System.Security.Principal.WindowsPrincipal(identity);
                //判断当前登录用户是否为管理员
                if (principal.IsInRole(System.Security.Principal.WindowsBuiltInRole.Administrator) == false)
                {
                    throw new UnauthorizedAccessException("当前用户无管理员权限，无法安装字体");
                }
                //获取Windows字体文件夹路径
                string fontPath = Path.Combine(System.Environment.GetEnvironmentVariable("WINDIR"), "fonts", Path.GetFileName(fontFilePath));
                //检测系统是否已安装该字体
                if (!File.Exists(fontPath))
                {                 
                    //将某路径下的字体拷贝到系统字体文件夹下
                    File.Copy(fontFilePath, fontPath); //font是程序目录下放字体的文件夹
                    AddFontResource(fontPath);
                    //安装字体
                    WriteProfileString("fonts", Path.GetFileNameWithoutExtension(fontFilePath) + "(TrueType)", Path.GetFileName(fontFilePath));
                }
            }
            catch (Exception ex)
            {
                return false;
            }
            return true;
        }

```

调用字体

```c#
           if (!CheckFont("7SEG"))
            {
                if (InstallFont(FontPath))
                {
                    MessageBox.Show("字体安装成功，重启生效！", "字体安装");
                }
                else
                {
                    MessageBox.Show("字体安装失败！", "字体安装");
                }
            }
```



## 程序单开

程序单开，只能同时启动一个

```c#
 [STAThread]
 static void Main(string[] args)
 { 
     //设置改程序只能启动一个
     bool hasCreated;
     Mutex mutex = new Mutex(true, "sov.cashier", out hasCreated);
     if (!hasCreated)
     {
         MessageBoxEx.ShowCustDialog(null, "对话框", "程序已经启动！");
         return;
     }
}
```



## 窗口位置





### 窗口全屏

**方式一**

```c#
this.FormBorderStyle = FormBorderStyle.None;     //设置窗体为无边框样式
this.WindowState = FormWindowState.Maximized;    //最大化窗体 
```

**方式二**

调用系统的API函数，如user32.dll中的FindWindow和ShowWindow函数，具体代码如下：

```c#
[DllImport("user32.dll", EntryPoint = "ShowWindow")]
public static extern Int32 ShowWindow(Int32 hwnd, Int32 nCmdShow);

[DllImport("user32.dll", EntryPoint = "FindWindow")]
private static extern Int32 FindWindow(string lpClassName, string lpWindowName);

[DllImport("user32.dll", EntryPoint = "SystemParametersInfo")]
private static extern Int32 SystemParametersInfo(Int32 uAction, Int32 uParam, ref Rectangle lpvParam, Int32 fuWinIni);


/// <summary>  
        /// 设置全屏或这取消全屏  
        /// </summary>  
        /// <param name="fullscreen">true:全屏 false:恢复</param>  
        /// <param name="rectOld">设置的时候，此参数返回原始尺寸，恢复时用此参数设置恢复</param>  
        /// <returns>设置结果</returns>  
        public Boolean SetFormFullScreen(Boolean fullscreen)//, ref Rectangle rectOld
        {
            Rectangle rectOld = Rectangle.Empty;
            Int32 hwnd = 0;
            hwnd = FindWindow("Shell_TrayWnd", null);//获取任务栏的句柄

            if (hwnd == 0) return false;

            if (fullscreen)//全屏
            {
                ShowWindow(hwnd, SW_HIDE);//隐藏任务栏

                SystemParametersInfo(SPI_GETWORKAREA, 0, ref rectOld, SPIF_UPDATEINIFILE);//get屏幕范围
                Rectangle rectFull = Screen.PrimaryScreen.Bounds;//全屏范围
                SystemParametersInfo(SPI_SETWORKAREA, 0, ref rectFull, SPIF_UPDATEINIFILE);//窗体全屏幕显示
            }
            else//还原 
            {
                ShowWindow(hwnd, SW_SHOW);//显示任务栏
                SystemParametersInfo(SPI_SETWORKAREA, 0, ref rectOld, SPIF_UPDATEINIFILE);//窗体还原
            }
            return true;
        }


/// <summary>
        ///  全屏按钮的Click事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button1_Click(object sender, EventArgs e)
        {
            m_IsFullScreen = !m_IsFullScreen;//点一次全屏，再点还原。  
            this.SuspendLayout();
            if (m_IsFullScreen)//全屏 ,按特定的顺序执行
            {
                SetFormFullScreen(m_IsFullScreen);
                this.FormBorderStyle = FormBorderStyle.None;
                this.WindowState = FormWindowState.Maximized;
                this.Activate();//
            }
            else//还原，按特定的顺序执行——窗体状态，窗体边框，设置任务栏和工作区域
            {
                this.WindowState = FormWindowState.Normal;
                this.FormBorderStyle = FormBorderStyle.Sizable;
                SetFormFullScreen(m_IsFullScreen);
                this.Activate();
            }
            this.ResumeLayout(false);
        }
```



### 多显示屏、多屏幕显示

```c#
// 方法一：
From2 frm2 = new From2();
if (Screen.AllScreens.Count() != 1)
{
  frm2.Left = Screen.AllScreens[0].Bounds.Width;
  frm2.Top = 0;
  frm2.Size = new System.Drawing.Size(Screen.AllScreens[1].Bounds.Width, Screen.AllScreens[1].Bounds.Height);
}
// 方法二：
this.Left = ((Screen.AllScreens[1].Bounds.Width - this.Width) / 2);
this.Top = ((Screen.AllScreens[1].Bounds.Height - this.Height) / 2);
this.Size = new System.Drawing.Size(Screen.AllScreens[1].Bounds.Width, Screen.AllScreens[1].Bounds.Height);
```





### 屏幕居中

在启动一个程序时，我们希望窗口显示的位置处于屏幕的正中心，可以如下设置

```c#
 MainForm mainForm = new MainForm();
 mainForm.StartPosition = FormStartPosition.CenterScreen;
 mainForm.Show();
```



### 手动屏幕居中

```c#
int ScreenH = System.Windows.Forms.SystemInformation.WorkingArea.Height;
int ScreenW = System.Windows.Forms.SystemInformation.WorkingArea.Width;
int formheight = this.Size.Height;
int formwidth = this.Size.Width;
int newformx = ScreenW / 2 - formwidth / 2;
int newformy = ScreenH / 2 - formheight / 2;
this.SetDesktopLocation(newformx, newformy);
```



### 主页登录

```c#
如果在允许操作主窗口之前，必须先登录，则弹出登录窗口。此时主窗口出现在登录窗口后面，无法进行操作。
 MainForm mainForm = new MainForm();
 LoginForm dlg=new LoginForm();
 dlg.ShowDialog();
这里ShowDialog方法表示你必须先操作完dlg窗口，才能操作后面的主窗体。
如果要登录窗口显示在主窗口的中心，则在显示之前设置如下
 dlg.StartPosition = FormStartPosition.CenterParent;
 dlg.ShowDialog();
能够这样做的前提是主窗体必须先定义和显示。否则登录窗体可能无法找到父窗体。
除此之外，也可以手动设置窗口显示的位置，即窗口坐标。
首先必须把窗体的显示位置设置为手动。
dlg.StartPosition=FormStartPosition.Manual;
随后获取屏幕的分辨率，也就是显示器屏幕的大小。
 int xWidth = SystemInformation.PrimaryMonitorSize.Width;//获取显示器屏幕宽度
 int yHeight = SystemInformation.PrimaryMonitorSize.Height;//高度
然后定义窗口位置，以主窗体为例
 mainForm.Location = new Point(xWidth/2, yHeight/2);//这里需要再减去窗体本身的宽度和高度的一半
 mainForm.Show();
```



### 淡入淡出窗口

```c#
public class FormFadeInorout
{

    public const Int32 AW_HOR_POSITIVE = 0x00000001; // 从左到右打开窗口
    public const Int32 AW_HOR_NEGATIVE = 0x00000002; // 从右到左打开窗口
    public const Int32 AW_VER_POSITIVE = 0x00000004; // 从上到下打开窗口
    public const Int32 AW_VER_NEGATIVE = 0x00000008; // 从下到上打开窗口
    public const Int32 AW_CENTER = 0x00000010; //若使用了AW_HIDE标志，则使窗口向内重叠；若未使用AW_HIDE标志，则使窗口向外扩展。
    public const Int32 AW_HIDE = 0x00010000; //隐藏窗口，缺省则显示窗口。
    public const Int32 AW_ACTIVATE = 0x00020000; //激活窗口。在使用了AW_HIDE标志后不要使用这个标志。
    public const Int32 AW_SLIDE = 0x00040000; //使用滑动类型。缺省则为滚动动画类型。当使用AW_CENTER标志时，这个标志就被忽略。
    public const Int32 AW_BLEND = 0x00080000; //使用淡出效果。只有当hWnd为顶层窗口的时候才可以使用此标志。
    [DllImport("user32.dll", CharSet = CharSet.Auto)]
    public static extern bool AnimateWindow
     (
        IntPtr hwnd, // 窗口句柄
        int dwTime, // 淡入淡出持续时间（单位：毫秒）
        int dwFlags // 淡入淡出特效类型
      );
}
```

**调用**

```
public Form2()
{
    FormFadeInorout.AnimateWindow(this.Handle, 500, FormFadeInorout.AW_BLEND);
}
```



### MDI窗体容器

窗体属性中有一个属性：IsMdiContainer - 确定该窗体是否是MDI容器

```c#
private void 销售ToolStripMenuItem_Click(object sender, EventArgs e)
{
            Form5 f5 = new Form5();
            //窗体最大化
            f5.WindowState = FormWindowState.Maximized;
            //去掉最大化最小化按钮
            f5.MaximizeBox = false;
            f5.MinimizeBox = false;
    		//去掉边框
            f5.FormBorderStyle = FormBorderStyle.None;
    		//设置新窗体的Parent
            //f5.Parent = panel1;
            f5.MdiParent = this;
    		f5.Show();
}
```



### 多线程中弹出窗口无效

```c#
public delegate DialogResult InvokeDelegate(Form parent);
public DialogResult xShowDialog(Form parent)
 {
     if (parent.InvokeRequired)
     {
         InvokeDelegate xShow = new InvokeDelegate(xShowDialog);
         parent.Invoke(xShow, new object[] { parent });
         return DialogResult;
     }
     return this.ShowDialog(parent);
 }
```

调用

```c#
Form2 form2 = new Form2();
form2.xShowDialog(this);
```



### 无边框窗口拖拽

在Panel事件处理

```c#
 private Point mPoint;
 private void panel1_MouseDown(object sender, MouseEventArgs e)
 {
     mPoint = new Point(e.X, e.Y);
 }

 private void panel1_MouseMove(object sender, MouseEventArgs e)
 {
     if (e.Button == MouseButtons.Left)
     {
         this.Location = new Point(this.Location.X + e.X - mPoint.X, this.Location.Y + e.Y - mPoint.Y);
     }
 }
```



## 全局异常监听

```c#
//处理未捕获的异常
Application.SetUnhandledExceptionMode(UnhandledExceptionMode.CatchException);
//处理UI线程异常
Application.ThreadException += new System.Threading.ThreadExceptionEventHandler(Application_ThreadException);
//处理非UI线程异常
AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(CurrentDomain_UnhandledException);

/// <summary>
/// 是否退出应用程序
/// </summary>
static bool glExitApp = false;

static void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e)
{
            LogHelper.Save("CurrentDomain_UnhandledException", LogType.Error);
            LogHelper.Save("IsTerminating : " + e.IsTerminating.ToString(), LogType.Error);
            LogHelper.Save(e.ExceptionObject.ToString());

            while (true)
            {//循环处理，否则应用程序将会退出
                if (glExitApp) {//标志应用程序可以退出，否则程序退出后，进程仍然在运行
                    LogHelper.Save("ExitApp");
                    return; 
                }
                System.Threading.Thread.Sleep(2*1000);
            };
}
        
static void Application_ThreadException(object sender, System.Threading.ThreadExceptionEventArgs e)
{
            LogHelper.Save("Application_ThreadException:" +
                e.Exception.Message, LogType.Error);
            LogHelper.Save(e.Exception);
            //throw new NotImplementedException();
}
```



## 软件开机自动启动

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;
using IWshRuntimeLibrary;  //添加引用，在 Com 中搜索 Windows Script Host Object Model
using System.Diagnostics;
/// <summary>
        /// 快捷方式名称-任意自定义
        /// </summary>
        private const string QuickName = "TCNVMClient";
 
        /// <summary>
        /// 自动获取系统自动启动目录
        /// </summary>
        private string systemStartPath { get { return Environment.GetFolderPath(Environment.SpecialFolder.Startup); } }
 
        /// <summary>
        /// 自动获取程序完整路径
        /// </summary>
        private string appAllPath { get { return Process.GetCurrentProcess().MainModule.FileName; } }
 
        /// <summary>
        /// 自动获取桌面目录
        /// </summary>
        private string desktopPath { get { return Environment.GetFolderPath(Environment.SpecialFolder.DesktopDirectory); } }
 
        /// <summary>
        /// 设置开机自动启动-只需要调用改方法就可以了参数里面的bool变量是控制开机启动的开关的，默认为开启自启启动
        /// </summary>
        /// <param name="onOff">自启开关</param>
        public void SetMeAutoStart(bool onOff = true)
        {
            if (onOff)//开机启动
            {
                //获取启动路径应用程序快捷方式的路径集合
                List<string> shortcutPaths = GetQuickFromFolder(systemStartPath, appAllPath);
                //存在2个以快捷方式则保留一个快捷方式-避免重复多于
                if (shortcutPaths.Count >= 2)
                {
                    for (int i = 1; i < shortcutPaths.Count; i++)
                    {
                        DeleteFile(shortcutPaths[i]);
                    }
                }
                else if (shortcutPaths.Count < 1)//不存在则创建快捷方式
                {
                    CreateShortcut(systemStartPath, QuickName, appAllPath, "中吉售货机");
                }
            }
            else//开机不启动
            {
                //获取启动路径应用程序快捷方式的路径集合
                List<string> shortcutPaths = GetQuickFromFolder(systemStartPath, appAllPath);
                //存在快捷方式则遍历全部删除
                if (shortcutPaths.Count > 0)
                {
                    for (int i = 0; i < shortcutPaths.Count; i++)
                    {
                        DeleteFile(shortcutPaths[i]);
                    }
                }
            }
            //创建桌面快捷方式-如果需要可以取消注释
            //CreateDesktopQuick(desktopPath, QuickName, appAllPath);
        }
 
        /// <summary>
        ///  向目标路径创建指定文件的快捷方式
        /// </summary>
        /// <param name="directory">目标目录</param>
        /// <param name="shortcutName">快捷方式名字</param>
        /// <param name="targetPath">文件完全路径</param>
        /// <param name="description">描述</param>
        /// <param name="iconLocation">图标地址</param>
        /// <returns>成功或失败</returns>
        private bool CreateShortcut(string directory, string shortcutName, string targetPath, string description = null, string iconLocation = null)
        {
            try
            {
                if (!Directory.Exists(directory)) Directory.CreateDirectory(directory);                         //目录不存在则创建
                //添加引用 Com 中搜索 Windows Script Host Object Model
                string shortcutPath = Path.Combine(directory, string.Format("{0}.lnk", shortcutName));          //合成路径
                WshShell shell = new IWshRuntimeLibrary.WshShell();
                IWshShortcut shortcut = (IWshRuntimeLibrary.IWshShortcut)shell.CreateShortcut(shortcutPath);    //创建快捷方式对象
                shortcut.TargetPath = targetPath;                                                               //指定目标路径
                shortcut.WorkingDirectory = Path.GetDirectoryName(targetPath);                                  //设置起始位置
                shortcut.WindowStyle = 1;                                                                       //设置运行方式，默认为常规窗口
                shortcut.Description = description;                                                             //设置备注
                shortcut.IconLocation = string.IsNullOrWhiteSpace(iconLocation) ? targetPath : iconLocation;    //设置图标路径
                shortcut.Save();                                                                                //保存快捷方式
                return true;
            }
            catch(Exception ex)
            {
                string temp = ex.Message;
                temp = "";
            }
            return false;
        }
 
        /// <summary>
        /// 获取指定文件夹下指定应用程序的快捷方式路径集合
        /// </summary>
        /// <param name="directory">文件夹</param>
        /// <param name="targetPath">目标应用程序路径</param>
        /// <returns>目标应用程序的快捷方式</returns>
        private List<string> GetQuickFromFolder(string directory, string targetPath)
        {
            List<string> tempStrs = new List<string>();
            tempStrs.Clear();
            string tempStr = null;
            string[] files = Directory.GetFiles(directory, "*.lnk");
            if (files == null || files.Length < 1)
            {
                return tempStrs;
            }
            for (int i = 0; i < files.Length; i++)
            {
                //files[i] = string.Format("{0}\\{1}", directory, files[i]);
                tempStr = GetAppPathFromQuick(files[i]);
                if (tempStr == targetPath)
                {
                    tempStrs.Add(files[i]);
                }
            }
            return tempStrs;
        }
 
        /// <summary>
        /// 获取快捷方式的目标文件路径-用于判断是否已经开启了自动启动
        /// </summary>
        /// <param name="shortcutPath"></param>
        /// <returns></returns>
        private string GetAppPathFromQuick(string shortcutPath)
        {
            //快捷方式文件的路径 = @"d:\Test.lnk";
            if (System.IO.File.Exists(shortcutPath))
            {
                WshShell shell = new WshShell();
                IWshShortcut shortct = (IWshShortcut)shell.CreateShortcut(shortcutPath);
                //快捷方式文件指向的路径.Text = 当前快捷方式文件IWshShortcut类.TargetPath;
                //快捷方式文件指向的目标目录.Text = 当前快捷方式文件IWshShortcut类.WorkingDirectory;
                return shortct.TargetPath;
            }
            else
            {
                return "";
            }
        }
 
        /// <summary>
        /// 根据路径删除文件-用于取消自启时从计算机自启目录删除程序的快捷方式
        /// </summary>
        /// <param name="path">路径</param>
        private void DeleteFile(string path)
        {
            FileAttributes attr = System.IO. File.GetAttributes(path);
            if (attr == FileAttributes.Directory)
            {
                Directory.Delete(path, true);
            }
            else
            {
                System.IO.File.Delete(path);
            }
        }
 
        /// <summary>
        /// 在桌面上创建快捷方式-如果需要可以调用
        /// </summary>
        /// <param name="desktopPath">桌面地址</param>
        /// <param name="appPath">应用路径</param>
        public void CreateDesktopQuick(string desktopPath = "", string quickName = "", string appPath = "")
        {
            List<string> shortcutPaths = GetQuickFromFolder(desktopPath, appPath);
            //如果没有则创建
            if (shortcutPaths.Count < 1)
            {
                CreateShortcut(desktopPath, quickName, appPath, "软件描述");
            }
        }
```



## 创建快捷方式

```c#
 var hasExistLink =System.IO. File.Exists(Path.Combine(Application.StartupPath, "Spov.Cash.lnk"));
 if (!hasExistLink)
 {
     WshShell shell = new WshShell();
     IWshShortcut shortcut = (IWshShortcut)shell.CreateShortcut(Path.Combine(Application.StartupPath,"Spov.Cash.lnk"));//创建快捷方式对象
     shortcut.TargetPath =Application.ExecutablePath;//指定目标路径
     shortcut.Arguments = "-setting";
     shortcut.WindowStyle = 1;//设置运行方式，默认为常规窗口
     shortcut.Description = "卡务机快捷键";//设置备注
     shortcut.Save();//保存快捷方式
 }
```













