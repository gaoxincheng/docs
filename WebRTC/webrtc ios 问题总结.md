### 声音播放卡顿问题

- 分两个AudioUnit 处理，一个播放，另一个采集
- AVAudioSession使用 playAndRecord，mode使用VoiceChat，设置默认输出到扬声器的option.
- 开麦时，采集AudioUnit只做start，关麦时若有声音播放，则采集AudioUnit执行stop，否则采集AudioUnit销毁
- 停止播放时，若没有采集，则将采集AudioUnit销毁
- 开关麦统一用同一个Mode，能避免音量变化问题。
  
### webrtc AVAudioSession的管理
- 建议使用webrtc封装的RTCAudioSession进行管理，内部会判断当前的设置是否一样，防止重复设置，并且有加锁保护。
  