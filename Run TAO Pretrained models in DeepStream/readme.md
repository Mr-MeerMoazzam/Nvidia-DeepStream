
# Run the TAO Models in DeepStream

Here we will describe the procedure to download and run the TAO pre-trained models in DeepStream.
Before running the TAO models in DeepStream you must have DeepStream SDK in your system. If you don't have then [install it] 
## Running the TAO Pretrained Purpose-Built Models in DeepStream

Firstly we will run the TAO Pretrained Purpose-Built models in DeepStream.

### Download the config files

- To download the config files, run the following commands on the terminal
```bash
$ git clone https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps.git
$ cd deepstream_reference_apps/deepstream_app_tao_configs/
$ sudo cp -a * /opt/nvidia/deepstream/deepstream/samples/configs/tao_pretrained_models/

```
### Download the models

- To download the models, run the following commands on the terminal
```bash
$ sudo apt install -y wget zip
$ cd /opt/nvidia/deepstream/deepstream/samples/configs/tao_pretrained_models/
$ sudo ./download_models.sh
```

### Run the models in DeepStream

- To run the models in deep stream, run the following command on the terminal
```bash
$ sudo deepstream-app -c deepstream_app_source1_$MODEL.txt
```
e.g.
```bash
$ sudo deepstream-app -c deepstream_app_source1_dashcamnet_vehiclemakenet_vehicletypenet.txt
```
The yaml config files can also be used
```bash
$ sudo deepstream-app -c deepstream_app_source1_$MODEL.yml
```
e.g.
```bash
$ sudo deepstream-app -c deepstream_app_source1_dashcamnet_vehiclemakenet_vehicletypenet.yml
```
#### Note:
For which model of the deepstream_app_source1_$MODEL.txt uses, please find from the [primary-gie] section in it, for example

Below is the [primary-gie] config of deepstream_app_source1_detection_models.txt, which indicates it uses yolov4 by default, and user can change to frcnn/ssd/dssd/retinanet/yolov3/detectnet_v2 by commenting "config-file=config_infer_primary_yolov4.txt" and uncommenting the corresponding "config-file=" .
```bash
[primary-gie]
enable=1
gpu-id=0
# Modify as necessary
batch-size=1
#Required by the app for OSD, not a plugin property
bbox-border-color0=1;0;0;1
bbox-border-color1=0;1;1;1
bbox-border-color2=0;0;1;1
bbox-border-color3=0;1;0;1
gie-unique-id=1
# Replace the infer primary config file when you need to
# use other detection models
#config-file=config_infer_primary_frcnn.txt
#config-file=config_infer_primary_ssd.txt
#config-file=config_infer_primary_dssd.txt
#config-file=config_infer_primary_retinanet.txt
#config-file=config_infer_primary_yolov3.txt
config-file=config_infer_primary_yolov4.txt
#config-file=config_infer_primary_detectnet_v2.txt
```

## Running the TAO (Custom trained) Models  in DeepStream

Now we will run the TAO models trained on our custom data in DeepStream. But before that you have to download the config files and models.

We have two types of files on the path below:
```bash
/opt/nvidia/deepstream/deepstream-6.1/samples/configs/tao_pretrained_models
```
1. config_infer_primary_detectnet_v2.txt or yml (supporting config file)
2. deepstream_app_source1_detection_models.txt or yml (main config file)


Now placed the trained model (model.etlt file) and other files like cache file , enginefile, and labels.txt in the directory path below:
```bash
/opt/nvidia/deepstream/deepstream-6.1/samples/models/tao_pretrained_models/yolov4/n/
```
- Changes in the supporting config file 
Now go to configuration path and create new file named as `nvinfer_config.txt` (you can rename it), and copy and paste supporting config file to your new created file and set parameter just like I did in the following file `nvinfer_config.txt`. we have two options either set engine file and cache file along with model.etlt and labels.txt but this engine file is specific to hardware use. The second option is setup parameter for model.etlt and labels.txt and optional cache file so deep stream will autogenerate engine file. We will choose the second option. Look at the changes I made it in the file.

