# ffmpeg explore

author: martin.cheng

## 1 ffmpeg document

* [ffmpeg Documentation](https://ffmpeg.org/ffmpeg.html)
* [FFmpeg Formats Documentation](https://ffmpeg.org/ffmpeg-formats.html)
* [FFmpeg Codecs Documentation](https://ffmpeg.org/ffmpeg-codecs.html)
* [FFmpeg Filters Documentation](https://ffmpeg.org/ffmpeg-filters.html)
* [FFmpeg Devices Documentation](https://ffmpeg.org/ffmpeg-devices.html)

## 2 ffmpeg fast explore

### 2.1 build config of ffmpeg

```shell
$ ffmpeg -h | grep show
-formats            show available formats
-muxers             show available muxers
-demuxers           show available demuxers
-devices            show available devices
-codecs             show available codecs
-decoders           show available decoders
-encoders           show available encoders
-bsfs               show available bit stream filters
-protocols          show available protocols
-filters            show available filters
-pix_fmts           show available pixel formats
-layouts            show standard channel layouts
-sample_fmts        show available audio sample formats
-colors             show available color names
-hwaccels           show available HW acceleration methods
```

### 2.2 config parameters of ffmpeg

```shell
./configure --list-
--list-bsfs       --list-demuxers   --list-filters    --list-indevs     --list-outdevs    --list-protocols 
--list-decoders   --list-encoders   --list-hwaccels   --list-muxers     --list-parsers
```
