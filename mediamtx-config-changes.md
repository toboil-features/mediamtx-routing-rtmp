# Approach 1
```yaml
  runOnReadyRestart: yes
  runOnReady: >
    ffmpeg -i srt://localhost:8890?streamid=read:mystream    # Default MediaMTX SRT stream
    -c copy    # Use the same stream and pipe it through
    -rtmp_enhanced_codecs ac-3,av01,avc1,ec-3,fLaC,hvc1,.mp3,mp4a,Opus,vp09
    -f flv rtmp://localhost:21001/live/abc123    # Owncast default localhost link, but with with custom port.
#   One-link only, so you are supposed to ebmed TOKEN into it by yourself.
```
