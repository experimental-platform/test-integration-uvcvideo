FROM experimentalplatform/ubuntu:latest

RUN apt-get -y update && apt-get -y install build-essential libjpeg-dev imagemagick subversion libv4l-dev checkinstall ffmpeg libav-tools && rm -rf /var/lib/apt/lists/*

RUN svn co svn://svn.code.sf.net/p/mjpg-streamer/code/ mjpg-streamer

WORKDIR mjpg-streamer/mjpg-streamer

RUN make USE_LIBV4L2=true 
RUN make install
ENV LD_LIBRARY_PATH /usr/local/lib


CMD [ "/bin/bash" ]

# export LD_LIBRARY_PATH=/usr/local/lib
# mjpg_streamer -i "input_uvc.so -d /dev/video0" -o "output_http.so -p 8080 -w /usr/local/www" 

## ffmpeg -f v4l2 -input_format mjpeg -i /dev/video0  -v 0 -vcodec mpeg4 -f mpegts  udp:172.16.10.112:1234
