Raspberry Pi ADS1015 Device Tree Overlay
==
This is a device tree overlay for the Texas Instruments ADS1015 A/D. Minimal changes would be required for it to work with other chips from the same family (ADS 1013/1115 etc).

**Getting Started**

First you need to install the device tree compiler (assuming you are using debian/raspbian):

```
  apt-get install device-tree-compiler
```

Now compile the .dts:

```
  dtc -@ -I dts -O dtb -o ads1015.dtbo ads1015.dts
```

Move the compiled overlay object to the raspberry pi's /boot/overlays

```
  mv ads1015.dtb /boot/overlays
```

Add the following line to the pi's /boot/config.txt to load the overlay at boot:

```
  dtoverlay=ads1015
```

**Options**

By default the overlay will only expose a single channel in differential mode (Pos A0, Neg A1). To expose other channels, change the channel configurations, change the sample rate or change the amplifier gain/scale you need to set some additional options in /boot/config.txt. All these lines must follow the "dtoverlay=ads1015" line in config.txt.

To set the i2c slave address specify the address you set with the addr pin. The default of 0x48 assumes the addr pin is pulled to ground.

Addr Pin Connected To | Address 
--- | ---
GND | 0x48
VDD | 0x49
SDA | 0x4A
SDL | 0x4B

```
  dtparam=addr=0x48
```

The ADS1015 supports 2 differential channels or 4 channels referenced to ground. You must ensure you enable and configure channels properly as misconfigured channels will have undefined/unexpected results.

To enable a channel add the following line to your /boot/config.txt after the "dtoverlay=ads1015" line:

```
  dtparam=chX_enable=true
```

Where X is a channel number from 1 to 4. To enable channel 2:

```
  dtparam=ch2_enable=true
```

After enabling a channel you must set the channel mode, use the following table (from the ADS1015 datasheet):

chX_cfg | Input MUX Setting | Mode
--- | --- | ---
0 | A0 (Pos) and A1 (Neg) | Differential
1 | A0 (Pos) and A3 (Neg) | Differential
2 | A1 (Pos) and A3 (Neg) | Differential
3 | A2 (Pos) and A3 (Neg) | Differential
4 | A0 and GND | GND Ref
5 | A1 and GND | GND Ref
6 | A2 and GND | GND Ref
7 | A3 and GND | GND Ref

```
  dtparam=chX_cfg=0
```

You can optionally set the gain of the PGA on the device.

chX_gain | FS
--- | ---
0 | 6.144V
1 | 4.096V
2 | 2.048V (default)
3 | 1.024V
4 | 0.512V
5 | 0.256V
6 | 0.256V
7 | 0.256V

```
  dtparam=chX_gain=0
```

You can optionally set the sample rate of the device

chX_datarate | Samples Per Sec
--- | ---
0 | 128
1 | 250
2 | 490
3 | 920
4 | 1600 (default)
5 | 2400
6 | 3300
7 | 3300

```
  dtparam=chX_datarate=6
```

PS. Some markdown renderers don't support the tables...
