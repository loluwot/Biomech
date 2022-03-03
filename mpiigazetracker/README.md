# These are my modifications to the script

- functionality to map the predicted gaze vector from webcam image to the computer screen
- gridview pointer showing where the gaze is currently pointed at (adjustable pixel size)
- added landmark averaging: **AVG_LANDMARKS** over number of frames
- added gaze vector averaging: **GAZE_AVG_FLAG** over number of gaze vectors
- video playback with different scenarios such as **UPDOWN**, **LEFTRIGHT**, **STABILITY**, **SEQ**
- performance evaluation calcuation of **MAE**, **CEP** and **CE95**
- drawing functions for the results

## Calibration values
Currently it is calibrated to my personal MBP13 so this needs to be adjusted accoring to your computer:
> check **screen_conf.py** for more details

## Model weights
* Landmarks: http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2
* Eye model: https://github.com/hysts/pytorch_mpiigaze_demo/releases/download/v0.1.0/mpiigaze_resnet_preact.pth
* Face model: https://github.com/hysts/pytorch_mpiigaze_demo/releases/download/v0.1.0/mpiifacegaze_resnet_simple.pth
The Landmarks dlib file needs to be unzipped and moved to **pytorch_mpiigaze/data/dlib/** folder. Move the pth files into **data/models/** -> **mpiigaze/resnet_preact/** for the eye model and **mpiifacegaze/resnet_simple_14/** for the face model.

## Run the script
* eye model: configs/demo_mpiigaze_resnet.yaml
* face model: configs/demo_mpiifacegaze_resnet_simple_14.yaml

python point_2_screen.py --config configs/demo_mpiigaze_resnet.yaml --demo 1

python point_2_screen.py --config configs/demo_mpiigaze_resnet.yaml --demo 1 --cust_vid 'path2video' --mode 'SEQ'

## Result:
![GRID VIEW](grid.png)

# A PyTorch implementation of MPIIGaze and MPIIFaceGaze

[Here](https://github.com/hysts/pytorch_mpiigaze_demo) is a demo program.


## Requirements

* Python >= 3.7

```bash
pip install -r requirements.txt
```


## Download the dataset and preprocess it

### MPIIGaze

```bash
bash scripts/download_mpiigaze_dataset.sh
python tools/preprocess_mpiigaze.py --dataset datasets/MPIIGaze -o datasets/
```

### MPIIFaceGaze

```bash
bash scripts/download_mpiifacegaze_dataset.sh
python tools/preprocess_mpiifacegaze.py --dataset datasets/MPIIFaceGaze -o datasets/
```


## Usage

This repository uses [YACS](https://github.com/rbgirshick/yacs) for
configuration management.
Default parameters are specified in
[`gaze_estimation/config/defaults.py`](gaze_estimation/config/defaults.py)
(which is not supposed to be modified directly).
You can overwrite those default parameters using a YAML file like
[`configs/mpiigaze/lenet_train.yaml`](configs/mpiigaze/lenet_train.yaml).


### Training and Evaluation

By running the following code, you can train a model using all the
data except the person with ID 0, and run test on that person.

```bash
python train.py --config configs/mpiigaze/lenet_train.yaml
python evaluate.py --config configs/mpiigaze/lenet_eval.yaml
```

Using [`scripts/run_all_mpiigaze_lenet.sh`](scripts/run_all_mpiigaze_lenet.sh) and
[`scripts/run_all_mpiigaze_resnet_preact.sh`](scripts/run_all_mpiigaze_resnet_preact.sh),
you can run all training and evaluation for LeNet and ResNet-8 with
default parameters.


## Results

### MPIIGaze

| Model           | Mean Test Angle Error [degree] | Training Time |
|:----------------|:------------------------------:|--------------:|
| LeNet           |              6.52              |  3.5 s/epoch  |
| ResNet-preact-8 |              5.73              |   7 s/epoch   |

The training time is the value when using GTX 1080Ti.

![](figures/mpiigaze/lenet.png)

![](figures/mpiigaze/resnet_preact_8.png)

### MPIIFaceGaze

| Model     | Mean Test Angle Error [degree] | Training Time |
|:----------|:------------------------------:|--------------:|
| AlexNet   |              5.06              |  135 s/epoch  |
| ResNet-14 |              4.83              |   62 s/epoch  |

The training time is the value when using GTX 1080Ti.

![](figures/mpiifacegaze/alexnet.png)

![](figures/mpiifacegaze/resnet_simple.png)




### Demo

This demo program runs gaze estimation on the video from a webcam.

1. Download the dlib pretrained model for landmark detection.

    ```bash
    bash scripts/download_dlib_model.sh
    ```

2. Calibrate the camera.

    Save the calibration result in the same format as the sample
    file [`data/calib/sample_params.yaml`](data/calib/sample_params.yaml).

4. Run demo.

    Specify the model path and the path of the camera calibration results
    in the configuration file as in
    [`configs/demo_mpiigaze_resnet.yaml`](configs/demo_mpiigaze_resnet.yaml).

    ```bash
    python demo.py --config configs/demo_mpiigaze_resnet.yaml
    ```


## References

* Zhang, Xucong, Yusuke Sugano, Mario Fritz, and Andreas Bulling. "Appearance-based Gaze Estimation in the Wild." Proc. of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2015. [arXiv:1504.02863](https://arxiv.org/abs/1504.02863), [Project Page](https://www.mpi-inf.mpg.de/departments/computer-vision-and-multimodal-computing/research/gaze-based-human-computer-interaction/appearance-based-gaze-estimation-in-the-wild/)
* Zhang, Xucong, Yusuke Sugano, Mario Fritz, and Andreas Bulling. "It's Written All Over Your Face: Full-Face Appearance-Based Gaze Estimation." Proc. of the IEEE Conference on Computer Vision and Pattern Recognition Workshops(CVPRW), 2017. [arXiv:1611.08860](https://arxiv.org/abs/1611.08860), [Project Page](https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/research/gaze-based-human-computer-interaction/its-written-all-over-your-face-full-face-appearance-based-gaze-estimation/)
* Zhang, Xucong, Yusuke Sugano, Mario Fritz, and Andreas Bulling. "MPIIGaze: Real-World Dataset and Deep Appearance-Based Gaze Estimation." IEEE transactions on pattern analysis and machine intelligence 41 (2017). [arXiv:1711.09017](https://arxiv.org/abs/1711.09017)



