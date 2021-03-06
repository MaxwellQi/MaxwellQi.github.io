---
layout: post
title: 音频基础知识
description: 简单介绍音频的基础知识，具体包括：音频文件格式、采样率、脉冲编码调制、音频帧的组成等
category: 
tag: 
---

## 常见的音频格式

音频文件格式专指存放音频数据的文件的格式。存在多种不同的格式。

一般获取音频数据的方法是：采用固定的时间间隔，对音频电压采样（量化），并将结果以某种分辨率（例如：CDDA每个采样为16比特或2字节）存储。采样的时间间隔可以有不同的标准，如CDDA采用每秒44100次；DVD采用每秒48000或96000次。因此，采样率，分辨率和声道数目（例如立体声为2声道）是音频文件格式的关键参数。

有两类主要的音频文件格式：

* 无损格式，例如WAV，PCM，ALS，ALAC，TAK，FLAC，APE，WavPack(WV)
* 有损格式，例如MP3，AAC，WMA，Ogg Vorbis

有损文件格式是基于声学心理学的模型，除去人类很难或根本听不到的声音，例如：一个音量很高的声音后面紧跟着一个音量很低的声音。MP3就属于这一类文件。

无损的音频格式（例如FLAC）压缩比大约是2：1，解压时不会产生数据/质量上的损失，解压产生的数据与未压缩的数据完全相同。如需要保证音乐的原始质量，应当选择无损音频编解码器。例如，用免费的FLAC无损音频编解码器你可以在一张DVD-R碟上存储相当于20张CD的音乐。

下面简单介绍一下音频格式的发展历史以及技术特点。具体包括：WAV、MP3、WMA、RA、APE、AAC等。

### WAV音频格式

#### WAV音频格式介绍

WAV是微软公司开发的一种声音文件格式，用于保存WINDOWS平台的音频信息资源，被WINDOWS平台及其应用程序所支持。“*.WAV”格式支持MSADPCM、CCITT A LAW等多种压缩算法，支持多种音频位数、采样频率和声道，标准格式的WAV文件和CD格式一样，也是44.1K的采样频率，速率88K/秒，16位量化位数。

在Windows平台下，基于PCM编码的WAV是被支持得最好的音频格式，所有音频软件都能完美支持，由于本身可以达到较高的音质的要求，因此，WAV也是音乐编辑创作的首选格式，适合保存音乐素材。因此，基于PCM编码的WAV被作为了一种中介的格式，常常使用在其他编码的相互转换之中，例如MP3转换成WMA。

#### WAV音频格式特点

WAV音频格式的优点包括：简单的编/解码(几乎直接存储来自模/数转换器(ADC)的信号)、普遍的认同/支持以及无损耗存储。

WAV格式的主要缺点是需要音频存储空间。对于小的存储限制或小带宽应用而言，这可能是一个重要的问题。WAV格式的另外一个潜在缺陷是在32位WAV文件中的2G限制，这种限制已在为SoundForge开发的W64格式中得到了改善。

常见的WAV文件使用PCM无压缩编码，这使WAV文件的质量极高，体积也出奇大，对于PCM WAV，恐怕也只有无损压缩的音频才能和其有相同的质量，平时我们见的什么mp3,wma(不含 wmalossless)和wav的质量都是差很远的！这点可以通过频谱看出，即使320kbps的mp3和wav一比，也要自卑了！

### MP3音频格式

#### MP3音频格式介绍

MP3全称是动态影像专家压缩标准音频层面3（Moving Picture Experts Group Audio Layer III）。是当今较流行的一种数字音频编码和有损压缩格式，它设计用来大幅度地降低音频数据量，而对于大多数用户来说重放的音质与最初的不压缩音频相比没有明显的下降。它是在1991年由位于德国埃尔朗根的研究组织Fraunhofer-Gesellschaft的一组工程师发明和标准化的。

