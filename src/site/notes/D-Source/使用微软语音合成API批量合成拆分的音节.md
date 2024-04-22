---
{"dg-publish":true,"dg-permalink":"use-microsoft-api-to-tts","permalink":"/use-microsoft-api-to-tts/","created":"2022-06-29T23:30:04.000+08:00"}
---

# 使用微软语音合成API批量合成拆分的音节
## 背景
继[[D-Source/使用xpath爬取字典内容\|使用xpath爬取字典]]中的音节划分之后，后续还有任务是要制作拆分后的音节的读音。

如果找真人录音的话，成本比较高，而且需要反复纠正录音师许多发音细节（例如避免句重音和并列短语造成的音韵变化），会消耗很多时间和精力。成本最低的办法是使用语音合成的软件或者API批量生成。具体的实现办法有很多，这里就不再赘述。

为了实现精确控制发音，一般在合成的时候需要使用SSML语言对合成内容进行标记，下面主要记录为了满足这次需求所需要使用到的一些SSML参数。

## 具体实现
首先我们已经拿到了单词的音节划分数据，如definition `/ˌde.fɪˈ.nɪ.ʃən/`。

然后我们可以根据音节边界（syllable boundary）对内容拆分，给每一个音节都打上所需的SSML标签。

```
sylb = syllable.strip('/').split('.')
```

以[微软](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-synthesis-markup?tabs=csharp)家的服务为例，我们需要用到的标签有三种，一种是精确控制发音的音素标签`<phoneme alphabet="ipa" ph=""></phoneme>`，一种是为了让音节听起来更自然的语调标签`<prosody contour="(%,Hz)"></prosody>`，最后一种是在音节之间创建停顿的停顿标签`<break time="200ms"/>`。

我们可以根据单词的重音和次重音给不同音节附上不同的音调调值，检索的对象就是重音符号，分别是`ˈ`和`ˌ`。

下面是完整的代码——

```Python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import re


def set_primary_stress(phon):
    return f'<prosody contour="(50%,+50Hz)"><phoneme alphabet="ipa" ph="{phon}">temp</phoneme></prosody>'


def set_secondary_stress(phon):
    return f'<prosody contour="(50%,+25Hz)"><phoneme alphabet="ipa" ph="{phon}">temp</phoneme></prosody>'


def get_xml(syllable):
	xml = ""

	sylb = syllable.strip('/').split('.')

	for i in range(len(sylb)):
		if re.search(r"ˈ", sylb[i]):
			xml += set_primary_stress(sylb[i])
		elif re.search(r"ˌ", sylb[i]):
			xml += set_secondary_stress(sylb[i])
		else:
			xml += f'<phoneme alphabet="ipa" ph="{sylb[i]}">temp</phoneme>'

		if i != len(sylb)-1:
			xml += '<break time="200ms"/>'

	return xml
```
因为使用`<phoneme>`标签之后，被括起来的内容无论写什么都只会按照音素发音，所以直接中间随便写什么内容都可以。
