# voicevox音声引擎

voicevox音声引擎相关技术笔记。

## docker部署引擎
```yaml
version: "3"
services:
  voicevox:
    image: aoirint/voicevox_engine:cpu-ubuntu20.04-latest
    ports:
      - 50021:50021
    container_name: voicevox
    network_mode: bridge
    hostname: voicevox
    restart: always

#
#pip install voicevox-client
#http://127.0.0.1:50021/docs

```

## 发送http请求生成语音
```yaml
echo -n "こんにちは、音声合成の世界へようこそ" >text.txt

curl -s   -X POST  "localhost:50021/audio_query?speaker=1"   --get --data-urlencode text@text.txt   "pronunciation=テストツー" > query.json

curl -s \
    -H "Content-Type: application/json" \
    -X POST \
    -d @query.json \
    "localhost:50021/synthesis?speaker=1" \
    > audio.wav
```

## python生成
```yaml
import requests
import argparse
import json
import time

from pydub import AudioSegment
from pydub.playback import play

# 指定VOICEVOX的主机名
HOSTNAME = '192.168.100.110'

##读取文件内容
try:
    file = open(r'F:\桌面\audio_file\text.txt', 'r', encoding='utf-8')  # 打开文件
    data = file.read()  # 读取文件内容
finally:
    if file:
        file.close()

# 初始化 #共有54个角色可供使用 0-53
# 15 18 17 16 27是大姐姐类型的声音
# 20 大小姐
# 19 22 36 50是asmr的声音 声音很轻
# 14 8 6 4 2 0 24 23 31 37 44 46jk
# 10 7 5 3 1 38 43 45幼
# 26 29中性少年
# 32 机器人
# 28 lnn
# 外国腔 47 48
datatext = ""
speaker_id = 15
filename = f"{speaker_id}user"
output_path = r'F:\桌面\audio_file'

# 命令参数
parser = argparse.ArgumentParser(description='VOICEVOX API')
parser.add_argument("-t", "--text", type=str, default=data, help='阅读的文本内容')
parser.add_argument('-id', '--speaker_id', type=int, default=speaker_id, help='角色ID')
parser.add_argument('-f', '--filename', type=str, default=filename, help='文件名')
parser.add_argument('-o', '--output_path', type=str, default=output_path, help='保存的目录')

# 参数引用赋值
args = parser.parse_args()
input_texts = args.text
speaker = args.speaker_id
filename = args.filename
output_path = args.output_path

# 显示参数详情
# print(args)

start = time.perf_counter()  # 程序开始

# texts = input_texts.split('。') #「 。」以句号为一行，分段式，读取、生成
# texts = input_texts.split()  #以空格或回车为分隔
texts = input_texts.split("09asdfs")  # 瞎打一个值它就识别不到要分段的位置，就会把整文都说出来。

n = 0
# 音声合成処理のループ
for i, text in enumerate(texts):
    # 文字列が空の場合は処理しない
    if text == '': continue
    n = 1 + n

    # audio_query (音声合成用のクエリを作成するAPI)
    res1 = requests.post('http://' + HOSTNAME + ':50021/audio_query',
                         params={'text': text, 'speaker': speaker})
    # synthesis (音声合成するAPI)
    res2 = requests.post('http://' + HOSTNAME + ':50021/synthesis',
                         params={'speaker': speaker},
                         data=json.dumps(res1.json()))
    # 写入wav文件
    with open(output_path + '/' + filename + f'_%03d.wav' % i, mode='wb') as f:
        f.write(res2.content)
        f.close()
        print(f'第{n}段完成：', text)  # 输出这段文本
        # 播放音频
        song = AudioSegment.from_wav(f.name)
        play(song)

print("-----------------------")
print("生成成功! 角色id为：", speaker_id, "\n文件名为：" + f.name)
print("-----------------------")
# print("等待3秒后播放音频")
# time.sleep(3)
# #播放音频
# song=AudioSegment.from_wav(f.name)
# play(song)
# # print("完成")

import contextlib
import wave

file_path = f.name
with contextlib.closing(wave.open(file_path, 'r')) as f:
    frames = f.getnframes()
    rate = f.getframerate()
    wav_length = frames / float(rate)
    print("音频长度：", int(wav_length), "秒")
print("-----------------------")

stop = time.perf_counter() - start
print(stop)

```
