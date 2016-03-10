---
categories:
- Twitch
- VLC
- OSX
- streaming
- Homebrew
- livestreamer
- rtmpdump
comments: true
date: 2013-12-07T00:20:42Z
title: Watch Twitch using VLC in OS X
url: /2013-12-07-watch-twitch-using-vlc-in-osx/
---

If you own a Retina-Macbook you problably struggle with fairly high CPU load and bad performance when watching Twitch-channels using the standard flash-based player in the browser.

A tool called `livestreamer` can be used to bring Twitch streams to the beloved VLC player (which also uses the GPU to process videos). This way you are not just able to reduce the used resources, the streams also feel much smoother, especially for high resolutions.

*Twitch will not be able to stream ads if you use this solution.* 
*Please be fair and subscribe to channels you like and you support.*

**[Update 2014-02-20]**

If you are using the glorious [Alfred App](http://www.alfredapp.com) you might be interested in this workflow, which allows for the same thing: [http://www.packal.org/workflow/twitchstreamer](http://www.packal.org/workflow/twitchstreamer).

**[Update 2014-03-06]**

Added updating instructions.

Install Livestreamer
--------------------

- Download and install `rtmpdump` from [here](http://trick77.com/wp-content/uploads/2008/01/rtmpdump-2.4_mac_os.zip)
- Download `python-setuptools` from [here](https://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11-py2.7.egg#md5=fe1f997bc722265116870bc7919059ea)
- Open `Terminal.app`
- Navigate to the folder where you downloaded the Egg-File and install it
{{< highlight bash >}}
cd ~/Downloads
sh setuptools-0.6c11-py2.7.egg
# Maybe you need to run it as sudo - `sudo sh setuptools-0.6c11-py2.7.egg`
{% endcodeblock %}
- Clone `Livestreamer` GIT Repository and install `Livestreamer`
{% codeblock lang:bash %}
git clone git://github.com/chrippa/livestreamer.git
cd livestreamer
python setup.py install
# Again, maybe you need to run it as sudo - `sudo python setup.py install`
{{< /highlight >}}

Now you should be able to view Twitch channels in VLC

Using Livestreamer
------------------

Say you want to view this Twitch channel in VLC: [http://www.twitch.tv/wcs_europe](http://www.twitch.tv/wcs_europe).
All you have to to is go into the `Terminal.app` and type
{{< highlight bash >}}
livestreamer http://www.twitch.tv/wcs_europe [quality]
# You might as well skip the `http://www.` part
{{< /highlight >}}
Here, `[quality]` has to be a quality setting from the stream, usually ranging between `low`, `medium`, `high` and `source`. If you leave it empty, `livestreamer` will tell you, which options you can choose from. Setting the parameter to `best` tells `livestreamer` to use the highest quality available.

Et voilá. Enjoy your stream.
{{< figure src="/blog/media/2013-12-07-watch-twitch-using-vlc-in-osx/twitch-vlc-sc2_small.png" >}}

Update Livestreamer
-------------------

If you see a message like this when launching `livestreamer` you might update to the latest version:
{{< highlight bash >}}
[cli][info] A new version of Livestreamer (1.7.4) is available!
{{< /highlight >}}

To do so, navigate to any folder, for example `~/Downloads`, and run the following commands:
{{< highlight bash >}}
cd ~/Downloads
git clone git://github.com/chrippa/livestreamer.git
cd livestreamer
python setup.py install
{{< /highlight >}}

After that you can remove the livestreamer folder from the folder you cloned the GIT repository to (`~/Downloads/livestreamer` in our example).

References
==========
- Links and instructions how to install everything is taken from different Posts in this [Gamespot Thread](http://forum.gamesports.net/dota/showthread.php?45027-How-to-watch-Twitch-TV-in-VLC-player-(MAC-OSX-HOW-TO)
- For further information, visit [this site from the livestreamer developer](http://livestreamer.tanuki.se/en/latest/), which offers more detailed instructions