```bash
[property]
gpu-id=0
net-scale-factor=1.0
offsets=103.939;116.779;123.68
model-color-format=1
labelfile-path= ../../models/tao_pretrained_models/yolov4/n/labels.txt      # set this path to your trained tao model labels
model-engine-file=../../models/tao_pretrained_models/yolov4/n/trt.engine    # set this path to your trained tao model engine file
int8-calib-file=../../models/tao_pretrained_models/yolov4/n/cal.bin         # # set this path to your trained tao model calibiration cache
tlt-encoded-model=../../models/tao_pretrained_models/yolov4/n/yolov4_resnet18_epoch_080.etlt   # set this path to your trained tao-model.eltl
tlt-model-key = NGpmbHN0ZTNrZHFkOGRxNnFsbW9rbXNxbnU6Yzc5NWM5MjQtZDE1YS00NTYxLTg3YzgtNTU2MWVhNDg1M2M3  # set the key which you are using during training time.
infer-dims=3;384;1248   # set this infer dimension when generate config for model that will have this infor
maintain-aspect-ratio=1
uff-input-order=0
uff-input-blob-name=Input
batch-size=1
## 0=FP32, 1=INT8, 2=FP16 mode
network-mode=0
num-detected-classes=3
interval=0
gie-unique-id=1
is-classifier=0
#network-type=0
cluster-mode=3
output-blob-names=BatchedNMS
parse-bbox-func-name=NvDsInferParseCustomBatchedNMSTLT
custom-lib-path=/opt/nvidia/deepstream/deepstream/lib/libnvds_infercustomparser.so
layer-device-precision=cls/mul:fp32:gpu;box/mul_6:fp32:gpu;box/add:fp32:gpu;box/mul_4:fp32:gpu;box/add_1:fp32:gpu;cls/Reshape_reshape:fp32:gpu;box/Reshape_reshape:fp32:gpu;encoded_detections:fp32:gpu;bg_leaky_c>

[class-attrs-all]
pre-cluster-threshold=0.3
roi-top-offset=0
roi-bottom-offset=0
detected-min-w=0
detected-min-h=0
detected-max-w=0
detected-max-h=0

[class-attrs-1]
nms-iou-threshold=0.9
```
- Changes in the main config file 
I created new file deepstrean_app_source1_custom_yolov4.txt (you can rename it), and copy and paste main config file to your new created file. Look at the changes I made it in the file.

```bash
[application]
enable-perf-measurement=1
perf-measurement-interval-sec=1

[tiled-display]
enable=1
rows=1
columns=1
width=1280
height=720
gpu-id=0

[source0]
enable=1
#Type - 1=CameraV4L2 2=URI 3=MultiURI
type=3
num-sources=1
uri=file://../../streams/sample_1080p_h265.mp4
gpu-id=0

[streammux]
gpu-id=0
batch-size=1
batched-push-timeout=40000
## Set muxer output width and height
width=1920
height=1080

[sink0]
enable=1
#Type - 1=FakeSink 2=EglSink 3=File
type=2
sync=1
source-id=0
gpu-id=0

[osd]
enable=1
gpu-id=0
border-width=3
text-size=15
text-color=1;1;1;1;
text-bg-color=0.3;0.3;0.3;1
font=Arial

[primary-gie]
enable=1
gpu-id=0
# Modify as necessary
batch-size=1
#Required by the app for OSD, not a plugin property
bbox-border-color0=1;0;0;1
bbox-border-color1=0;1;1;1
bbox-border-color2=0;0;1;1
bbox-border-color3=0;1;0;1
gie-unique-id=1
# Replace the infer primary config file when you need to
# use other detection models
#config-file=config_infer_primary_frcnn.txt
#config-file=config_infer_primary_ssd.txt
config-file = nvinfer_config.txt                      # just set this to you supporting config file and other all will be default
#config-file=config_infer_primary_dssd.txt
#config-file=config_infer_primary_retinanet.txt
#config-file=config_infer_primary_yolov3.txt
#config-file=config_infer_primary_yolov4.txt
#config-file=config_infer_primary_detectnet_v2.txt
#config-file=config_infer_primary_yolov4-tiny.txt
#config-file=config_infer_primary_efficientdet.txt

[sink1]
enable=0
type=3
#1=mp4 2=mkv
container=1
#1=h264 2=h265 3=mpeg4
codec=1
#encoder type 0=Hardware 1=Software
enc-type=0
sync=0
bitrate=2000000
#H264 Profile - 0=Baseline 2=Main 4=High
#H265 Profile - 0=Main 1=Main10
profile=0
output-file=out.mp4
source-id=0

[sink2]
enable=0
#Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming 5=Overlay
type=4
#1=h264 2=h265
codec=1
#encoder type 0=Hardware 1=Software
enc-type=0
sync=0
bitrate=4000000
#H264 Profile - 0=Baseline 2=Main 4=High
#H265 Profile - 0=Main 1=Main10
profile=0
# set below properties in case of RTSPStreaming
rtsp-port=8554
udp-port=5400

[tracker]
enable=1
# For NvDCF and DeepSORT tracker, tracker-width and tracker-height must be a multiple of 32, respectively
tracker-width=640
tracker-height=384
ll-lib-file=/opt/nvidia/deepstream/deepstream/lib/libnvds_nvmultiobjecttracker.so
# ll-config-file required to set different tracker types
# ll-config-file=../deepstream-app/config_tracker_IOU.yml
ll-config-file=../deepstream-app/config_tracker_NvDCF_perf.yml
# ll-config-file=../deepstream-app/config_tracker_NvDCF_accuracy.yml
# ll-config-file=../deepstream-app/config_tracker_DeepSORT.yml
gpu-id=0
enable-batch-process=1
enable-past-frame=1
display-tracking-id=1

[tests]
file-loop=0
```

Now we are ready to run TAO model trained on our custom data in DeepStream.
```bash
sudo deepstream-app -c deepstrean_app_source1_custom_yolov4.txt
```


## Acknowledgements

 - [Official Documentation](https://docs.nvidia.com/tao/tao-toolkit/text/ds_tao/yolo_v4_ds.html#integrating-yolov4-model)
 - [Documentation](https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps/tree/master/deepstream_app_tao_configs)

