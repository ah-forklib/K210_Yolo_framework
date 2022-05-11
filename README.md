# K210_Yolo_framework

> For old README, please visit [here](./README_old.md).

+ This repository is modified by [Aoi-hosizora](https://github.com/Aoi-hosiozra) for Tensorflow 1.15.2 and Google Colab.

### Record

> Passed when 2022/01/12 and 2022/05/08.

+ Prepare environment

```python
%tensorflow_version 1.x
!pip install tensorflow_model_optimization==0.1.1 scikit_image==0.15.0 h5py==2.10.0 --force-reinstall
# RESTART RUNTIME
%tensorflow_version 1.x
import tensorflow as tf
print(tf.__version__)
# 1.15.2
```

+ Model training and compiling

```bash
# Generate xxx_img_ann.npy
python make_voc_list.py datasets/test_20220501/train.txt data/test_20220501_img_ann.npy

# Generate xxx_anchor.npy
make anchors DATASET=test_20220501 ANCNUM=3 LOW="0.0 0.0" HIGH="1.0 1.0"

# Train using mobilenet_v1_base_7.h5 ()
make train MODEL=yolo_mobilev1 DATASET=test_20220501 DEPTHMUL=0.75 \
    CLSNUM=3 OBJTHRESH=0.7 MAXEP=150 BATCH=64 ILR=0.001 SPLITFACTOR=0.1 IAA=True PRUNE=False

# Test
make inference MODEL=yolo_mobilev1 DATASET=test_20220501 DEPTHMUL=0.75 \
    CLSNUM=3 OBJTHRESH=0.2 \
    CKPT=/content/drive/MyDrive/Colab/k210/K210_Yolo_framework/yolo_model.h5 IMG=/content/drive/MyDrive/Colab/k210/K210_Yolo_framework/datasets/test/24.png

# Quantize model to tflite
toco --output_file log/20220111-202657/yolo_model.tflite --keras_model_file log/20220111-202657/yolo_model.h5

# Compile model to kmodel
ncc log/20220111-202657/yolo_model.tflite log/20220111-202657/yolo_model.kmodel -i tflite -o k210model --dataset ...
```

### Related tools

+ Pretrained models: https://github.com/zhen8838/K210_Yolo_framework#download-pre-trian-model
+ nncase: https://github.com/kendryte/nncase/releases/tag/v0.2.0-alpha4
+ Kendryte toolchain: https://github.com/kendryte/kendryte-gnu-toolchain/releases/tag/v8.2.0-20190409
+ kflash_gui: https://dl.sipeed.com/MAIX/tools/kflash_gui/kflash_gui_v1.6.5
+ SEASKY-K210-YOLO-DEMO: https://github.com/SEASKY-Master/Yolo-for-k210/tree/bdf79c87e4/seasky_yolo

### Reference

+ [Does Any one got "AttributeError: 'str' object has no attribute 'decode' " , while Loading a Keras Saved Model](https://stackoverflow.com/questions/53740577/)does-any-one-got-attributeerror-str-object-has-no-attribute-decode-whi
+ [ValueError: Dimensions must be equal, ... when training is started using YOLO](https://github.com/zhen8838/K210_Yolo_framework/issues/12)
+ [ValueError: Empty training data.](https://github.com/zhen8838/K210_Yolo_framework/issues/26)
+ [tensorflow.python.eager.core._FallbackException: This function does not handle the case of the path where all inputs are not already EagerTensors.](https://github.com/tensorflow/tensorflow/issues/28924)
+ [How do I disable TensorFlow's eager execution?](https://stackoverflow.com/questions/53429896/how-do-i-disable-tensorflows-eager-execution)
+ [SEASKY-Master/Yolo-for-k210](https://github.com/SEASKY-Master/Yolo-for-k210)
