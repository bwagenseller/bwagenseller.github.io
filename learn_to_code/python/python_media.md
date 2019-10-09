# Python Media

!> This assumes yo uare working in Ubuntu. That said, most of this will work for Mac and Windows as well (although the various installs will be different, as will the directory structure).

These libraries are designed to help you interact with media (audio, video, etc) in Python.

# pytube

> I initially read about pytube on [stackoverflow](https://stackoverflow.com/questions/40713268/download-youtube-video-using-python-to-a-certain-directory).

!> Currently (September 2019) pytube is broken - use [youtube_dl](learn_to_code/python/python_media?id=youtube_dl) instead.

`pytube` is a Python library that allows you to interact with / download files from YouTube.

## pytube Installation

To install pytube, [become root](operating_systems/ubuntu/linux_notes?id=becoming-root) and then run:
```
pip install pytube
```

## Basic pytube Example

> Initially found on [stackoverflow](https://stackoverflow.com/questions/40713268/download-youtube-video-using-python-to-a-certain-directory).

This example downloads the specified YouTube video.

```
import pytube

if __name__ == '__main__':
		
	pytube.YouTube("https://www.youtube.com/watch?v=SOMEADDRESS").streams.first().download("/home/user/Downloads")
```
* "https://www.youtube.com/watch?v=SOMEADDRESS" is the address of the video you wish to download.
* "/home/user/Downloads" is the target folder where it will the file will be stored.
 * For Windows, use double backslashes in the path (i.e. 'C:\\home\\Downloads')


# youtube_dl

> I initially read about youtube_dl on [stackoverflow](https://stackoverflow.com/questions/40713268/download-youtube-video-using-python-to-a-certain-directory).

`youtube_dl` is a Python library that allows you to interact with / download files from YouTube.

## youtube_dl Installation

To install youtube_dl, [become root](operating_systems/ubuntu/linux_notes?id=becoming-root) and then run:
```
pip install --upgrade youtube-dl
```

## Basic youtube_dl Example

> Initially found on [stackoverflow](https://stackoverflow.com/questions/40713268/download-youtube-video-using-python-to-a-certain-directory).

This example downloads the specified YouTube video.

```
import youtube_dl

if __name__ == '__main__':
		
	ydl_opts = {}
	with youtube_dl.YoutubeDL(ydl_opts) as ydl:
		ydl.download(["https://www.youtube.com/watch?v=SOMEADDRESS"])

```
* "https://www.youtube.com/watch?v=SOMEADDRESS" is the address of the video you wish to download.
 * This is actually a list of addresses, and as such, you can send a list of YouTube videos.
* It seems this simply saves the files to the current directory.
 
