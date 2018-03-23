##[Phonon Module](http://doc.qt.io/archives/qt-4.8/phonon-module.html#details) 为多媒体功能提供了命名空间和类。

**Namespaces:**

	phonon::BackendCapabilitise
		//描述了多媒体后台的功能

**class：**

类 | 描述
---|---
Phonon::AudioDataOutput | Access to audio data
Phonon::AudioOutput | Used to send data to audio output devices
Phonon::BackendCapabilities::Notifier | 
Phonon::Effect |  Used to transform audio streams

。。。。。。。。。。。。。

##[AudioOutput Class](http://doc.qt.io/archives/qt-4.8/phonon-audiooutput.html)

用来给音频输入设备发送数据。AudioOutput类通过一个声音设备播放声音。音频输出需要使用 createPath() 连接到一个 [MediaObject](http://doc.qt.io/archives/qt-4.8/phonon-mediaobject.html)。要开始播放，你可以调用media object的 play()。

*只能播放wav文件* 

    Phonon::MediaObject *mediaObject = new Phonon::MediaObject(this);
    mediaObject->setCurrentSource(Phonon::MediaSource("/mymusic/barbiegirl.wav"));
    Phonon::AudioOutput *audioOutput =
        new Phonon::AudioOutput(Phonon::MusicCategory, this);
    Phonon::Path path = Phonon::createPath(mediaObject, audioOutput);

##[QMediaObject](http://doc.qt.io/archives/qt-4.8/phonon-mediaobject.html)

为媒体播放提供了一个接口。 



-------------------------

# QtMultimedia

## QAudioOutput

提供一个接口，用于给一个音频输出设备发送音频数据。

要播放一个文件，用一个 **QIODevice** 调用 start() 即可。

> QIODevice:在Qt中所有 I/O的基类接口

## QAudioFormat

存储音频流参数信息。一个 audio format 表明数据是如何在音频流中排列的。

参数有：

- Sample Rate
- Number of channels
- Sample size
- Sample type
- Byte order

QAudioFormat通常用于 **QAudioInput** 和 **QAudioOutput** 中

	//播放设备

	  QFile sourceFile;   // class member.
	  QAudioOutput* audio; // class member.
	  {
	      sourceFile.setFileName("/tmp/test.raw");
	      sourceFile.open(QIODevice::ReadOnly);
	
	      QAudioFormat format;
	      // Set up the format, eg.
	      format.setSampleRate(8000);
	      format.setChannelCount(1);
	      format.setSampleSize(8);
	      format.setCodec("audio/pcm");
	      format.setByteOrder(QAudioFormat::LittleEndian);
	      format.setSampleType(QAudioFormat::UnSignedInt);
	
	      QAudioDeviceInfo info(QAudioDeviceInfo::defaultOutputDevice());
	      if (!info.isFormatSupported(format)) {
	          qWarning() << "Raw audio format not supported by backend, cannot play audio.";
	          return;
	      }
	
	      audio = new QAudioOutput(format, this);
	      connect(audio, SIGNAL(stateChanged(QAudio::State)), this, SLOT(handleStateChanged(QAudio::State)));
	      audio->start(&sourceFile);
	  }

# 用 **QAudioOutput** 播放wav文件 #

*不能播放 MP3、wma文件*

    QAudioOutput* audio;
    QFile sourceFile("C:/Users/xiaweiyi123/Downloads/a/b.wav");

    bool p = sourceFile.open(QIODevice::ReadOnly);
    if(p==false)
        qDebug() << "error";
    else if(p==true)
        qDebug() << "successful";

    QAudioFormat format;
    format.setSampleRate(44100);
    format.setChannelCount(2);
    format.setSampleSize(16);
    format.setCodec("audio/pcm");
    format.setByteOrder(QAudioFormat::LittleEndian);
    format.setSampleType(QAudioFormat::UnSignedInt);
   
    audio = new QAudioOutput(format);
    audio->start(&sourceFile);

> Question:

> 把上面的程序在 main.cpp 中运行，程序正常。但是把程序放在 xxx.cpp 中，在 main 里面定义

	xxx w;
	w.show();
> 该程序出错，原因不明


# QMediaPlayer

**QMediaPlayer** 允许播放一种媒体源。能够被用在播放歌曲、电影和互联网radio。播放的内容被确认为一种 **QMediaContent** ，**QVideoWidget** 能与 **QMediaPlayer** 一起用于视频播放，与 **QMediaPlaylist** 一起用于歌单功能。

- 设置当前媒体播放源：

		QMediaPlayer::setMedia(QMediaContent &media,QIODevice *stream = QNULLPTR)
			//第一个参数为 QMediaContent 对象，可以是本地文件，也可以是网络文件。
- 切换播放状态：

		QMediaPlayer::play()
		QMediaPlayer::pause()
		QMediaPlayer::stop()
- 两种重要的枚举类型：State和MediaStatus。
	- State：包含当前的播放状态，以上三种。默认状态： QMediaplayer::Stopped
	- MediaStatus：包含当前媒体文件的有效性。有 { UnknownMediaStatus, NoMedia, LoadingMedia, LoadedMedia, …, InvalidMedia }等状态。默认：QMediaPlayer::NoMedia

# QVideoWidget

一个展示视频的控件。要使用它，需要定义一个**QMediaPlayer**，然后将 **QMediaPlayer** 的**VideoOutput**设置为 **QVideoWidget** 对象即可。

	player = new QMediaPlayer;
	
	player->setMedia(QMediaContent(QUrl("http://example.com/myclip2.mp4")));
	videoWidget = new QVideoWidget;
	player->setVideoOutput(videoWidget);
	
	videoWidget->show();
	player->play();

			//网上例程，未成功

**WWWWWWWWWWWWWWWWWWW**

------------------------------------

# QMediaPlaylist

提供播放内容的列表用于播放。

	player = new QMediaPlayer;
	playlist = new QMediaPlaylist；
	playlist->addMedia(QMediaContent(QUrl::fromLocalFile("C:/Users/xiaweiyi123/Downloads/晴天.mp3")));
	playlist->addMedia(QMediaContent(QUrl::fromLocalFile("C:/Users/xiaweiyi123/Downloads/顽固.mp3")));
	playlist->setCurrentIndex(0);	

	player->setPlaylist(playlist);
	player->play();
		
## CurrentIndex  问题

不管之前的 CurrentIndex 为多少。

	playlist->setPlaylist(playlist);
		//会把 currentIndex 设为0。
而把给 **QMediaPlaylist** 用**addMedia** 添加了内容后， 该playlist列表的 **CurrentIndex** 的内容均为 -1.而把 player 添加歌单后， **CurrentIndex** 值为0.

    playlist->addMedia(QMediaContent(QUrl::fromLocalFile("C:/Users/xiaweiyi123/Downloads/七里香.mp3")));
    playlist->addMedia(QMediaContent(QUrl::fromLocalFile("C:/Users/xiaweiyi123/Downloads/晴天.mp3")));
    playlist->addMedia(QMediaContent(QUrl::fromLocalFile("C:/Users/xiaweiyi123/Downloads/星空.mp3")));

    qDebug()<<playlist->currentIndex();       //输出-1
    playlist->setCurrentIndex(1);        
    	//此语句没用，因为后面会把值重新设为0.
    playlist->setPlaybackMode(QMediaPlaylist::Loop);
    player->setPlaylist(playlist);
    qDebug()<<playlist->currentIndex();
    	//输出0
    playlist->setCurrentIndex(1);
    	//把CurrentIndex的值设为1，播放 晴天
    player->play();		
