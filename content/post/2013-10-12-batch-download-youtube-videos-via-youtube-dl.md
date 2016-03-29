+++
title = "Batch download YouTube videos via youtube-dl"
date = "2013-10-12T07:39:00+08:00"
+++

Recently I spent lots of time watching lecture videos on YouTube. But in China,
I have to VPN tunneling back to Taiwan, and the network condition is usually
not so good such that the streams are always buffering... So it's a good idea
to write a script that can batch download all the lecture videos, and then put
them into my iPad thus I can watch everywhere and watch later.  Thanks
[youtube-dl](https://github.com/rg3/youtube-dl/ "youtube-dl"). We can download
YouTube videos simply in a command line environment, and it also supports
subtitles! Awesome. So now I just have to:

- Prepare an input file contains urls of videos and its title
- Write down a script feed each url of the above file to youtube-dl

Okay, I use **Python + Mechanize + BeautifulSoup4** to produce this file:
```
http://www.youtube.com/watch?v=*********** => [02.05.01] Lecture 2.5.1
http://www.youtube.com/watch?v=*********** => [02.05.02] Lecture 2.5.2
http://www.youtube.com/watch?v=*********** => [02.06.01] Lecture 2.6.1
http://www.youtube.com/watch?v=*********** => [02.06.02] Lecture 2.6.2
http://www.youtube.com/watch?v=*********** => [02.07.01] Lecture 2.7.1
http://www.youtube.com/watch?v=*********** => [02.08.01] Lecture 2.8.1
http://www.youtube.com/watch?v=*********** => [02.08.02] Lecture 2.8.2
```
The first part of this file is obviously the url of video, and the second part
is the video's title which we gonna use as the downloaded filename.

Then the script is quite simple like this:
```bash
#!/bin/bash

trap "echo Cancelled by user.; exit 1;" SIGINT SIGTERM

while read line
do
  url=`echo $line | awk '{print $1}'`
  title=`echo $line | awk 'BEGIN {FS=" => "}; {print $2}'`
  echo "Downloading: $title "
  youtube-dl -o "$title.%(ext)s" --write-sub --sub-lang en --write-auto-sub $url
done < $1

echo "Done!";
```
Note that some videos will not have subtitle, but thanks Google's ASR, there
will be a *automatic caption*, so let's add a **--write-auto-sub** option.
