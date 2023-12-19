---
date: 2023-12-19
---

# Convert an audiobook to a podcast, to listen with AntennaPod

I listen to podcasts a lot, and I wanted to get into listening to audiobooks. However, I wanted to really buy them as un-DRM'd audio files, but still be able to listen to them via an app on my phone, without having to copy the whole several hundred megabytes onto it. 
So I thought it might be a good idea to use AntennaPod, a podcast listening app for Android, which can nicely stream podcasts and also manage offline availability.
To that end, I needed to dress up the audiobook (a folder of audio files) as a podcast (an RSS file available at a URL, linking to the audio files available at a URL).
I already have my audiobooks at my NAS, which also serves them via HTTP locally, so I only needed to create the appropriate RSS file. I took a [m3u to rss script from github](https://github.com/ildar/m3u2rss/blob/master/m3u2rss.sh) and modified it to my needs.

First, in my case I needed to convert the audiobook from FLAC to MP3 format, as AntennaPod couldn't seek in the FLACs:

```shell
find . -name \*.flac -exec ffmpeg -i '{}' '{}.mp3' ';'
```

Then, I created a simple M3U playlist file of all MP3s:

```shell
ls -1 *.mp3 > my-audiobook.m3u
```

Make sure to check that the chapters are in order.

Then I created the podcast RSS file from the M3U file using a [modified version of the script](https://github.com/luelista/m3u2rss/blob/master/m3u2rss.sh), which supports creating the file locally and providing a URL prefix, at which they will be available later on (AntennaPod required absolute URLs in the RSS file):

```shell
sh m3u2rss.sh my-audiobook.m3u "http://my-nas.local/audiobooks/my-audiobook/"
```

Finally, I uploaded the resulting my-audiobook.rss to be available at http://my-nas.local/audiobooks/my-audiobook/my-audiobook.rss, and added that
as a podcast into AntennaPod (Menu -> Add Podcast -> Advanced: Add Podcast by RSS address).

Now I can listen to the audiobook at home or via my VPN without copying it to the phone, and temporarily download episodes to listen to while not connected to the home network.

Warning: make sure the files are not publicly available on the internet if it's a restrictively licensed audiobook (as most are).


