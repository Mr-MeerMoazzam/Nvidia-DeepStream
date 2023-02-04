
# dGPU Setup for Ubunto

Here you will see how to Setup the DeepStream SDK on Ubunto.




## Requirements

You must install the following components:

 - Ubuntu 20.04

 - GStreamer 1.16.3 [install]

 - NVIDIA driver 525.85.12 [install](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Quickstart.html#install-nvidia-driver-525-85-12)

 - CUDA 11.8 [install](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Quickstart.html#install-cuda-toolkit-11-8)

 - TensorRT 8.5.2.2 [install](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Quickstart.html#install-tensorrt-8-5-2-2)

## Remove all previous DeepStream installations
Enter the following commands to remove all previous DeepStream 3.0 or prior installations:
```bash
sudo rm -rf /usr/local/deepstream /usr/lib/x86_64-linux-gnu/gstreamer-1.0/libgstnv* /usr/bin/deepstream* /usr/lib/x86_64-linux-gnu/gstreamer-1.0/libnvdsgst*
/usr/lib/x86_64-linux-gnu/gstreamer-1.0/deepstream*
/opt/nvidia/deepstream/deepstream*

```
```bash
sudo rm -rf /usr/lib/x86_64-linux-gnu/libv41/plugins/libcuvidv4l2_plugin.so
```
To remove DeepStream 4.0 or later installations:

1. Open the `uninstall.sh` file in `/opt/nvidia/deepstream/deepstream/`
2. Set PREV_DS_VER as 4.0
3. Run the following script as `sudo ./uninstall.sh`

## Install Dependencies

Before installing the DeepStream SDK, use the following instructions to instal the required packages:
```bash
sudo apt install \
libssl1.1 \
libgstreamer1.0-0 \
gstreamer1.0-tools \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly \
gstreamer1.0-libav \
libgstreamer-plugins-base1.0-dev \
libgstrtspserver-1.0-0 \
libjansson4 \
libyaml-cpp-dev \
libjsoncpp-dev \
protobuf-compiler \
gcc \
make \
git \
python3

```

## Install the DeepStream SDK

### Method 1: 
Using the DeepStream Debian package, below are the steps to install it.


- [Download](https://developer.nvidia.com/downloads/deepstream-62-620-1-amd64-deb) the DeepStream 6.2 dGPU Debian package deepstream-6.2_6.2.0-1_amd64.deb

- Navigate to the location of the downloaded DeepStream package to extract and install the DeepStream SDK by run the following command
```bash
sudo apt-get install ./deepstream-6.2_6.2.0-1_amd64.deb
```

### Method 2:
Using the DeepStream tar package, below are the steps to install it.

- [Download](https://developer.nvidia.com/downloads/deepstream-sdk-v620-x86-64-tbz2) the DeepStream tar package 


- Navigate to the location of the downloaded DeepStream package to extract and install the DeepStream SDK bu running the following commands.
```bash
$ sudo tar -xvf deepstream_sdk_v6.2.0_x86_64.tbz2 -C /
$ cd /opt/nvidia/deepstream/deepstream-6.2/
$ sudo ./install.sh
$ sudo ldconfig
```
```bash
sudo apt-get install ./deepstream-6.2_6.2.0-1_amd64.deb
```

### Run the deepstream-app(the reference application)
Now it is good to run the reference DeepStream App by following the steps below:
- Go to the samples directory (/opt/nvidia/deepstream/deepstream-6.2/samples/configs/deepstream-app) and run this command 
```bash 
deepstream-app -c <path_to_config_file>

```
Where <path_to_config_file> is the pathname of one of the reference application’s configuration files, found in `configs/deepstream-app.