所谓的MP3也就是指的是MPEG标准中的音频部分，也就是MPEG音频层。根据压缩质量和编码处理的不同分为3层，分别对应`*.mp1`/`*.mp2`/`*.mp3`这3种声音文件。需要提醒大家注意的地方是：MPEG音频文件的压缩是一种有损压缩，MPEG3音频编码具有10：1~12：1的高压缩率，同时基本保持低音频部分不失真，但是牺牲了声音文件中12KHz到16KHz高音频这部分的质量来换取文件的尺寸，相同长度的音乐文件，用`*.mp3`格式来储存，一般只有`*.wav`文件的1/10，而音质要次于CD格式或WAV格式的声音文件。由于其文件尺寸小，音质好;所以在它问世之初还没有什么别的音频格式可以与之匹敌，因而为*.mp3格式的发展提供了良好的条件。

#### MP3音频格式的特点

1. MP3是一个数据压缩格式。
2. 它丢弃掉脉冲编码调制（PCM）音频数据中对人类听觉不重要的数据（类似于JPEG是一个有损图像压缩），从而达到了小得多的文件大小。
3. MP3音频可以按照不同的位速进行压缩，提供了在数据大小和声音质量之间进行权衡的一个范围。MP3格式使用了混合的转换机制将时域信号转换成频域信号。
4. 32波段多相积分滤波器（PQF）。 
5. 36或者12 tap 改良离散余弦滤波器（MDCT）；每个子波段大小可以在0...1和2...31之间独立选择。 
6. MP3不仅有广泛的用户端软件支持，也有很多的硬件支持比如便携式媒体播放器（指MP3播放器）DVD和CD播放器。

### WMA音频格式

#### WMA音频格式介绍

WMA(Windows Media Audio)，它是微软公司推出的与MP3格式齐名的一种音频格式。由于WMA在压缩比和音质方面都超过了MP3，更是远胜于RA(Real Audio)，即使在较低的采样频率下也能产生较好的音质。一般使用Windows Media Audio编码格式的文件以WMA作为扩展名，一些使用Windows Media Audio编码格式编码其所有内容的纯音频ASF文件也使用WMA作为扩展名。

WMA就是Windows Media Audio编码后的文件格式。微软声称，在只有64kbps的码率情况下，WMA可以达到接近CD的音质。和以往的编码不同，WMA支持防复制功能，它支持通过Windows Media Rights Manager 加入保护，可以限制播放时间和播放次数甚至于播放的机器等等。WMA支持流技术，即一边读一边播放，因此WMA可以很轻松的实现在线广播。

#### WMA音频格式特点

1. 在128kbps及以下码流的试听中WMA完全超过了MP3格式，低码流之王不是浪得虚名的，因此WMA非常适合用于网络流媒体。
2. 当码流上升到128kbps以后，WMA的音质并没有如MP3一样随着码流的提高而大大提升。同音源的一个320kbps的MP3与192kbps的WMA相比，音质和渲染力很容易分别出是MP3较优。因此对于有更高要求的用户来说WMA并不是一个适合的格式。
3. WMA 7之后的WMA支持证书加密，未经许可（即未获得许可证书），即使是非法拷贝到本地，也是无法收听的。

### RA音频格式

#### RA音频格式介绍

 RA的全称是RealAudio，是RealNetworks公司成熟的音频格式，它是一种可以在网络上实时传送和播放的音乐文件，是目前网络上比较流行的流媒体技术。此类文件格式有以下几个主要形式：RA（RealAudio）、RM（RealMedia，RealAudio G2）、RMX（RealAudio Secured），这些格式统称为“Real”。
 
  RA格式流媒体技术起源于窄带互联网时期。由于经济发展的需要，人们迫切渴求一种网络技术，以便进行远程信息沟通。从1994年一家叫做progressivenetworks的美国公司成立之初，流媒体开始正式在互联网上登场亮相。1995年，他们推出了c/s架构的音频接受系统realaudio，并在随后的几年内引领了网络流式技术的汹涌潮流。1997年9月，该公司更名为realnetworks，相继发布了多款应用非常广泛的流媒体播放器realplayer系列，在其鼎盛时期，曾一度占据该领域超过85%的市场份额。
  
 
