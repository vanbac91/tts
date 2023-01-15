## Yêu cầu cốt lõi là đây `wss://eastus.tts.speech.microsoft.com/cognitiveservices/websocket/v1`
![image-20220313185522719](assets/debugger_note/image-20220313185522719.png) Có hai tham số của yêu cầu này`Authorization` và `X-ConnectionId`
Trong `Authorization` Từ mã nguồn web`token`，Có thể được thực hiện trực tiếp với thường xuyên

![image-20220313184024591](assets/debugger_note/image-20220313184024591.png)

`X-ConnectionId`Hơi phức tạp，Tìm kiếm `cognitiveservices/websocket/v1`Định vị vào vị trí sau, người ta thấy rằng nó đã tạo ra，Theo đuổi một bước

![image-20220313184200274](assets/debugger_note/image-20220313184200274.png)

Khám phá `createNoDashGuid` Tạo ra

![image-20220313184347146](assets/debugger_note/image-20220313184347146.png)

Tiếp tục phục hồi là một uuid4

![image-20220313184505031](assets/debugger_note/image-20220313184505031.png)

python Mô phỏng rất đơn giản

```python
import uuid
print(uuid.uuid4().hex.upper())
```

## websocket

Sau yêu cầu trên，Thay đổi thành WebSocket

Gửi văn bản của client，Máy chủ trả về nhị phân

Ví dụ về nội dung đã gửi

```
Path: speech.config
X-RequestId: 1570E9705B67461494126EE84ED36CD9
X-Timestamp: 2022-03-13T10:48:30.067Z
Content-Type: application/json

{"context":{"system":{"name":"SpeechSDK","version":"1.19.0","build":"JavaScript","lang":"JavaScript"},"os":{"platform":"Browser/Win32","name":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36","version":"5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36"}}}
```



```
Path: synthesis.context
X-RequestId: 1570E9705B67461494126EE84ED36CD9
X-Timestamp: 2022-03-13T10:48:30.068Z
Content-Type: application/json

{"synthesis":{"audio":{"metadataOptions":{"bookmarkEnabled":false,"sentenceBoundaryEnabled":false,"visemeEnabled":false,"wordBoundaryEnabled":false},"outputFormat":"audio-24khz-160kbitrate-mono-mp3"},"language":{"autoDetection":false}}}
```

Gói gửi thứ ba chứa các văn bản cần được chuyển đổi

```
Path: ssml
X-RequestId: 1570E9705B67461494126EE84ED36CD9
X-Timestamp: 2022-03-13T10:48:30.068Z
Content-Type: application/ssml+xml

<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" xmlns:emo="http://www.w3.org/2009/10/emotionml" version="1.0" xml:lang="en-US"><voice name="en-US-JennyNeural"><prosody rate="0%" pitch="0%">You can replace this text with any text you wish. You can either write in this text box or paste your own text here.

Try different languages and voices. Change the speed and the pitch of the voice. You can even tweak the SSML (Speech Synthesis Markup Language) to control how the different sections of the text sound. Click on SSML above to give it a try!

Enjoy using Text to Speech!</prosody></voice></speak>
```



Sau đó, máy chủ trả về một nhị phân giai đoạn，Chỉ cần trích xuất`Path:audio`Khâu nội dung sau là tệp MP3 chúng ta cần.

![image-20220313184910519](assets/debugger_note/image-20220313184910519.png)

