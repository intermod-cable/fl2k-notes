# fl2k-notes

Notes on playback of tbc files using the fl2k.

## Using GNRC-Flowgraphs


### Install `vhs-decode`

```
sudo apt install ffmpeg
wget https://github.com/oyvindln/vhs-decode/releases/download/0.3.8/vhs-decode-x86_64.AppImage
chmod +x vhs-decode-x86_64.AppImage
```

Convert flac file:

```
./vhs-decode-x86_64.AppImage vhs --threads 2 --tf vhs --ts SP -f 20 --ntsc --overwrite --ire0_adjust tbcs/CBS-1987-10-23-NTSC-SP-vhs-rf-20msps-u8.flac tbcs/output-sample.tbc
```

### Install `gnuradio`

```
sudo apt install gnuradio
```

Update the config to make sure `xterm` is set. This actually may not be needed:

```
which xterm
/usr/bin/xterm

cat  /etc/gnuradio/conf.d/grc.conf
# This file contains system wide configuration data for GNU Radio.
# You may override any setting on a per-user basis by editing
# ~/.config/gnuradio/grc.conf

[grc]
global_blocks_path = /usr/share/gnuradio/grc/blocks:/usr/local/share/gnuradio/grc/blocks
local_blocks_path =
examples_path = /usr/share/gnuradio/examples:/usr/local/share/gnuradio/examples
default_flow_graph =
xterm_executable = xterm
#x-terminal-emulator
canvas_font_size = 8
canvas_default_size = 1280, 1024
enabled_components = testing-support;python-support;post-install;man-pages;gnuradio-runtime;common-precompiled-headers;gr-ctrlport;* thrift;gnuradio-companion;JSON/YAML config blocks;gr-blocks;gr-fec;gr-fft;gr-filter;gr-analog;gr-digital;gr-dtv;gr-audio;* alsa;* oss;* jack;* portaudio;gr-channels;gr-pdu;gr-iio;* libad9361;gr-qtgui;gr-trellis;gr-uhd;gr-uhd UHD 4.0 RFNoC;gr-utils;gr_modtool;gr_blocktool;gr-video-sdl;gr-vocoder;* codec2;* freedv;* gsm;gr-wavelet;gr-zeromq;gr-network;gr-soapy
```

Open `tbc_via_fl2k.grc`. Use `output-sample.tbc` as the input file and `outfile.s8` as the output sink in the GUI and then execute flowgrah.

### Install `fl2k_file`

Install dependencies:

```
sudo apt update && sudo apt install libusb-1.0-0-dev libsoxr-dev libsoxr0 libsoxr-lsr0 git make cmake -y
```

Before being able to use the device as a non-root user, the udev rules need to be reloaded:

```
sudo udevadm control -R && sudo udevadm trigger
```

Install `osmo-fl2k`:

```
git clone https://gitea.osmocom.org/sdr/osmo-fl2k
mkdir osmo-fl2k/build
cd osmo-fl2k/build
cmake ../ -DINSTALL_UDEV_RULES=ON
make -j 3
sudo make install
sudo ldconfig
```

Playback the file:

```
sudo echo 0 > /sys/module/usbcore/parameters/usbfs_memory_mb
fl2k_file -s 14318181 outfile.s8
```

Ignore errors about incorrect rate.

## Sources

- https://github.com/tandersn/GNRC-Flowgraphs/tree/main/tbc_via_fl2k