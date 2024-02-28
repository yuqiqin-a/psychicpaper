---
{"dg-publish":true,"permalink":"/D-Source/use-pydub-to-cut-audio/","created":"2022-06-15T09:59:29.000+08:00","updated":"2022-06-15T09:59:29.000+08:00"}
---

# 使用pydub切分音频
前段时间另一个项目的负责人在做一款听力相关的demo测试，原定在周五下班前给开发一批单词音频的数据，但是因为原本找的一批单词音频音质很差，而且读音有误，眼看快要上线了。情急之下找了另外一个语音合成的软件，临时合成了200多个音频，但是导出的时候给的是完整的一个mp3，可开发的要求是单独的音频文件，现场用Adobe Premiere Pro切肯定也来不及了。

于是就找到了我，还好StackOverflow相关帖子很多，用pydub就能解决，代码也很好理解，具体代码如下：

```Python
from pydub import AudioSegment  
from pydub.silence import split_on_silence  
  
# 放在虚拟环境
AudioSegment.ffprobe = '~/site-packages/ffprobe'  
  
def match_target_amplitude(aChunk, target_dBFS):  
    change_in_dBFS = target_dBFS - aChunk.dBFS  
    return aChunk.apply_gain(change_in_dBFS)  
  
  
# 选择音频
song = AudioSegment.from_mp3(".mp3")  

# 阈值可以根据实际情况调整
chunks = split_on_silence (  
    song,  
    min_silence_len=200,  
    silence_thresh=-100  
)  
for i, chunk in enumerate(chunks):  
	# 前后都加silence才不会太突兀
    silence_chunk = AudioSegment.silent(duration=500)  
    audio_chunk = silence_chunk + chunk + silence_chunk  
  
	# 平衡一下每个音频的音量
    normalized_chunk = match_target_amplitude(audio_chunk, -20.0) 
	
	# 按计数器命名并导出
    normalized_chunk.export(  
        f"~/chunk{i}.mp3",  
        bitrate = "192k",  
        format = "mp3"  
    )
```

唯一可能会踩的坑是，当使用pip安装玩pydub之后，运行会报错
`FileNotFoundError: [Errno 2] No such file or directory: 'ffprobe'`

或者
`FileNotFoundError: [Errno 2] No such file or directory: 'ffmpeg'`

这个似乎是一个高频报错，网上很多办法都不好用，唯一一个成功的解决方案是：去ffbinaries的[官网](https://ffbinaries.com/downloads)下载`ffprobe`和 `ffmpeg`，然后放到虚拟环境的`site-packages`的目录下。