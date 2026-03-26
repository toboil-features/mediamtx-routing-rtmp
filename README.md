# mediamtx-routing-rtmp

Owncast RTMP Port: 21001
Owncast Admin: http://localhost:8080/admin (admin : admin)
Owncast main page: http://localhost:8080

MediaMTX BROwser stream (to read): http://localhost:8889/mystream/
MediaMTX direct WebRTC read: http://localhost:8889/mystream/whep

MediaMTX streaming:
http://localhost:8889/mystream/whip

Owncast streaming:
rtmp://localhost:21001/live
abc123
rtmp://localhost:21001/live/abc123

# Test stream (Broadcast Box FFmpeg)
ffmpeg \
  -re \
  -f lavfi -i testsrc=size=1280x720 \
  -f lavfi -i sine=frequency=440 \
  -pix_fmt yuv420p -vcodec libx264 -profile:v baseline -r 25 -g 50 \
  -acodec libopus -ar 48000 -ac 2 \
  -f whip -authorization "ffmpeg-test" \
  "https://b.siobud.com/api/whip"

Broadcast Box demo live:
https://b.siobud.com/api/whip
tryyyyout

Works (Owncast):
ffmpeg -i srt://localhost:8890?streamid=read:mystream -c:v h264_nvenc -c:a aac -f flv "rtmp://localhost:21001/live/abc123"

Works (Broadcast Box):
ffmpeg \
  -re \
  -f lavfi -i testsrc=size=1280x720 \
  -f lavfi -i sine=frequency=440 \
  -pix_fmt yuv420p -vcodec libx264 -profile:v baseline -r 25 -g 50 \
  -acodec libopus -ar 48000 -ac 2 \
  -f whip -authorization "ffmpeg-test" \
  "https://b.siobud.com/api/whip"


ffmpeg \
  -re \
  -i http://localhost:8889/mystream/whep \
  -c copy \
  -f whip -authorization "ffmpeg-test" \
  "https://b.siobud.com/api/whip"

# Owncast internals of passthrough
core/transcoders/codecs.go

/usr/bin/ffmpeg -hide_banner -loglevel warning -hwaccel cuda -fflags +genpts -flags +cgop -i pipe:0 -map v:0 -c:v:0 copy -map "a:0?" -c:a:0 copy -preset p1 -var_stream_map v:0,a:0 -f hls -hls_time 1 -hls_list_size 25 -hls_flags program_date_time+independent_segments+omit_endlist -segment_format_options "mpegts_flags=mpegts_copyts=1" -pix_fmt yuv420p -sc_threshold 0 -master_pl_name stream.m3u8 -hls_segment_filename "http://127.0.0.1:45263/%v/stream-2cpJrOcvg-%d.ts" -max_muxing_queue_size 400 -method PUT "http://127.0.0.1:45263/%v/stream.m3u8"


gst-launch-1.0 -v \
  /dev/video0 \
  ! videoconvert \
  ! x264enc tune=zerolatency \
  ! rtph264pay \
  ! "application/x-rtp,media=video,encoding-name=H264,payload=97,clock-rate=90000" \
  ! whip0.sink_0 \
  $audioelem \
  ! audioconvert \
  ! opusenc \
  ! rtpopuspay \
  ! "application/x-rtp,media=audio,encoding-name=OPUS,payload=96,clock-rate=48000,encoding-params=(string)2" \
  ! whip0.sink_1 \
  whipsink \
    name=whip0 \
    use-link-headers=true \
    whip-endpoint="$whip_endpoint" \
    auth-token="$auth_token"
