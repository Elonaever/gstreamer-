# 目标：
  掌握一个软件库最好的方式就是在屏幕上打印"Hello World"。 但是对于多媒体框架而言，最好的选择是播放一个视频。  
  不必为下方代码的行数担忧，真正工作的代码实际上只有4行，其余的都是C语言中不得不做的资源管理代码。  
  事不宜迟，让我们开始第一个GStreamer应用。  
  
#  Hello World:  

  复制下方名为basic-tutorial-1.c的文本内容（或在你的Gstreamer目录中找到它)。  

## basic-tutorial-1.c

```
#include <gst/gst.h>  

int
main (int argc, char *argv[])  
{  
  GstElement *pipeline;  
  GstBus *bus;  
  GstMessage *msg;  

  /* Initialize GStreamer */  
  gst_init (&argc, &argv);  

  /* Build the pipeline */  
  pipeline =  
      gst_parse_launch
      ("playbin uri=https://www.freedesktop.org/software/gstreamer-sdk/data/media/sintel_trailer-480p.webm",
      NULL);  

  /* Start playing */  
  gst_element_set_state (pipeline, GST_STATE_PLAYING);  

  /* Wait until error or EOS */  
  bus = gst_element_get_bus (pipeline);  
  msg =
      gst_bus_timed_pop_filtered (bus, GST_CLOCK_TIME_NONE,
      GST_MESSAGE_ERROR | GST_MESSAGE_EOS);  

  /* Free resources */  
  if (msg != NULL)  
    gst_message_unref (msg);  
  gst_object_unref (bus);  
  gst_element_set_state (pipeline, GST_STATE_NULL);  
  gst_object_unref (pipeline);  
  return 0;  
}  
```   

根据你系统对应的编译选项来编译它，如果你出现了编译错误，请再次检查安装过程中给的说明。  

如果编译成功，启动可执行文件。你会发现一个窗口弹出，中间包含一个正在播放的网络视频与音频，恭喜！  
***
  ### 备注：

  如果你在编译过程中需要帮助，请参阅 Building the turorials中对应你所在系统的部分  
    其中Linux系统中可以使用以下命令:  
    ```
      gcc basic-tutorial-1.c -o basic-tutorial-1 `pkg-config --cflags --libs gstreamer-1.0` 
    ```  
  如果你在执行过程中需要帮助，请参阅 Running the turorials中对应你所在系统的部分，运行需要依赖于gstreamer-1.0库  
***
  
  
本教程会打开一个窗口并播放带音频的视频。该视频通过互联网获取，因此窗口可能过一段时间才会出现，时间长短取决于网络连接速度。  
同时，本教程并没有做任何的延迟管理(缓存)，所以如果网络连接速度过慢，视频播放可能会出现卡顿。参阅Basic tutorial 12:Streaming来解决这个问题。  
    

  
  
