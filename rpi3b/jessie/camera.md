# IP-camera / RTSP stream

## Prerequisites

- Raspbian Jessie
- Internet connection
- Camera mod, connected
- Open terminal on your rPi through SSH connection or a desktop GUI

## Setup

Initiate super user, get the latest update and install VLC Player.

```
sudo su
sudo apt-get update
sudo apt-get install vlc
```

Note: sometimes it may not update without SU access.

## Streaming

Initiate the stream through your terminal:

```
raspivid -o - -t 0 -n -w 600 -h 400 -fps 12 | cvlc -vvv stream:///dev/stdin --sout '#rtp{sdp=rtsp://:8554/}' :demux=h264
```

### Dissection

```raspivid``` command to capture the video

```-o -``` causes the output to be written to stdout

```-t 0``` sets the timeout to disabled

```-n``` stops the video being previewed (remove if you want to see the video on the HDMI output)

```-w``` frame width, has range between 64 to 1920 | optional

```-h``` frame height, has range between 64 to 1080 | optional

```-fps``` frame rate, has range between 2 to 30 | optional

```cvlc``` console vlc player

```-vvv``` and its argument specifies where to get the stream from

```-sout``` and its argument specifies where to output goes to

## Viewing

### Prerequisites

Video player with RTSP support (Quicktime, VLC, etc...)

### Connection

Check your IP on the rPi via another terminal

```
ifconfig
```

Open a network stream using your chosen player, where ###.###.###.### should be the IP address of the rPi

```
rtsp://###.###.###.###:8554/
```

## Other

- Make sure your camera is calibrated if you want a clear picture
- Expect latency issues

### Notes and Ideas

- Try with -v instead of -vvv
- Experiment with ports
- View multiple streams at the same time
- Standby mode until 1+ connections