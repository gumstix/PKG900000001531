# Firmware

__Raspberry Pi OS__:

* [Raspberry Pi OS](https://www.raspberrypi.org/software/)
* Username: pi
* Password: raspberry
* Modifications: [config.txt:](config.txt)
    # pixhawk
    enable_uart=1
    dtoverlay=dwc2,dr_mode=host
    start_x=1
    gpu_mem=128
    dtoverlay=uart4,ctsrts
* Download to /boot folder (first partition) to enable both cameras
    [dt-blob.bin:](dt-blob.bin)