#### RA音频格式的特点

RA采用的是有损压缩技术，由于它的压缩比相当高，因此音质相对较差，但是文件也是最小的，因此在高压缩比条件下表现好，但若在中、低压缩比条件下时，表现却反而不及其他同类型档案格式了。此外RA可以随网络带宽的不同而改变声音质量，以使用户在得到流畅声音的前提下，尽可能高地提高声音质量。由于RA格式的这些特点，因此特别适合在网络传输速度较低的互联网上使用，互联网上许多的网络电台、音乐网站的歌曲试听都在使用这种音频格式。

### APE音频格式

#### APE音频格式介绍

APE是Monkey's Audio提供的一种无损压缩格式。在APE出现之前，音乐迷们都认为以CD或者WAV来保存自己喜欢的音乐素材是最好的方法了，但APE的出现，足以使他们改变这种看法，因为APE既可以保持音乐信号的无损，又可以以比WAV高得多的压缩率（接近2：1）压缩WAV文件，而且可以无须解压而直接播放。由于压缩后的APE文件只有原文件一半左右大小，APE格式受到了许多音乐爱好者的喜爱，特别是对于希望通过网络传输音频CD的朋友来说，APE可以帮助他们节约大量的资源。APE如此流行，在网上也比较容易能下载到APE格式的文件。

#### APE音频格式的特点

APE的本质，其实它是一种无损压缩音频格式。它是从庞大的WAV音频文件压缩而来，当然还是要比MP3格式的文件要大。庞大的WAV音频文件，可以通过Monkey''sAudio这个软件进行“瘦身”压缩为APE。很时候它被用做网络音频文件传输，因为被压缩后的APE文件容量要比WAV源文件小一半多，可以节约传输所用的时间。更重要的是，通过Monkey''s Audio解压缩还原以后得到的WAV文件可以做到与压缩前的源文件完全一致。所以APE被誉为“无损音频压缩格式”，Monkey''s Audio被誉为“无损音频压缩软件”。与采用WinZip或者WinRAR这类专业数据压缩软件来压缩音频文件不同，压缩之后的APE音频文件是可以直接被播放的。

相比于MP3、WMA等格式，APE可以用完胜来形容，APE的采样率最高可达1400kbps，接近于音乐CD格式的1411.2kbps，而我们日常使用的压缩格式的音乐，例如:MP3、WMA等，大多只是为128kbps。

### AAC音频格式

#### AAC音频格式介绍

AAC（Advanced Audio Coding），中文称为“高级音频编码”，出现于1997年，基于 MPEG-2的音频编码技术。由Fraunhofer IIS、杜比实验室、AT&T、Sony（索尼）等公司共同开发，目的是取代MP3格式。2000年，MPEG-4标准出现后，AAC 重新集成了其特性，加入了SBR技术和PS技术，为了区别于传统的 MPEG-2 AAC 又称为 MPEG-4 AAC。
 
AAC号称“最大能容纳48通道的音轨，采样率达96 KHz，并且在320Kbps的数据速率下能为5.1声道音乐节目提供相当于ITU-R广播的品质”。和MP3比起来，它的音质比较好，也能够节省大约30%的储存空间与带宽。

#### AAC音频格式的特点

1. 提升的压缩率：可以以更小的文件大小获得更高的音质，同样是128Kbps，AAC格式的音质明显好于MP3
2. 支持多声道：可提供最多48个全音域声道
3. 更高的解析度：最高支持96KHz的采样频率
4. 提升的解码效率：解码播放所占的资源更少
5. AAC属于有损压缩的格式，与时下流行的APE、FLAC等无损格式相比音质仍然存在“本质上”的差距。

总的来讲，AAC可以说是极为全面的编码方式，一方面，多声道和高采样率的特点使得它非常适合未来的DVD－Audio；另一方面，低码率下的高音质则使它也适合移动通讯、网络电话、在线广播等领域，真是全能的编码方式。

