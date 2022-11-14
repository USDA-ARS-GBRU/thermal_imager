# Automated image acquisition software for thermal mastitis detection

This repository contains scripts to automatically collect thermal images of cows in the dairy
by detecting the EID radio frequency identification ear tags on each animal.  Cows move through the chute
to the milking parlor quickly and gates are operated remotely so it is difficult
to manually to collect images of cows and associate them with the correct animal ID.

## Hardware

_EID Tag Reader_ :  We are using a [Tru-Test ERP2 Tag reader](https://www.livestock.tru-test.com/en-us/readers/xrp2-panel-reader). This reader has a well documented serial protocol that can be accessed by cable or over Bluetooth. Protocol documentation is in the `docs` file of
this repo. By default is sends the EID every time a new tag is read.  
The range on the reader is pretty good, about 80 cm.

_Ear Tags_ : We are using [Allflex FDX Ear tags (non-USDA IDs)](https://www.cattletags.com/collections/allflex-standard-global-eid-tags/products/allflex-tfp-fdxotp982-y-gesmy-20). The Tru-test reader will detect
any common format tag.

_FLIR Teledyne Boson Thermal cameras_: We have two thermal cameras that we
will use on each side of the chute to image all for quarters of the udder.

-   The first camera is a FLIR Boson 320 (non-radiometric) with a 34 degree
    field-of -view (6.3 mm lens) and a resolution of 320 × 256.  P/N 20320A034-6PAAX,
    S/N S0097206.

-   The second camera is a FLIR Boson 320 (radiometric) with a 34 degree
    field-of -view (6.3 mm lens) and a resolution of 320 × 256.  

Both of these cameras can output data in 8-bit or 16 bit format. The standard
8-bit format is contrast adjusted so it may not be the best input.  
There is not a way to capture 16-bit data from a Mac, but its can be done from
a PC or Linux computer. 16-bit images can be post-processed in a uniform way. I
think this will be important for batch effect corrections.

_Raspberry Pi_

Interacting with these cameras requires hardware drivers that are platform
specific. I would like to leave our setup in the dairy for 24 hour periods. its a pretty wet place and leaving a laptop is not ideal.  I am looking at using a linux raspberry pi to collect data.  I am currently loaning ARS my personal  Pi because the global chip shortage has made them difficult to procure.



## Software libraries

FLIR offers a number of ways to connect to the camera.

### _FLIR's Boson USB Repo_

They have an old example repo <https://github.com/FLIR/BosonUSB>. I could not
get cmake to build but I compiles if directly in g++ and it worked okay.  This
tuned out not to be the easiest way to access the camera.

### _OpenCV_

(OpenCV)[https://docs.opencv.org/] can directly  record fro mthe camera using a
number of protocols and drivers.  the `capture_images.py` file in the is
directory is an example of how to capture images  with openCV. OpenCV can also
capture images on a Mac in 8-bit mode.

### _Flirpy_

(Flirpy)[https://github.com/LJMUAstroecology/flirpy] is a community Python
repository to control Boson Cameras.  This is particularly helpful for
dealing with radiometric images where each pixel corresponds to a temperature.

## Acquisition Code

The script `capture_images.py` demonstrated how to connect to the serial
port and listen for  asynchronous  tag reading events.  When an ear tag is read
it triggers an image collection event.
