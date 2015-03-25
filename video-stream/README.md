#### Raspberry Pi video stream

* Linux raspberrypi-1G 3.18.9-v7+ #772
* raspivid Camera App v1.3.12
* raspistill Camera App v1.3.8
* MJPG Streamer Version 2.0
* motion Version 3.2.12

##### Required packages

* libjpeg8-dev
* imagemagick
* MJPEG-streamer
* libv4l-dev
* motion
* vlc
* mplayer

========
#### VLC
Server side
~~~
$ raspivid -o - -t 0 -hf -w 800 -h 400 -fps 24 |cvlc -vvv stream:///dev/stdin --sout '#standard{access=http,mux=ts,dst=:8001}' :demux=h264
~~~
VLC client
~~~
http://<SERVER_IP_ADDRESS>:8001
~~~
=====================
#### netcat
Client side
~~~
$ nc -l -p 5001 | mplayer -fps 31 -cache 1024 -
~~~
Server side
~~~
$ raspivid -t 99999 -o - | nc <CLIENT_IP_ADDRESS> 5001
~~~
================================
#### MJPEG-streamer
~~~
$ tar xvzf mjpg-streamer-r63.tar.gz
$ cd mjpg-streamer-r63
$ make
~~~
Copy ``mjpg_streamer, input_*.so`` and ``output_*.so`` to ``/usr/local/bin`` directory:
~~~
$ cp mjpeg-streamer /usr/local/bin/
$ find . -type f -name "input_*.so" | xargs cp -t /usr/local/lib/
$ find . -type f -name "output_*.so" | xargs cp -t /usr/local/lib/
~~~
~~~
$ cp -R www /usr/local/www
~~~
Create ``LD_LIBRARY_PATH`` environment variable (add to ``.bashrc`` or ``.profile`` to set environment variable permanently):
~~~
$ export LD_LIBRARY_PATH=/usr/local/bin/
~~~
Enable camera device ``/dev/video0`` (add bcm2835-v4l2 to ``/etc/modules`` to make it run on every boot):
~~~
$ sudo modprobe bcm2835-v4l2
~~~
Create a symbolic link of header:
~~~
$ ln -s /usr/include/linux/videodev2.h /usr/include/linux/videodev.h
~~~
Start capture:
~~~
$ mjpg_streamer -i "/usr/local/lib/input_uvc.so -y" -o "/usr/local/lib/output_http.so -w /usr/local/www"
~~~

Some ``mjpg_streamer`` options:
* ``-i``: uses input_uvc.so as input
* ``-y``: enables YUYV format
* ``-i``: output_http.so as output
* ``-w``: directory for HTML files
* ``-f``: frames per second
* ``-r``: image size (W x H)
* ``-b``: run in background

Client browser:
~~~
http://<SERVER_IP_ADDRESS>:8080
~~~

=======================
#### motion
Edit ``etc/motion/motion.conf``:
~~~
...
# Accept external connections
[Line 413] webcam_localhost off
...
~~~
Start capture:
~~~
$ motion
~~~
Client browser:
~~~
http://<SERVER_IP_ADDRESS>:8081
~~~
