---
layout: post
title: Setup a Tracker server and a seeding client in minutes
date: 2017-11-04T00:00:00.000Z
excerpt: Minimum code, maximum torrent love.
post: true
project: true
tag:
  - Programming
  - Python
  - BitTorrent
  - UDP
  - Trackers
  - Asyncio
comments: true
---

# Setup a Tracker server and a seeding client in minutes

Ever thought of making your own torrent using open source tools? Well here is one way to do it very little lines of code. We are going to take a look at how we can host our own torrent eco-system that essentially makes a torrent file, for your files, seed it through your own tracker server, private?

Okay, so what you will be doing  
1. You will be setting up a Tracker server. We are gonna setup a UDP Tracker
2. You will write a piece of script to make the torrent file for your files, using python ofcourse.
3. You will see how to seed your torrent, get it downloaded else where, just like a normal torrent

Most torrents that we use from internet are public and have a lot of trackers and is not something we might be willing to use, if we need to be private, or say you want to share your family moments and 4K videos and High Resolution photos (lots of 'em). Yes you could use private trackers, but let's not jump into it.

For the sake of an example, we are gonna assume a situation where a big family is spread across the corners of the world (I know, this is me being sarcastic)
Let's see how we can setup the connecting link between seeders and leachers. For this, we use Tracker(s). We will setup a UDP Tracker Server that essentially bridges connections from Leachers (say your cousins in USA) and the seeders(you, yes you! you could be anyone, dad? brother? mom?)

To do that, we are gonna take help from an awesome package from PyPi, [Pybtracker](https://pypi.python.org/pypi/pybtracker)
The documentation in the package itself is self explanatory, but if you might have to take a look at this [issue](https://github.com/elektito/pybtracker/issues/3) I raised while I used the package and a possible [solution](https://github.com/elektito/pybtracker/pull/4) to it.

So here is your `server.py`  :  

```py

from pybtracker import TrackerServer
import asyncio
import logging

logger = logging.getLogger('udp_tracker_server')
logger.setLevel(logging.DEBUG)
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
ch.setFormatter(formatter)
logger.addHandler(ch)



loop = asyncio.get_event_loop()
logger.info(":: Setting Loop...")
tracker = TrackerServer(local_addr=('0.0.0.0', 2255), loop=loop)
logger.info(":: Setting up tracker at 0.0.0.0:2255 ...")
asyncio.ensure_future(tracker.start())
try:
	logger.info(":: Server started ... 'udp://your.server.ip.or.domain:2255'")
	loop.run_forever()
except KeyboardInterrupt:
	logger.exception(":: Server stopped! ...")
	loop.run_until_complete(tracker.stop())
except Exception as e:
	logger.exception(e)
	# raise e

```

Run this as a service under NGINX or Apache Server, you are done with the tracker server. Now all you need to do is use this for making your torrents!

Let's see how we can now make torrent for your files.
Say, you have a folder, `Trip to Miami` and this is some dope videos shot in 4K, and that you wish to share with your cousins (leachers). You need to make a `.torrent` file that will essentially allow the bittorrent client to download the actual file(s).

I again have to mention another awesome package from PyPi that let's you make torrents like a piece of cake. [DotTorrent](http://dottorrent.readthedocs.io/), and this one comes with a excellent documentation. Nevertheless, I will share the code here.

```py

from dottorrent import Torrent
import os
import logging

logger = logging.getLogger('make_torrent')
logger.setLevel(logging.DEBUG)
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
ch.setFormatter(formatter)
logger.addHandler(ch)

def make_torrent(location, comment):
	try:
		t = Torrent(
			location,
			trackers = ['udp://your.server.ip.or.domain:2255'],
			private = True,
			comment = comment,
			include_md5 = True
		)

		logger.info("Generating torrent for location : %s ... " %(location))
		t.generate()

		filename = location.split('/')[-1] + '.torrent'
		path = os.path.join(os.getcwd(),filename)
		logger.info("Torrent file be saved in path : %s" %(path))

		with open(path, 'wb') as fp:
			t.save(fp)
			logger.info("Torrent saved! Enjoy seeding ...")
		return True

	except FileNotFoundError:
		logger.error("File(s) not found")
		return False
	except Exception as e:
		raise e

```

call the function in whichever script you wish, or just save the location and commment in the code itself, and run the script.

`make_torrent("/home/theCrazyDad/Videos/Trip to Miami","Miami Trip shared by theCrazyDad")`

Lastly, you need to seed this torrent, which is as simple as opening the torrent file in your favorite torrent client like Deluge or qBittorrent, do keep in mind to tell the download location as the same location you gave while making torrent file, so that your client will automatically start seeding the file as soon as you start the torrent.

Now, just share the torrent file with your family, ask them to add it to their torrent client, and that is there is to it. They should get the file, no matter what size or number you have to share.

For those, who are hosting the torrent creation as a service, do keep in mind to seed from that server where you have the files initially. ;)