## Superpoint Network for Point Cloud Oversegmentation  

by Le Hui, Jia Yuan, Mingmei Cheng, Jin Xie, Xiaoya Zhang, and Jian Yang



#####  This is a rough README.md, and we'll work through it. !!!

# Damien installation notes

```bash
# module load gpu
module load v100-32g
#module load a100
module load cuda/12.2.1

export LD_LIBRARY_PATH=/apps/opt/spack/linux-ubuntu20.04-x86_64/gcc-9.3.0/cuda-12.2.1-762mhumcr6r5qnnzu4polhx65hthh6iv/lib64:$LD_LIBRARY_PATH


conda create -n spnet python=3.6.6
conda activate spnet

pip install torch==1.4.0
#torch               1.4.0
#torchnet            0.0.4


conda install -c anaconda boost -y
conda install -c omnia eigen3 -y
conda install eigen -y
conda install -c r libiconv -y

module load cmake
cd lib/ply_c
cmake . -DPYTHON_LIBRARY=$CONDA_PREFIX/lib/libpython3.6m.so -DPYTHON_INCLUDE_DIR=$CONDA_PREFIX/include/python3.6m -DBOOST_INCLUDEDIR=$CONDA_PREFIX/include -DEIGEN3_INCLUDE_DIR=$CONDA_PREFIX/include/eigen3
make
cd ../../

cd lib/pointops
python setup.py install
cd ../../

pip install tensorboardX h5py pyyaml pillow plyfile torchnet transforms3d scikit-learn tqdm 

# Rate controls the pts/sp ratio for FPS and k-means
RATE=0.0069
sh tool/sh_test.sh s3dis 20220121 config/spnet.yaml 850 $RATE


for RATE in 0.0001 0.0005 0.001 0.005 0.007 0.01 0.05 0.1
do
    sh tool/sh_test.sh s3dis 20220121 config/spnet.yaml 850 $RATE
done


for RATE in 0.008 0.009 0.02 0.03 0.04
do
    sh tool/sh_test.sh s3dis 20220121 config/spnet.yaml 850 $RATE
done

```

Installation with more recent python version to be able to run on more 
recent torch, to be able to run on A100.

```bash
# module load gpu
#module load v100-32g
module load a100
module load cuda/12.2.1

export LD_LIBRARY_PATH=/apps/opt/spack/linux-ubuntu20.04-x86_64/gcc-9.3.0/cuda-12.2.1-762mhumcr6r5qnnzu4polhx65hthh6iv/lib64:$LD_LIBRARY_PATH

conda create -n spnet_39 python=3.9 -y
conda activate spnet_39

pip install torch==2.7.1
#torch               1.4.0
#torchnet            0.0.4

conda install -c anaconda boost -y
conda install -c omnia eigen3 -y
conda install eigen -y
conda install -c r libiconv -y

module load cmake
cd lib/ply_c
cmake . -DPYTHON_LIBRARY=$CONDA_PREFIX/lib/libpython3.9.so -DPYTHON_INCLUDE_DIR=$CONDA_PREFIX/include/python3.9 -DBOOST_INCLUDEDIR=$CONDA_PREFIX/include -DEIGEN3_INCLUDE_DIR=$CONDA_PREFIX/include/eigen3
make
cd ../../

# THIS IS THE INSTALLATION PAIN POINT. I CANNOT COMPILE THIS WITH 
# TORCH>=2; ONLY 1.4.0. tHIS PREVENTS USING MORE RECENT GPU ARCHITECTURES 
# (eg A100/H100). SO I AM STUCK WITH MEMORY ERRORS WHEN RUNNING INTO 
# MEMORY ISSUES...
cd lib/pointops
python setup.py install
cd ../../

pip install tensorboardX h5py pyyaml pillow plyfile torchnet transforms3d scikit-learn tqdm 
```


