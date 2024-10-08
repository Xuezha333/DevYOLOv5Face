# Fork of [deepcam-cn/yolov5-face](https://github.com/deepcam-cn/yolov5-face)

Differences between original repository and fork (Some MORE edits made by Xuezha333):

* Compatibility with PyTorch >=2.1. (🔥)
* Original pretrained models and converted ONNX models from GitHub [releases page](https://github.com/clibdev/yolov5-face/releases). (🔥)
* Installation with [requirements.txt](requirements.txt) file.
* The [wider_val.txt](data/widerface/val/wider_val.txt) file for WIDERFace evaluation. 
* The following deprecations and errors has been fixed:
  * UserWarning: torch.meshgrid: in an upcoming release, it will be required to pass the indexing argument.
  * DeprecationWarning: 'np.float' is a deprecated alias for builtin 'float'.
  * FutureWarning: Cython directive 'language_level' not set.
  * Cython Warning: Using deprecated NumPy API.
  * AttributeError: module 'numpy' has no attribute 'int'.
  * RuntimeError: result type Float can't be cast to the desired output type long int.
  * Fixed face bounding box drawing problem in the TensorRT example.
  * NameError: name 'warnings' is not defined.

# Installation

```shell
pip install -r requirements.txt
```

# Pretrained models

| Name                   | Easy  | Medium | Hard  | FLOPs(G) | Params(M) | Link                                                                                                                                                                                                |
|------------------------|-------|--------|-------|----------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| yolov5n-0.5            | 90.76 | 88.12  | 73.82 | 0.571    | 0.447     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-0.5.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-0.5.onnx)                 |
| yolov5n                | 93.61 | 91.52  | 80.53 | 2.111    | 1.726     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-face.onnx)               |
| yolov5s                | 94.33 | 92.61  | 83.15 | 5.751    | 7.075     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5s-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5s-face.onnx)               |
| yolov5m                | 95.30 | 93.76  | 85.28 | 18.146   | 21.063    | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5m-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5m-face.onnx)               |
| yolov5l                | 95.78 | 94.30  | 86.13 | 41.607   | 46.627    |                                                                                                                                                                                                     |
| yolov5l (non-original) | 95.63 | 94.06  | 85.49 | 41.607   | 46.627    | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5l-face-custom.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5l-face-custom.onnx) |

**Note:** Unable to download the YOLOv5l-Face model from the original repository due to an expired Google Drive link
and a lack of access to Baidu Netdisk. So, the decision has been made to train the YOLOv5l-Face model from scratch.
If someone have the original YOLOv5l-Face model, please create an issue with a new download link.

YOLOv5l-Face model training took about 10.57 hours using NVIDIA RTX 4090.
Results can be found in the [yolov5l-face.txt](result/train/yolov5l-face.txt) file

# Inference

```shell
python detect_face.py --weights weights/yolov5s-face.pt --source data/images/bus.jpg --save-img
```

# WIDERFace evaluation

* Download WIDERFace [validation dataset](https://drive.google.com/file/d/1GUCogbp16PMGa39thoMMeWxp7Rp5oM8Q/view).
* Move dataset to `data/widerface/val` directory.

```shell
python test_widerface.py --weights weights/yolov5s-face.pt --dataset_folder data/widerface/val/images
```
```shell
cd widerface_evaluate
```
```shell
python setup.py build_ext --inplace
```
```shell
python evaluation.py
```

# Export to ONNX format

```shell
pip install onnx onnxruntime
```
```shell
python export.py --weights weights/yolov5s-face.pt
```

# Export to TensorRT format

```shell
pip install tensorrt pycuda
```
```shell
python export.py --weights weights/yolov5s-face.pt --onnx2trt
```

# TensorRT Inference

```shell
python torch2trt/main.py --trt_path weights/yolov5s-face.trt --img_path data/images/bus.jpg
```

# PyTorch vs TensorRT speed comparison

```shell
python torch2trt/speed.py --torch_path weights/yolov5s-face.pt --trt_path weights/yolov5s-face.trt
```

# Training

* Download WIDERFace [training dataset](https://drive.google.com/file/d/15hGDLhsx8bLgLcIRD5DhYt5iBxnjNF1M/view).
* Download WIDERFace [validation dataset](https://drive.google.com/file/d/1GUCogbp16PMGa39thoMMeWxp7Rp5oM8Q/view).
* Download [annotation files](https://drive.google.com/file/d/1tU_IjyOwGQfGNUvZGwWWM4SwxKp2PUQ8/view).
* Move WIDERFace training images `WIDER_train/images` to `data/widerface/tmp/train/images`.
* Move WIDERFace validation images `WIDER_val/images` to `data/widerface/tmp/val/images`.
* Move training annotation file `train/label.txt` to `data/widerface/tmp/train/label.txt`.
* Move validation annotation file `val/label.txt` to `data/widerface/tmp/val/label.txt`.

```shell
python data/train2yolo.py data/widerface/tmp/train data/widerface/train
```
```shell
python data/val2yolo.py data/widerface/tmp data/widerface/val
```
```shell
pip install tensorboard
```

* Start training:

```shell
python train.py --data data/widerface.yaml --cfg models/yolov5n-0.5.yaml
```
```shell
python train.py --data data/widerface.yaml --cfg models/yolov5l.yaml --weights weights/yolov5l.pt
```

* Resume training:

```shell
python train.py --data data/widerface.yaml --cfg models/yolov5n-0.5.yaml --resume
```
```shell
python train.py --data data/widerface.yaml --cfg models/yolov5l.yaml --resume
```
