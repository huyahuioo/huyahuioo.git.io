# 视频分割

## 实现方式
为了实现简单，这里采用Python实现视频的分割，在java中调用Python脚本。

## Python实现

### 安装moviepy库
源的选择
- [清华的](https://pypi.tuna.tsinghua.edu.cn/simple)很快，但是下载imageio的时候，卡在99%
- [USTC的](https://pypi.mirrors.ustc.edu.cn/simple)源速度很快，但找不到imageio
- [阿里云的](https://mirrors.aliyun.com/pypi/simple)源速度一般，但是目前最靠谱。

创建pip配置文件
```shell
vim ~/.pip/pip.conf
```
编写文件内容如下
```
[global]
timeout = 6000
index-url = https://mirrors.aliyun.com/pypi/simple
[install]
use-mirrors = true
mirros = https://pypi.mirrors.ustc.edu.cn/simple
```

```shell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple moviepy
```

### Python代码

#### 第一次运行报错
```python
import moviepy
import miageio

from moviepy.editor import *

video = VideoFileClip("video.mp4").subclip(108,138)

result = CompositeVideoClip([video,])
reslut.to_videofile("video_1.mp4")
```
提示“imageio.core.fetching.NeedDownloadError: Need ffmpeg exe. You can download it by calling: imageio.plugins.ffmpeg.download()"

解决方法：
在代码中添加`iamgeio.plugins.ffmpet.download()`
```python
import moviepy
import miageio

iamgeio.plugins.ffmpeg.download()
from moviepy.editor import *

video = VideoFileClip("video.mp4").subclip(108,138)

result = CompositeVideoClip([video,])
reslut.to_videofile("video_1.mp4")
```

第一次运行，因为要下载imageio.plugins.ffmpeg，速度很慢。
以后运行，感觉效率也不是很好。只能作为一个暂时的解决方法吧！

## 在Java中调用Python脚本
```Java
String path_to_cut_py = "/home/huyahui/jars/py/cut_video.py";
try{
    System.out.println("Cut video :" + local_path + " start");

    String[] args = new String[]{"python", path_to_cut_py};
    Process pr = Runtime.getRuntime().exec(args);
    BufferedReader in = new BufferedReader(new InputStreamReader(pr.getErrorStream()));

    String line = null;
    while((line = in.readLine()) != null) {
        System.out.println(line);
    }

    in.close();
    pr.waitFor();
    System.out.println("Cut video end. return: " + pr.exitValue());
}catch (Exception e) {
    e.printStackTrace();
}

```
**脚本路径一定要用绝对路径？**
反正调用脚本时，路径写为
>~/jars/***.py

是不能调的

出现了一个新的问题**单独执行Python脚本可以，但是在Java中调用执行到一半就退出了**。

怀疑还是路径的问题，这次是Python脚本中的路径的问题。到路径哪一句就卡住了。
果然！我们把Python脚本的错误输出后，
用**pr.getErrorStream()** 来获取pr的错误输出。就可以发现出现了以下错误
```
Traceback (most recent call last):
  File "/home/huyahui/jars/py/cut_video.py", line 12, in <module>
    video = VideoFileClip("video.mp4").subclip(108, 138)
  File "/home/huyahui/.local/lib/python2.7/site-packages/moviepy/video/io/VideoFileClip.py", line 81, in __init__
    fps_source=fps_source)
  File "/home/huyahui/.local/lib/python2.7/site-packages/moviepy/video/io/ffmpeg_reader.py", line 32, in __init__
    fps_source)
  File "/home/huyahui/.local/lib/python2.7/site-packages/moviepy/video/io/ffmpeg_reader.py", line 272, in ffmpeg_parse_infos
    "path.")%filename)
IOError: MoviePy error: the file video.mp4 could not be found !
Please check that you entered the correct path.
```
将Python脚本中的文件路径修改为`./video.mp4`也不行。改为绝对路径后就可以了，这个问题不大，反正真正使用的时候我们传入的就是绝对路径。

**终于成功生成了！**
