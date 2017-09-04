# VLC builder
A practical guide to building VLC packages for Debian 8 (Jessie).

## Compile VLC
This repository has been created to explain how to compile VLC without APT-get dependencies. Default VLC package have many dependencies like X11, with compiled VLC you can enable or disable packages.

It is recommended to compile VLC in a __clean__ Debian 8 installation, because VLC's build scripts check whether any .deb packages exist. If one of them does exist VLC will link to it as a dependency. If the command 'APT-get update' is run it is possible that one of these dependencies is updated and VLC will not work anymore. To ensure that this can not happen, compile VLC in a __clean__ Debian 8 installation so that VLC will download these packages and compile them within VLC itself.

### Example
```bash
apt-get update
apt-get install wget git libtool build-essential pkg-config autoconf autopoint yasm gettext checkinstall cmake
wget http://ftp.nl.debian.org/debian/pool/main/a/automake-1.15/automake_1.15-6_all.deb
dpkg -i automake_1.15-6_all.deb
wget http://www.cmake.org/files/v3.2/cmake-3.2.2.tar.gz
tar xf cmake-3.2.2.tar.gz
cd cmake-3.2.2
./configure
make
checkinstall
cd ~
git clone https://github.com/videolan/vlc.git
cd vlc
./bootstrap
cd contrib
mkdir native
cd native
../bootstrap --enable-shout --enable-dvbpsi --enable-twolame --enable-libmpeg2 --enable-png --enable-x264 --enable-ffmpeg --enable-mad --enable-libarchive --enable-samplerate --enable-libxml2 --enable-gcrypt --enable-gnutls --disable-a52 --disable-aom --disable-aribb24 --disable-aribb25 --disable-asdcplib --disable-ass --disable-bghudappkit --disable-live555 --disable-bluray --disable-bpg --disable-caca --disable-cddb --disable-chromaprint --disable-crystalhd --disable-d3d11 --disable-d3d9 --disable-daala --disable-dca --disable-directx --disable-dshow --disable-dvdcss --disable-dvdnav --disable-dvdread --disable-ebml --disable-faad2 --disable-ffi --disable-flac --disable-fluid --disable-fluidlite --disable-fontconfig --disable-freetype2 --disable-fribidi --disable-gettext --disable-glew --disable-glib --disable-gme --disable-gmp --disable-goom --disable-gpg-error --disable-growl --disable-gsm --disable-harfbuzz --disable-iconv --disable-jack --disable-jpeg --disable-kate --disable-lame --disable-libdsm --disable-libtasn1 --disable-lua --disable-luac --disable-matroska --disable-mfx --disable-microdns --disable-modplug --disable-mpcdec --disable-mpg123 --disable-ncurses --disable-nettle --disable-nfs --disable-ogg --disable-openjpeg --disable-opus --disable-orc --disable-postproc --disable-projectM --disable-protobuf --disable-pthreads --disable-qt --disable-regex --disable-schroedinger --disable-sdl --disable-SDL_image --disable-sidplay2 --disable-soxr --disable-sparkle --disable-speex --disable-speexdsp --disable-sqlite --disable-ssh2 --disable-taglib --disable-theora --disable-tiff --disable-tiger --disable-tremor --disable-upnp --disable-vncclient --disable-vorbis --disable-vorbisenc --disable-vpx --disable-x265 --disable-xau --disable-xcb --disable-xcb-proto --disable-xorg-macros --disable-xproto --disable-zlib --disable-zvbi
# Remove --enable-libmp3lame in FFMpeg configure
nano ../src/ffmpeg/rules.mak
make
cd ../../
./configure --libdir /usr/lib/ --enable-shout --enable-vlm --enable-dvbpsi --enable-twolame --enable-libmpeg2 --enable-png --enable-x264 --enable-mad --enable-avcodec --enable-avformat --enable-swscale --enable-samplerate --enable-libxml2 --enable-libgcrypt --enable-gnutls --disable-dbus --disable-lua --disable-addonmanagermodules --disable-live555 --disable-dc1394 --disable-dv1394 --disable-linsys --disable-dvdread --disable-dvdnav --disable-bluray --disable-opencv --disable-smbclient --disable-dsm --disable-sftp --disable-nfs --disable-v4l2 --disable-decklink --disable-vcd --disable-libcddb --disable-screen --disable-vnc --disable-freerdp --disable-realrtsp --disable-asdcp --disable-gme --disable-sid --disable-ogg --disable-matroska --disable-mod --disable-mpc --disable-wma-fixed --disable-shine --disable-omxil --disable-omxil-vout --disable-rpi-omxil --disable-crystalhd --disable-mpg123 --disable-merge-ffmpeg --disable-gst-decode --disable-libva --disable-dxva2 --disable-d3d11va --disable-postproc --disable-faad --disable-aom --disable-vpx --disable-fdkaac --disable-a52 --disable-dca --disable-flac --disable-vorbis --disable-tremor --disable-speex --disable-opus --disable-theora --disable-oggspots --disable-daala --disable-schroedinger --disable-jpeg --disable-bpg --disable-x262 --disable-x265 --disable-x26410b --disable-mfx --disable-fluidsynth --disable-fluidlite --disable-zvbi --disable-telx --disable-libass --disable-aribsub --disable-aribb25 --disable-kate --disable-tiger --without-x --disable-xcb --disable-xvideo --disable-vdpau --disable-wayland --disable-sdl-image --disable-fontconfig --disable-fribidi --disable-harfbuzz --disable-freetype --disable-svg --disable-svgdec --disable-aa  --disable-caca --disable-kva --disable-mmal --disable-evas --disable-pulse --disable-alsa --disable-oss --disable-sndio --disable-opensles --disable-wasapi --disable-jack --disable-tizen-audio --disable-soxr --disable-kai --disable-chromaprint --disable-chromecast --disable-qt --disable-skins2 --disable-libtar --disable-ncurses --disable-lirc --disable-goom --disable-projectm --disable-vsxu --disable-avahi --disable-udev --disable-mtp --disable-upnp --disable-microdns --disable-taglib --disable-secret --disable-kwallet
make
```

## Create .deb package
After compiling VLC you can create a .deb package. This .deb package is needed for installing VLC on you target machine. With the commands below you can create a .deb package.

### Commands
```bash
mkdir -vp doc-pak && cp -v AUTHORS COPYING INSTALL NEWS README THANKS doc-pak
checkinstall --pakdir "$HOME/vlc_build" --backup=no --deldoc=yes --pkgname vlc --pkgversion "3.0.0-git~$(git rev-parse --short HEAD)" --fstrans=no --deldesc=yes --delspec=yes --default
```

## Install VLC package
With the commands below you can install VLC on your target server. 

### Commands
```bash
dpkg -i vlc_{version}_amd64.deb && /usr/lib/vlc/vlc-cache-gen /usr/lib/vlc/plugins/
```