## 音频基础

了解一些音频的基础知识对于实际项目的开发尤为重要，因为我们要知道所以然。

### PCM

目前我们在计算机上进行音频播放都需要依赖于音频文件，音频文件的生成过程是将声音信息采样、量化和编码产生的数字信号的过程，人耳所能听到的声音，最低的频率是从20Hz起一直到最高频率20KHZ，因此音频文件格式的最大带宽是20KHZ。根据奈奎斯特的理论，只有采样频率高于声音信号最高频率的两倍时，才能把数字信号表示的声音还原成为原来的声音，所以音频文件的采样率一般在40~50KHZ，比如最常见的CD音质采样率44.1KHZ。

对声音进行采样、量化过程被称为脉冲编码调制（Pulse Code Modulation），简称PCM。PCM数据是最原始的音频数据完全无损，所以PCM数据虽然音质优秀但体积庞大，为了解决这个问题先后诞生了一系列的音频格式，这些音频格式运用不同的方法对音频数据进行压缩，其中有无损压缩（ALAC、APE、FLAC）和有损压缩（MP3、AAC、OGG、WMA）两种。

### MP3

目前最为常用的音频格式是MP3，MP3是一种有损压缩的音频格式，设计这种格式的目的就是为了大幅度的减小音频的数据量，它舍弃PCM音频数据中人类听觉不敏感的部分。

MP3格式中的数据通常由两部分组成，一部分为ID3用来存储歌名、演唱者、专辑、音轨数等信息，另一部分为音频数据。音频数据部分以帧(frame)为单位存储，每个音频都有自己的帧头，如图所示就是一个MP3文件帧结构图（图片同样来自互联网）。MP3中的每一个帧都有自己的帧头，其中存储了采样率等解码必须的信息，所以每一个帧都可以独立于文件存在和播放，这个特性加上高压缩比使得MP3文件成为了音频流播放的主流格式。帧头之后存储着音频数据，这些音频数据是若干个PCM数据帧经过压缩算法压缩得到的，对CBR的MP3数据来说每个帧中包含的PCM数据帧是固定的，而VBR是可变的。

![image](../../images/blog/ios_audio_intro/audio_01.jpg)


## IOS音频播放

### IOS音频播放概述

了解了基础概念之后我们就可以列出一个经典的音频播放流程（以MP3为例）：

1. 读取MP3文件
2. 解析采样率、码率、时长等信息，分离MP3中的音频帧
3. 对分离出来的音频帧解码得到PCM数据
4. 对PCM数据进行音效处理（均衡器、混响器等，非必须）
5. 把PCM数据解码成音频信号
6. 把音频信号交给硬件播放
7. 重复1-6步直到播放完成

在iOS系统中apple对上述的流程进行了封装并提供了不同层次的接口：

![image](../../images/blog/ios_audio_intro/audio_02.png)

下面对其中的中高层接口进行功能说明：

* Audio File Services：读写音频数据，可以完成播放流程中的第2步；
* Audio File Stream Services：对音频进行解码，可以完成播放流程中的第2步；
* Audio Converter services：音频数据转换，可以完成播放流程中的第3步；
* Audio Processing Graph Services：音效处理模块，可以完成播放流程中的第4步；
* Audio Unit Services：播放音频数据：可以完成播放流程中的第5步、第6步；
* Extended Audio File Services：Audio File Services和Audio Converter services的结合体；
* AVAudioPlayer/AVPlayer(AVFoundation)：高级接口，可以完成整个音频播放的过程（包括本地文件和网络流播放，第4步除外）；
* Audio Queue Services：高级接口，可以进行录音和播放，可以完成播放流程中的第3、5、6步；





## 参考

* [音频格式](https://hexdigi.jimdo.com/2011/08/14/wav-mp3-wma-ape-aac/)
* [音频文件格式](https://zh.wikipedia.org/wiki/%E9%9F%B3%E9%A2%91%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)
* [ios音频播放](http://msching.github.io/blog/2014/07/07/audio-in-ios/)