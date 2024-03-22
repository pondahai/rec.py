# rec.py

## 影片錄製與音訊加入程式

這個程式可以錄製影片和音訊，並將音訊加入影片。

### 程式功能

* 錄製影片
* 錄製音訊
* 將音訊加入影片
* 自動播放輸出影片

### 程式碼說明

```python
import cv2
import pyaudio
import wave
import subprocess
import os

# 設定 webcam 和 pyaudio
cap = cv2.VideoCapture(0)
p = pyaudio.PyAudio()

# 設定錄音參數
stream = p.open(format=pyaudio.paInt16, channels=2, rate=44100, frames_per_buffer=2205, input=True)

# 設定影片儲存參數
fourcc = cv2.VideoWriter_fourcc(*'mp4v')
out = cv2.VideoWriter('output.mp4', fourcc, 20.0, (640, 480))

# 開始錄影和錄音
# frames = []  # 存放影像幀的列表
audio_data = []  # 存放音訊數據的列表
while True:
    # 讀取 webcam 影像
    ret, frame = cap.read()
#     frames.append(frame)
    # 將影像寫入影片
    out.write(frame)

    # 讀取 pyaudio 音訊
    data = stream.read(2205)
    audio_data.append(data)

    # 顯示影像（可選）
    cv2.imshow('frame', frame)

    # 按下 'q' 鍵結束
    if cv2.waitKey(50) & 0xFF == ord('q'):
        break

# 釋放資源
cap.release()
stream.stop_stream()
stream.close()
out.release()
p.terminate()
cv2.destroyAllWindows()  # 關閉所有 OpenCV 視窗

# 將音訊數據轉換為 WAV 檔案
with wave.open('output.wav', 'wb') as f:
    f.setnchannels(2)
    f.setsampwidth(p.get_sample_size(pyaudio.paInt16))
    f.setframerate(44100)
    f.writeframes(b''.join(audio_data))

# 使用 FFmpeg 將音訊加入影片
subprocess.call(['ffmpeg', '-y', '-i', 'output.mp4', '-i', 'output.wav', '-c:v', 'copy', '-c:a', 'aac', '-strict', '-2', 'output_out.mp4'])

# 刪除 WAV 檔案
# os.remove('output.wav')

# 播放
# 'C:\Program Files\VideoLAN\VLC\vlc.exe', 'output_out.mp4'
os.startfile('output_out.mp4')
```

### 使用方式

1. 將程式碼儲存為 `.py` 檔案。
2. 執行程式碼。
3. 按下 `q` 鍵結束錄製。
4. 輸出影片 `output_out.mp4` 將會自動播放。

### 注意事項

* 您可以修改程式碼來調整錄製參數，例如影片解析度、音訊採樣率等。
* 您可以使用其他影片編輯軟體來調整輸出影片的效果。

