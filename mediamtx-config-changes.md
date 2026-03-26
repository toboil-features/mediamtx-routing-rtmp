# Approach 1
Redirect to Owncast.
```yaml
  runOnReadyRestart: yes
  runOnReady: >
    ffmpeg -i srt://localhost:8890?streamid=read:mystream
    -c copy
    -rtmp_enhanced_codecs ac-3,av01,avc1,ec-3,fLaC,hvc1,.mp3,mp4a,Opus,vp09
    -f flv rtmp://localhost:21001/live/abc123
```
Owncast default localhost link, but with with custom port, since MediaMTX demands 1935 port too.
One URL streaming only, so you are supposed to embed TOKEN into URL by yourself, else restreaming is unavailable?
That's how Owncast handles it: https://owncast.online/docs/broadcasting/
