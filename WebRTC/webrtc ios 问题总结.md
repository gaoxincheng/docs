### 声音播放卡顿问题

- 分两个`AudioUnit` 处理，一个播放，另一个采集
- `AVAudioSession`使用 `playAndRecord`，mode使用`VoiceChat`，设置默认输出到扬声器的option.
- 开麦时，采集`AudioUnit`只做start，关麦时若有声音播放，则采集`AudioUnit`执行stop，否则采集`AudioUnit`销毁
- 停止播放时，若没有采集，则将采集`AudioUnit`销毁
- 开关麦统一用同一个Mode，能避免音量变化问题。
  
### webrtc AVAudioSession的管理
- 建议使用webrtc封装的`RTCAudioSession`进行管理，内部会判断当前的设置是否一样，防止重复设置，并且有加锁保护。
  
### 播放有变声或杂音问题

- 三方打断或蓝牙切换时，会导致`AVAudioSession` 中的采样率发生变化，这是需重新初始化`AudioUnit`，否则会出现杂音或变声问题

### 关于AudioUnit的VPIO、RemoteIO 与模式(mode)的关系

- `AudioUnit` 使用功能 `VPIO` 时，声音通话效果最好，因为`VPIO`下默认自带回声处理、降噪、自动增益等功能，但音质会变差，不适合媒体播放。并且使用`VPIO`时，mode会自动切换为`VoiceChat`，`VoiceChat`模式下声音就无法调到最小。
- `AudioUnit` 使用功能`RemoteIO`时，通话效果不好，有明显的回声，即使强制打开使用webrtc的软件回声处理，效果也很差，不知道为什么webrtc的处理效果这么差。使用`RemoteIO`适合做媒体播放，能呈现更好的音质且音量也能调到最低，因为他不会将模式切换到`VoiceChat`。

### VoiceChat模式下，不能使用IOS自带的录屏软件，无法保存或没有声音

-   可能苹果出于安全考虑，禁止在通话模式下进行录屏,QQ电话也无法录屏;