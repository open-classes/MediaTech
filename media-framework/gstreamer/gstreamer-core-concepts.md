# gstreamer的工作原理

GStreamer是一组库和插件，可用于实现各种多媒体应用程序，包括台式机播放器，音频/视频记录器，多媒体服务器，媒体转换器等。通过构建由元素(GstElement)组成的管道(GstPipeline)来构建应用程序。管道是若干个元素的容器，应用程序将焊盘上的元素链接(gst_element_link)在一起以构建管道。元素(GstElement)是对多媒体流执行某些操作的对象，例如：

- 读取文件
- 在格式之间解码或编码
- 从硬件设备捕获
- 渲染到硬件设备
- 混合或复用多个流

Gstreamer应用程序不必管理实际数据流/解码/转换/同步等的任何复杂性，而只需在管道(GstPipeline)对象上调用诸如PLAY / PAUSE / STOP之类的高级功能。Gstreamer应用程序还从(GstPipeline)接收消息和通知，例如元数据，警告，错误和EOS消息。如果应用程序需要对图形进行更多控制，则可以直接访问管道中的元素和焊盘。

## Dynamic pipelines

应用程序创建一个管道来管理应用程序中的所有元素。管道是特殊的bin子类，它为其子代提供以下功能：

- 选择并管理所有子时钟的全局时钟。
- running_time根据所选时钟进行管理。Running_time是管道在PLAYING状态中花费的经过时间，用于同步。
- 管理管道中的延迟。
- 提供手段使元素与程序通信 GstBus。
- 管理元素的全局状态，例如错误和流末。

GStreamer应用程序构建pipelines的过程：

- 该应用程序首先使用创建管道元素 gst_pipeline_new()。
- 使用和将元素添加到管道gst_bin_add()或从管道中删除gst_bin_remove()。
- 添加元素后，可以使用检索焊盘 gst_element_get_pad()。
- 然后可以将打击垫链接到一起 gst_pad_link()。

```C
#include <gst/gst.h>

int main(int argc, char *argv[]) {
  GstElement *pipeline, *source, *sink;
  GstBus *bus;
  GstStateChangeReturn ret;

  /* Initialize GStreamer */
  gst_init (&argc, &argv);

  /* Create the elements */
  source = gst_element_factory_make ("videotestsrc", "source");
  sink   = gst_element_factory_make ("autovideosink", "sink");

  /* Create the empty pipeline */
  pipeline = gst_pipeline_new ("test-pipeline");

  /* Build the pipeline */
  gst_bin_add_many (GST_BIN (pipeline), source, sink, NULL);
  if (gst_element_link (source, sink) != TRUE) {
    g_printerr ("Elements could not be linked.\n");
    gst_object_unref (pipeline);
    return -1;
  }

  /* Modify the source's properties */
  g_object_set (source, "pattern", 0, NULL);

  /* Start playing */
  ret = gst_element_set_state (pipeline, GST_STATE_PLAYING);

  /* Wait until error or EOS */
  bus = gst_element_get_bus (pipeline);

  /* Free resources */
  gst_object_unref (bus);
  gst_element_set_state (pipeline, GST_STATE_NULL);
  gst_object_unref (pipeline);
  return 0;
}
```

## 时钟管理(Clock Management)

将GstClock返回与方法单调递增的时间 _get_time()。它的精度和基本时间取决于特定的时钟实现，但是时间始终以纳秒表示。由于未定义时钟的基准，因此返回的时钟时间本身就没有意义，重要的是两个时钟时间之间的差异。时钟报告的时间称为absolute_time。

要同步不同的元素，GstPipeline负责为其中GstClock的所有元素选择并分配一个全局变量。每当管道进入“播放”状态时，都会进行此选择。每当在管道中添加/删除元素时，此选择将​​在下一个状态更改为“正在播放”时重做。添加一个可以提供时钟的元素将GST_MESSAGE_CLOCK_PROVIDE在总线上发布一条消息，以通知父容器需要重新计算时钟的事实。

选择时钟后，NEW_CLOCK总线上会发布一条消息，将时钟告知应用程序。从管道中删除提供时钟的元素后，将CLOCK_LOST发布一条 消息。然后，应用程序必须将管道重新设置为“暂停并播放”，以便让管道选择新的时钟并分配新的基准时间。GStreamer核心提供了一个GstSystemClock基于系统时间的功能。在实现异步通知时，鼓励时钟实现者将此系统时钟子类化。

## Media formats 和 Pad Capabilities

使用键/值对的通用列表来描述媒体类型(Caps)。 键是一个字符串，值可以是int / float / string的单个/列表/范围。
填充功能(Pad Capabilities)是GStreamer的基本元素，尽管在大多数情况下它们是不可见的，因为框架会自动处理它们。

- 什么是填充功能(Pad Capabilities)。
- 如何找回它们。
- 何时检索它们。
- 为什么您需要了解它们。

## Debug Flags

有时情况不会按预期进行，并且从总线中检索到的错误消息（如果有）只是无法提供足够的信息。幸运的是，GStreamer附带了大量调试信息，这些信息通常可以提示问题所在。

- 如何从GStreamer获取更多调试信息。
- 如何将自己的调试信息打印到GStreamer日志中。
- 如何获得管道图

## Playback Speed

快进，反向播放和慢动作都是统称为特技模式的技术，它们的共同点是可以改变正常播放速率。播放速度控制需要考虑一下问题:

- 如何改变播放速率，快慢比正常快进和快退?
- 如何逐帧推进视频?

## References

- [Clocks and synchronization in GStreamer](https://gstreamer.freedesktop.org/documentation/application-development/advanced/clocks.html)