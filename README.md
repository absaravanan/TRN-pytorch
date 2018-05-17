# Temporal Relation Networks

We release the code of the [Temporal Relation Networks](http://relation.csail.mit.edu/), built on top of the [TSN-pytorch codebase](https://github.com/yjxiong/temporal-segment-networks).

**Note**: always use `git clone --recursive https://github.com/metalbubble/TRN-pytorch` to clone this project
Otherwise you will not be able to use the inception series CNN architecture.



### Data preparation
Download the [something-something dataset](https://www.twentybn.com/datasets/something-something) or [jester dataset](https://www.twentybn.com/datasets/something-something) or [charades dataset](http://allenai.org/plato/charades/). Decompress them into some folder. Use [process_dataset.py](process_dataset.py) to generate the index files for train, val, and test split. Finally properly set up the train, validatin, and category meta files in [datasets_video.py](datasets_video.py).

### Code

Core code to implement the Temporal Relation Network module is [TRNmodule](TRNmodule.py). It is plug-and-play on top of the TSN.



### Pretrained models and demo code

* Download pretrained models on [Something-Something](https://www.twentybn.com/datasets/something-something), [Jester](https://www.twentybn.com/datasets/jester), and [Moments in Time](http://moments.csail.mit.edu/)

```bash
cd pretrain
./download_models.sh
```

* Download sample video and extracted frames. There will be mp4 video file and a folder containing the RGB frames for that video.

```bash
cd sample_data
./download_sample_data.sh
```

* Test pretrained model on mp4 video file with jester dataset

![alt text](sample_data/abs_thumbs_up.gif "Thumbs UP")


```bash
python test_video.py --arch BNInception --dataset jester     --weight pretrain/TRN_jester_RGB_BNInception_TRNmultiscale_segment8_best.pth.tar /
    --video_file sample_data/VID_20180517_164640.mp4 --rendered_output sample_data/predicted_video.mp4

RESULT ON /home/archimedes/Downloads/VID_20180517_164640.mp4
0.635 -> Thumb Up
0.360 -> Doing other things
0.001 -> Thumb Down
0.001 -> Zooming Out With Two Fingers
0.001 -> Pulling Hand In

```

The command above uses `ffmpeg` to extract frames from the supplied video `--video_file` and optionally generates a new video `--rendered_output` from the frames used to make the prediction with the predicted category in the top-left corner.


### Training and Testing

* The command to train single scale TRN

```bash
CUDA_VISIBLE_DEVICES=0,1 python main.py something RGB \
                     --arch BNInception --num_segments 3 \
                     --consensus_type TRN --batch-size 64
```

* The command to train multi-scale TRN
```bash
CUDA_VISIBLE_DEVICES=0,1 python main.py something RGB \
                     --arch BNInception --num_segments 8 \
                     --consensus_type TRNmultiscale --batch-size 64
```

* The command to test the single scale TRN

```bash
python test_models.py something RGB model/TRN_something_RGB_BNInception_TRN_segment3_best.pth.tar \
   --arch BNInception --crop_fusion_type TRN --test_segments 3
```

* The command to test the multi-scale TRN

```bash
python test_models.py something RGB model/TRN_something_RGB_BNInception_TRNmultiscale_segment8_best.pth.tar \
   --arch BNInception --crop_fusion_type TRNmultiscale --test_segments 8
```



### Reference:
B. Zhou, A. Andonian, and A. Torralba. Temporal Relational Reasoning in Videos. arXiv:1711.08496, 2017. [PDF](https://arxiv.org/pdf/1711.08496.pdf)
```
@article{zhou2017temporalrelation,
    title = {Temporal Relational Reasoning in Videos},
    author = {Zhou, Bolei and Andonian, Alex and Torralba, Antonio},
    journal={arXiv:1711.08496},
    year={2017}
}
```

### Acknowledgement
This repo is heavily influenced from metalbubble/TRN-pytorch. This temporal relation network is plug-and-play on top of the [TSN-Pytorch](https://github.com/yjxiong/temporal-segment-networks), but it could be extended to other network architectures easily. We thank Yuanjun Xiong for releasing TSN-Pytorch codebase. Something-something dataset and Jester dataset are from [TwentyBN](https://www.twentybn.com/), we really appreciate their effort to build such nice video datasets. Please refer to [their dataset website](https://www.twentybn.com/datasets/something-something) for the proper usage of the data.
