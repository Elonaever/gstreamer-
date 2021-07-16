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
    

# 代码分析：
让我们一起来回顾一下这些代码都做了什么：

```
/* Initialize GStreamer */
gst_init (&argc, &argv);
```
这一句必须位于你所有Gstreamer命令的开始，通常而言gst_init()会做以下事情：  
·初始化所有内部数据结构  
·检查有哪些有效的Plugin插件  
·执行其他传入的用于gstreamer的命令行选项  
  
如果你始终将命令行参数argc和argv传递给gst_init(), 你的程序将自动受益于Gstreamer标准命令行选项(参阅Basic tutorial 10:Gstreamer Tools)。  
  
  
```
  pipeline =
      gst_parse_launch
      ("playbin uri=https://www.freedesktop.org/software/gstreamer-sdk/data/media/sintel_trailer-480p.webm",
      NULL);
```
这行代码是本次教程的核心，举例展示了两个关键信息： gst_parse_launch()和playbin。 
### gst_parse_launch
Gstreamer是一个用于处理多媒体流的框架。媒体数据从"source" elements(生产者)元素开始，达到"sink" elements(消费者)元素，中间会经理一系列的中间elements从而处理各种任务。这些所有互联的elements整体被称为一个"pipeline"。  
  
在Gstremaer中，你会经常通过手动组装各个独立的elements来构造pipeline。但是在pipeline非常简单，且你不需要任何高级特性的情况下，你可以使用gst_parse_launch()来快速组建一个pipeline。 
  
这个函数会接受一个代表pipeline的文本参数，并直接建立对应的pipeline。这个函数十分方便，并有一个专门为此构建的工具(参阅Basic tutorial 10:Gstreamer Tools来了解gst-launch-1.0及相关用法)。  
### playbin
所以，我们这次希望gst_parse_launch()为我们构建的pipeline是什么呢？这里就是第二个关键参数：我们正在构建由一个单独的名为"playbin"的element组成的pipeline。  
  
playbin是一个同时作为source和sink的element，同时它就是一整个pipeline。在其内部，它会创建并连接播放视频过程中所有必要的elements，因此你不比过于操心细节。 
  
虽然它并不像手动pipeline一样提供更细粒度的控制项，但其仍然提供了大部分场景下需要的控制选项，包括这次教程中所需要的。  
  
在这次实例中，我们只给playbin传递了一个参数，就是我们希望播放的视频的URI。可以尝试一下传递别的参数值！无论是http://格式 或者是file:// 格式的URI，playbin都会实例化对应的Gstreamer source element。  
  
如果你输入了错误的URI，或者这个文件不存在，或者对应的plug-in丢失，Gstreamer都提供了多种通知机制。但是在这个实例中我们做的只是收到错误并退出，并不会提供过多的反馈信息。  
  
```
/* Start playing */
gst_element_set_state (pipeline, GST_STATE_PLAYING);
```
这行代码同时引入了另一个关键概念：state。每个Gstreamer element都拥有各自的state，这里你可以把它理解为DVD播放器的播放/暂停按钮。所以说，除非你把整个pipeline的state设为PLAYING状态，否则视频并不会开始播放。  
  
这行代码中，gst_element_set_state()正在设置pipeline(我们唯一的element)的state为PLAYING，这将导致播放开始。  

```
/* Wait until error or EOS */
bus = gst_element_get_bus (pipeline);
msg =
      gst_bus_timed_pop_filtered (bus, GST_CLOCK_TIME_NONE,
      GST_MESSAGE_ERROR | GST_MESSAGE_EOS);
```
这些代码会等待错误信息发生或者播放结束。gst_element_get_bus()用来获取pipeline的bus，而gst_bus_timed_pop_filtered()将堵塞至从bus接收到ERROR或者EOS信息。不要太在意这行代码，Gstreamer的bus会在Basic tutorial 2:Gstreamer concepts中详细介绍。  
  
以上就是所有的主要代码了！从这开始，GStreamer就会接管所有的任务。进程将会在播放结束(EOS)或者错误发生(试着关闭播放窗口，或者拔掉网线)时结束。也可以通过键入Ctrl-C命令结束进程。  

# Cleanup
在应用结束前，我们还是要做一些收尾工作。
```
/* Free resources */
if (msg != NULL)
  gst_message_unref (msg);
gst_object_unref (bus);
gst_element_set_state (pipeline, GST_STATE_NULL);
gst_object_unref (pipeline);
```
时刻注意阅读所用函数的相关文档，来确认使用完毕后是否需要释放对应的资源。 
  
本例中，gst_bus_timed_pop_filtered()返回了一个message，这个message需要通过gst_message_unref()方法释放(参阅Basic tutorial 2:Gstreamer concepts了解更多与message相关的信息)。 
  
gst_element_get_bus()则为bus增加了一个引用计数，因此也需要通过gst_object_unref()方法释放。将pipeline的state设为NULL可以确保它所申请的相关资源被释放(参考Basic tutorial 3:Dynamic pipelines)。最终，使用gst_object_unref()释放pipeline及其包含的内容。  
  
# Conclusion
这里你的第一个GStreamer教程就结束了。我希望这个教程的简介可以让你们感受到这个框架的强大之处！  
  
让我们总结一下，今天我们一共学会了：  
  
·如何通过gst_init()初始化GStreamer  
·如何使用gst_parse_launch()根据对应的文本描述符快速的创建pipeline  
·如何通过playbin创建一个自动播放的pipeline  
·如何通过gst_element_set_state()通知GStreamer开始播放   
·如何使用gst_element_get_bus()和gst_bus_timed_pop_filetered()使自己躺平，让GStreamer来接管所有任务  
  
下一个教程将继续介绍更多的GStreamer elements，并为你展示如何手动构建一个pipeline  
  
很高兴在这里见到你，再见！  


### 参考文档：
https://gstreamer.freedesktop.org/documentation/tutorials/basic/hello-world.html?gi-language=c










  
