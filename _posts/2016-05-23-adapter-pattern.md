---
layout: post
title: "设计模式08_适配器模式（Adapter Pattern）"
date: 2016-05-23 20:07:07 +0800
categories: DesignPattern
tags: 结构型模式 适配器模式
---
* content
{:toc} 

# 描述

 作为两个不兼容的接口之间的桥梁。

# 简介

## 意图

  将一个类的接口转换成客户希望的另外一个接口。

## 主要解决

  主要解决在软件系统中，常常要将一些"现存的对象"放到新的环境中，而新环境要求的接口是现对象不能满足的。










## 何时使用

  接口不兼容。

## 解决方案

  继承或依赖（推荐）。

## 关键代码

  适配器继承或依赖已有的对象，实现想要的目标接口。

## 应用实例

- 美国电器 110V，中国 220V，就要有一个适配器将 110V 转化为 220V。

- JAVA JDK 1.1 提供了 Enumeration 接口，而在 1.2 中提供了 Iterator 接口，想要使用 1.2 的 JDK，则要将以前系统的 Enumeration 接口转化为 Iterator 接口，这时就需要适配器模式。

- 在 LINUX 上运行 WINDOWS 程序。

- JAVA 中的 jdbc。

## 使用场景

  有动机地修改一个正常运行的系统的接口，这时应该考虑使用适配器模式。

## 注意事项

  适配器不是在详细设计时添加的，而是解决正在服役的项目的问题。

## 特点

### 优点

- 可以让没有关联的类一起运行。

- 提高类的复用。

-  增加类的透明。

-  灵活性好。

### 缺点

  过多地使用适配器，会让系统非常零乱，不易整体进行把握。比如，明明看到调用的是 A 接口，其实内部被适配成了 B 接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。

# UML

 ![适配器模式](http://7xucao.com1.z0.glb.clouddn.com/adapter_pattern_uml_diagram.jpg)

- MediaPlayer接口和实现MediaPlayer接口实体类AudioPlayer。AudioPlayer可以播放MP3格式。           
- AdvancedMediaPlayer接口和实现AdvancedMediaPlayer接口的实体类。AdvancedMediaPlayer可以播放MP4和
- 目标：实现AudioPlayer播放其他格式的音频文件。                                                 
- 创建实现MediaPlayer接口的适配器类MediaAdapter，使用AdvancedMediaPlayer对象播放所需格式。      
- AudioPlayer使用适配器MediaAdapter传递所需的音频类型，不需要知道播放所需格式音频的实际类。     


## 实现


//步骤1 为媒体播放器和更高级的媒体播放器创建接口。

```cpp
class MediaPlayer
{
public:
	virtual	void play(string audioType, string fileName) = 0;
};

class AdvancedMediaPlayer
{
public:
	virtual void playVlc(string fileName) = 0;
	virtual void playMp4(string fileName) = 0;
};
```

//步骤2 创建实现了 AdvancedMediaPlayer 接口的实体类。

```cpp
class VlcPlayer :public AdvancedMediaPlayer
{

public:
	void playVlc(string fileName)
	{
		cout << "Playing vlc file. Name: " << fileName << endl;
	}
	void playMp4(string fileName)
	{
		//do nothing
	}
};

class Mp4Player :public AdvancedMediaPlayer
{
public:
	void playVlc(string fileName)
	{
		//do nothing
	}

	void playMp4(string fileName)
	{
		cout << "Playing Mp4 file. Name: " << fileName << endl;
	}
};
```

//步骤3 创建实现了 MediaPlayer 接口的适配器类。

```cpp
class MediaAdapter :public MediaPlayer
{
public:
	MediaAdapter(string audioType)
	{
		if (audioType == "vlc")
		{
			advanceMusicPlayer = new VlcPlayer();
		}
		else if (audioType == "mp4")
		{
			advanceMusicPlayer = new Mp4Player();
		}
	}

	void play(string audioType, string fileName)
	{
		if (audioType == "vlc")
		{
			advanceMusicPlayer->playVlc(fileName);
		}
		else if (audioType == "mp4")
		{
			advanceMusicPlayer->playMp4(fileName);
		}
	}
private:
	AdvancedMediaPlayer *advanceMusicPlayer;
};
```

//步骤4 创建实现了 MediaPlayer 接口的实体类。

```cpp
class AudioPlayer :public MediaPlayer
{
public:
	void play(string audioType, string fileName)
	{
		//播放 mp3 音乐文件的内置支持
		if (audioType == "mp3")
		{
			cout << "Playing mp3 file. Name: " + fileName << endl;
		}
		//mediaAdapter 提供了播放其他文件格式的支持
		else if (audioType == "vlc"||audioType=="mp4")
		{
			mediaAdapter = new MediaAdapter(audioType);
			mediaAdapter->play(audioType, fileName);
		}
		else
		{
			cout << "Invalid media. " + audioType + " format not supported" << endl;
		}
	}
private:
	MediaAdapter *mediaAdapter;
};
```


//步骤5 使用 AudioPlayer 来播放不同类型的音频格式。

```cpp
int main()
{ 
	AudioPlayer *audioPlayer = new AudioPlayer();
	audioPlayer->play("mp3", "beyond the horizon.mp3");
	audioPlayer->play("mp4", "alone.mp4");
	audioPlayer->play("vlc", "far far away.vlc");
	audioPlayer->play("avi", "mind me.avi");
	system("pause");
	return 0;
}
```

