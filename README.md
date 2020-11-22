## WebRTC to UDP to RTSP

This repo is a fork from [Centricular
gstwebrtc](https://github.com/centricular/gstwebrtc-demos) with the
goal of converting noisy WebRTC acquired video streams into clean RTSP
streams.  To accomplish this, the following mods were made:

* Added threaded code to send key frame to sender periodically to
  handle video corruption due to network packet loss

* Redirect video buffers to UDP sink for RTSP packet conversion

#### Dependencies

* rtsp-simple-server from [RTSP Simple Server](https://github.com/aler9/rtsp-simple-server) .)

* gstreamer 1.14+ and all the usual plugins and libs

		sudo apt-get install -y gstreamer1.0-tools gstreamer1.0-nice gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-plugins-good libgstreamer1.0-dev git libglib2.0-dev libgstreamer-plugins-bad1.0-dev libsoup2.4-dev libjson-glib-dev

#### Compile

Navigate to this directory repo containing Makefile and webrtc_to_udp.c file and run

	./make

### Running Media Stack

* Pull the video from remote web browser by navigating to https://webrtc.nirbheek.in 
at remote peer endpoint

* From the other endpoint run the command below (swapping XXXX for
  supplied peer-id on browser page at remote peer endpoint):

		./webrtc_to_udp --peer-id=XXXX
		
* Enable camera and microphone when prompted by browser (tested in Chrome and Firefox).
	
* Start RTSP server in background.  (Here, we use rtsp-simple-server from [RTSP Simple Server](https://github.com/aler9/rtsp-simple-server) .)

		./rtsp-simple-server &
	
* Relay udp to rtsp
	
		gst-launch-1.0 udpsrc port=9500 ! "application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264, payload=(int)96" ! rtph264depay ! rtspclientsink location=rtsp://localhost:8554/mystream

* Display video locally (to visually verify packets are correct)

		gst-launch-1.0 rtspsrc location=rtsp://localhost:8554/mystream latency=200 ! decodebin ! queue ! videoconvert ! autovideosink sync=false



### References

* [Centricular gstwebrtc](https://github.com/centricular/gstwebrtc-demos)
* [RTSP Simple Server](https://github.com/aler9/rtsp-simple-server) 