```
Train result at epoch [851/5000]: ASA/BR/BP 0.4767/51.3566/11.6720
cnt_room: 68 cnt_sp: 896 avg_sp: 13.176470588235293
cnt_sp_act: 896 avg_sp_act: 13.176470588235293
ASA: 47.7
Oracle OA: 47.7
Oracle mIoU: 14.5
FPS / k-means rate: 0.0001
Total partition time WITHOUT SPG CLASSIF: 144.581

Train result at epoch [851/5000]: ASA/BR/BP 0.8025/65.9005/13.4759
cnt_room: 68 cnt_sp: 4615 avg_sp: 67.86764705882354
cnt_sp_act: 4603 avg_sp_act: 67.69117647058823
ASA: 80.2
Oracle OA: 80.2
Oracle mIoU: 49.3
FPS / k-means rate: 0.0005
Total partition time WITHOUT SPG CLASSIF: 132.853

Train result at epoch [851/5000]: ASA/BR/BP 0.8729/68.8595/13.9309
cnt_room: 68 cnt_sp: 9263 avg_sp: 136.22058823529412
cnt_sp_act: 9174 avg_sp_act: 134.91176470588235
ASA: 87.3
Oracle OA: 87.3
Oracle mIoU: 63.5
FPS / k-means rate: 0.001
Total partition time WITHOUT SPG CLASSIF: 125.821

Train result at epoch [851/5000]: ASA/BR/BP 0.9575/79.1453/13.6364
cnt_room: 68 cnt_sp: 46460 avg_sp: 683.2352941176471
cnt_sp_act: 45593 avg_sp_act: 670.4852941176471
ASA: 95.7
Oracle OA: 95.7
Oracle mIoU: 86.8
FPS / k-means rate: 0.005
Total partition time WITHOUT SPG CLASSIF: 122.797

Train result at epoch [851/5000]: ASA/BR/BP 0.9654/82.1226/13.1064
cnt_room: 68 cnt_sp: 65064 avg_sp: 956.8235294117648
cnt_sp_act: 63581 avg_sp_act: 935.0147058823529
ASA: 96.5
Oracle OA: 96.5
Oracle mIoU: 89.7
FPS / k-means rate: 0.007
Total partition time WITHOUT SPG CLASSIF: 127.976

Train result at epoch [851/5000]: ASA/BR/BP 0.9680/83.1743/12.8688
cnt_room: 68 cnt_sp: 74354 avg_sp: 1093.4411764705883
cnt_sp_act: 72530 avg_sp_act: 1066.6176470588234
ASA: 96.8
Oracle OA: 96.8
Oracle mIoU: 90.5
FPS / k-means rate: 0.008
Total partition time WITHOUT SPG CLASSIF: 133.736

Train result at epoch [851/5000]: ASA/BR/BP 0.9702/84.0403/12.6392
cnt_room: 68 cnt_sp: 83656 avg_sp: 1230.235294117647
cnt_sp_act: 81566 avg_sp_act: 1199.5
ASA: 97.0
Oracle OA: 97.0
Oracle mIoU: 91.1
FPS / k-means rate: 0.009
Total partition time WITHOUT SPG CLASSIF: 136.768

Train result at epoch [851/5000]: ASA/BR/BP 0.9716/84.7251/12.4060
cnt_room: 68 cnt_sp: 92959 avg_sp: 1367.0441176470588
cnt_sp_act: 90576 avg_sp_act: 1332.0
ASA: 97.2
Oracle OA: 97.2
Oracle mIoU: 91.6
FPS / k-means rate: 0.01
Total partition time WITHOUT SPG CLASSIF: 128.834

```


### Project Code

#### Requirements

* basic environment
    ```
    Python 3.6.6
    Pytorch 1.4.0
    CUDA 10.1
    ```

* compile the "libply_c" library (Please refer to [SPG](https://github.com/loicland/superpoint_graph))

  ```
  CONDAENV=YOUR_CONDA_ENVIRONMENT_LOCATION
  cd libs/ply_c
  cmake . -DPYTHON_LIBRARY=$CONDAENV/lib/libpython3.6m.so -DPYTHON_INCLUDE_DIR=$CONDAENV/include/python3.6m -DBOOST_INCLUDEDIR=$CONDAENV/include -DEIGEN3_INCLUDE_DIR=$CONDAENV/include/eigen3
  make
  cd ../../
  ```
  
* build the ops

  ```
  cd libs/pointops && python setup.py install && cd ../../
  
  Note that this may take a long time.
  ```


#### Training and Evaluation

* To train and evaluate SPNet, run the following command:

    ```
    # Train & Eval
    # Note that you should change the paths in the yaml file.
    
    sh tool/sh_train.sh s3dis 20220121 config/spnet.yaml
    
    sh tool/sh_test.sh s3dis 20220121 config/spnet.yaml 850
    ```


#### Citation

If you find the code or trained models useful, please consider citing:

```
@inproceedings{hui2021spnet,
  title={Superpoint Network for Point Cloud Oversegmentation},
  author={Hui, Le and Yuan, Jia and Cheng, Mingmei and Xie, Jin and Yang, Jian},
  booktitle={ICCV},
  year={2021}
}

```

#### Acknowledgement

Our code refers to [SPG](https://github.com/loicland/superpoint_graph) and [PointWeb](https://github.com/hszhao/PointWeb). Many thanks to SPG for a great work.