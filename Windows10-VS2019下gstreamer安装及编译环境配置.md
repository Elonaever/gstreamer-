gstreamer官方虽然提供了windows下安装的示例，但不知是否因为年代过于久远，按教程操作无法正确配置编译环境。  

通过查找相关资料，总结了Win10-VS2019环境下gstreamer相关的安装及开发环境配置方法：  

    操作系统： Windows10 ver.1909 64位操作系统  
    IDE:  Visual Studio 2019  
    gstreamer版本：1.19.1  

# 一、gstreamer安装过程：
1.  
    首先去https://gstreamer.freedesktop.org/data/pkg/windows 选择对应gstreamer版本的安装包，这里选择的是1.19.1/msvc/  
      下载gstreamer-1.0-devel-msvc-x86-1.19.1.msi以及gstreamer-1.g0-msvc-x86-1.19.1.msi两个安装包  
2.  关闭Visual Studio 2019及相关程序  
3.  安装gstreamer-1.0-msvc-x86-1.19.1.msi，安装过程中选择Complete  
        注意：默认安装目录会选择你剩余空间最大磁盘，这里我只有一个C盘，因此默认安装目录为C:\gstreamer，之后我写C:\gstreamer的地方可根据自己的实际安装目录进行更改  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-1.png)  
4.  安装gstreamer-1.0-devel-msvc-x86-1.19.1.msi，同样选择Complete  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-2.png)  
5.  打开系统环境变量，查看用户变量中是否存在GSTREAMER_1_0_ROOT_MSVC_X86_64，不存在则重新安装   
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-3.png)  
6.  编辑系统环境变量PATH，新建值C:\gstreamer\1.0\msvc_x86_64\bin  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-4.png)  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-5.png)  
7.  新建系统环境变量，变量名GST_PLUGIN_SYSTEM_PATH， 变量值为C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-6.png)  
8.  注销账户并重新登录(win10必要程序！！！)  
9.  打开CMD或PowerShell  输入命令：gst-launch-1.0.exe videotestsrc ! videoconvert ! autovideosink 出现画面，说明安装成功
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-7.png)  
    
# 二、gstreamer-tutorial Visual Studio 2019开发环境配置过程：  
1.  前往https://gitlab.freedesktop.org/gstreamer/gst-docs 下载tutorial代码文件压缩包，并解压至gstreamer同一目录(可选)
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-8.png)  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-9.png)   
2.  进入gst-docs-master\examples\tutorials\vs2010 目录， 打开toturails.sln项目文件，升级选择确认  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-10.png)   
3.  更改调试器为Debug x64模式
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-11.png)   
4.  打开视图-其他窗口-属性管理器选项
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-12.png)   
5.  右键basic-tutorial-1，选择添加新项目属性表， 目录改为C:\gstreamer\gst-docs-master\examples\tutorials\vs2010， 选择添加
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-13.png)   
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-14.png)    
6.  双击basic-tutorial-1/Debug|x64/PropertySheet文件，进入设置界面
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-15.png)   
7.  选择通用属性-C/C++-常规，右侧第一项 附加包含目录，添加C:\gstreamer\1.0\msvc_x86_64\lib\glib-2.0\include、
    C:\gstreamer\1.0\msvc_x86_64\include\gstreamer-1.0、C:\gstreamer\1.0\msvc_x86_64\include\glib-2.0以及C:\gstreamer\1.0\msvc_x86_64\include\glib-2.0\glib
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-16.png)   
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-17.png)    
8.  选择通用属性-链接器-常规，右侧第九项 附加库目录，添加C:\gstreamer\1.0\msvc_x86_64\lib
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-18.png)  
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-19.png)  
9.  选择通用属性-链接器-输入，右侧第九项 附加依赖项，添加gobject-2.0.lib;glib-2.0.lib;gstreamer-1.0.lib;
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-20.png)    
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-21.png)    
10.  点击应用-确定退出设置界面，右键PropertySheet文件，选择保存PropertySheet
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-22.png)    
    备注：之后，对于其他项目，可以直接右键项目名，选择添加现有属性表，找到保存的PropertySheet.props文件打开即可
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-23.png)    
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-24.png)    
11.  回到解决方案资源管理器界面，右键待编译运行的项目名称，选择属性，左侧配置属性-调试，右侧第三项 工作目录，更改为C:\gstreamer\1.0\msvc_x86_64\bin
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-25.png)  
12.  右键项目名-调试-启动新实例，成功编译并执行项目，恭喜，环境配置成功！
    ![image](https://github.com/Elonaever/gstreamer-/blob/main/pictures/install-26.png)
    
    
    
## 参考文章：
        https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c    
        https://homanhuang.medium.com/back-to-the-gstreamer-basic-1-c-vs2019-d516c35c9e2f#14cd   
