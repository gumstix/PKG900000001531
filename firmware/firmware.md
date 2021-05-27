# Firmware

## Raspberry Pi OS

* [Raspberry Pi OS (64-bit recommended)](https://downloads.raspberrypi.org/raspios_arm64/images/)
* Username: pi
* Password: raspberry
* Modifications: [config.txt:](config.txt)
```
    # pixhawk
    enable_uart=1
    dtoverlay=dwc2,dr_mode=host
    start_x=1
    gpu_mem=128
    dtoverlay=uart4,ctsrts
    dtoverlay=pcie-32bit-dma
    gpio=6,7=op,dh
```
* Download to /boot folder (first partition) to enable both cameras
    [dt-blob.bin](dt-blob.bin)

## ROS2 Integration

__PX4 Update__:
* Add micrortps client
  * [PX4 1.12.0](px4_fmu-v6u_rtps.px4)
  ```
    FW git-hash: db3c89b1e34af65efc334d928a82196947bd8a3a
    FW version: 1.12.0 80 (17563776)
    FW git-branch: master
  ```
  * Compile with [patch](0001-CM4-micrortps.patch)
  ```
    git am < 0001-CM4-micrortps.patch 
    make px4_fmu-v6u_rtps upload
  ```

__Raspberry Pi CM4 Update__:
* [Install Docker](https://docs.docker.com/engine/install/debian/)
* Add Non-Root User
  ```
    sudo usermod -aG docker ${USER}
  ```
* Enable Docker starting on boot
  ```
    sudo systemctl enable docker
  ```
* Get the image and make it start on boot
  ```
    docker run -d --privileged --net=host --platform=linux/arm64 --restart=always gumstix/px4_ros2
  ```

__[ROS 2 Offboard Control Example](https://docs.px4.io/master/en/ros/ros2_offboard_control.html)__
* Run Docker image on another device on network (can be done on CM4 on Pixhawk).
  * Desktop machine
    ```
      docker run -it --net=host --platform=linux/amd64 gumstix/px4_ros2 bash
    ```
  * Raspberry Pi CM4
    ```
      docker run -it --net=host --platform=linux/arm64 gumstix/px4_ros2 bash
    ```
* Run offboard control example (ros2 is already sourced)
  ```
    ros2 run px4_ros_com offboard_control
  ```

## Coral Accelerator Example
* Install Docker - See above
* Execute steps [1](https://coral.ai/docs/accelerator/get-started/#1-install-the-edge-tpu-runtime), [2](https://coral.ai/docs/accelerator/get-started/#2-install-the-pycoral-library) and [3](https://coral.ai/docs/accelerator/get-started/#3-run-a-model-on-the-edge-tpu) of the [Coral Getting Started](https://coral.ai/docs/accelerator/get-started/)
* Step 3 needs to be run every time before docker command below
* Run gstreamer on host device (ip address = w.x.y.z)
```
  gst-launch-1.0 udpsrc port=5000 ! application/x-rtp,encoding-name=H264,payload=96 ! rtph264depay ! decodebin ! autovideosink
```
* Run Docker image and stream to host device
```
  docker run -d --privileged --network=host gumstix/coral_camera --hostip w.x.y.z
```

