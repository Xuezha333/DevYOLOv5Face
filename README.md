# Fork of [deepcam-cn/yolov5-face](https://github.com/deepcam-cn/yolov5-face)

Differences between original repository and fork:

* Compatibility with PyTorch >=2.0. (🔥)
* Original pretrained models and converted ONNX models from GitHub [releases page](https://github.com/clibdev/yolov5-face/releases). (🔥)
* Installation with [requirements.txt](requirements.txt) file.
* The [wider_val.txt](data/widerface/val/wider_val.txt) file for WIDERFace evaluation. 
* The following deprecations has been fixed:
  * UserWarning: torch.meshgrid: in an upcoming release, it will be required to pass the indexing argument.
  * DeprecationWarning: 'np.float' is a deprecated alias for builtin 'float'.
  * FutureWarning: Cython directive 'language_level' not set.
  * Cython Warning: Using deprecated NumPy API.

# Installation

```shell
pip install -r requirements.txt
```

# Pretrained models

| Name        | Easy  | Medium | Hard  | FLOPs(G) | Params(M) | Link                                                                                                                                                                                  |
|-------------|-------|--------|-------|----------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| yolov5n-0.5 | 90.76 | 88.12  | 73.82 | 0.571    | 0.447     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-0.5.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-0.5.onnx)   |
| yolov5n     | 93.61 | 91.52  | 80.53 | 2.111    | 1.726     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5n-face.onnx) |
| yolov5s     | 94.33 | 92.61  | 83.15 | 5.751    | 7.075     | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5s-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5s-face.onnx) |
| yolov5m     | 95.30 | 93.76  | 85.28 | 18.146   | 21.063    | [PyTorch](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5m-face.pt), [ONNX](https://github.com/clibdev/yolov5-face/releases/latest/download/yolov5m-face.onnx) |
| yolov5l     | 95.78 | 94.30  | 86.13 | 41.607   | 46.627    |                                                                                                                                                                                       |

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

## Data preparation

1. Download WIDERFace datasets.
2. Download annotation files from [google drive](https://drive.google.com/file/d/1tU_IjyOwGQfGNUvZGwWWM4SwxKp2PUQ8/view?usp=sharing).

```shell
cd data
python3 train2yolo.py /path/to/original/widerface/train [/path/to/save/widerface/train]
python3 val2yolo.py  /path/to/original/widerface [/path/to/save/widerface/val]
```

## Training

```shell
CUDA_VISIBLE_DEVICES="0,1,2,3" python3 train.py --data data/widerface.yaml --cfg models/yolov5s.yaml --weights 'pretrained models'
```

#### Citation 
- If you think this work is useful for you, please cite 

      @article{YOLO5Face,
      title = {YOLO5Face: Why Reinventing a Face Detector},
      author = {Delong Qi and Weijun Tan and Qi Yao and Jingfeng Liu},
      booktitle = {ArXiv preprint ArXiv:2105.12931},
      year = {2021}
      }

