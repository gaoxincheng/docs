## 音频相关知识点

- `webrtc` 每次处理10ms大小的语音帧（`AudioFrame`)
- 采样率 ： 声卡每秒采集次数
- 采样位数 ： 声卡每次采集位数
- [其他知识点](https://www.cnblogs.com/yongdaimi/p/10722355.html)

## Android webrtc声音采集与播放流程梳理

### 声音采集(AudioRecord)

*webrtc\voiceengine\WebRtcAudioRecord.java* 开启线程获取设备采集的音频数据，通过方法`nativeDataIsRecorded` 通知对应jni层

- *webrtc\modules\audio_device\android\audio_record_jni.cc* 的`DataIsRecorded`方法以通知有新的音频数据采集
- *webrtc\modules\audio_device\audio_device_buffer.cc* 会调用`DeliverRecordedData` 
- *webrtc\voice_engine\voe_base_impl.cc* 的`RecordedDataIsAvailable` 方法被调用
- *webrtc\voice_engine\voe_base_impl.cc* `ProcessRecordedDataWithAPM` 会做语音包处理，该方法会调用TransmitMixer 进行混音、编码等操作
    - *webrtc\voice_engine\transmit_mixer.cc* 中的`PrepareDemux`会生成`AudioFrame`，并调`AudioProcess`模块对语音帧处理
        - *webrtc\modules\audio_processing\audio_processing_impl.cc* 的`ProcessStream`会执行自动增益(AGC)、高斯过滤、回声抑制(AEC近端数据处理)、静音检测(VAD)等处理
    - *webrtc\voice_engine\transmit_mixer.cc* 中 `EncodeAndSend`的会做编码并发送
        - *webrtc_android\webrtc\voice_engine\channel.cc* 中 `EncodeAndSend` 会调用`AudioCoding`模块对语音帧处理，等待发送，会有底层语音包发送线程定时获取该语音帧（`AudioFrame`）进行网络发送


### 语音播放(AudioTrack)

*webrtc\modules\audio_device\android\java\src\org\webrtc\voiceengine\WebRtcAudioTrack.java* 会开启线程定时请求播放数据，通过方法`nativeGetPlayoutData`从相应jni层获取播放数据
- *webrtc\modules\audio_device\android\audio_track_jni.cc* `GetPlayoutData`的方法获取播放数据
- *webrtc\modules\audio_device\audio_device_buffer.cc* 会调用`RequestPlayoutData`获取播放数据
- *webrtc\voice_engine\voe_base_impl.cc* `NeedMorePlayData`的方法会被调用
- *webrtc\voice_engine\voe_base_impl.cc* `GetPlayoutData`的方法会获取底层混音后的数据
    - *webrtc_android\webrtc\voice_engine\output_mixer.cc* 会调用混音模块、`AudioProcess`模块(AEC远端数据处理)等处理

## IOS webrtc声音采集与播放流程梳理

### IOS webrtc不同点

- IOS 采集和播放都是通过系统自带的AudioUnit音频库处理的，具体参考 *webrtc\modules\audio_device\ios\audio_device_ios.mm* 相关代码
- AudioUnit支持多个组合，可实现混音和回音消除（AEC-VPIO模式）等功能
- IOS 系统的AVAudioSession就是用来管理多个APP对音频硬件设备（麦克风，扬声器）的资源使用。AVAudioSession包含多种Category和Mode，Category和Mode组合可适应不同场景。
- AudioUnit 可以设置音频采集和播放的回调方法