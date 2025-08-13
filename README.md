## Superpoint Network for Point Cloud Oversegmentation  

by Le Hui, Jia Yuan, Mingmei Cheng, Jin Xie, Xiaoya Zhang, and Jian Yang



#####  This is a rough README.md, and we'll work through it. !!!

# Damien installation notes

```bash
# module load gpu
module load v100-32g
module load cuda/12.2.1

export LD_LIBRARY_PATH=/apps/opt/spack/linux-ubuntu20.04-x86_64/gcc-9.3.0/cuda-12.2.1-762mhumcr6r5qnnzu4polhx65hthh6iv/lib64:$LD_LIBRARY_PATH


conda create -n spnet python=3.6.6
conda activate spnet

pip install torch==1.4.0

conda install -c anaconda boost -y
conda install -c omnia eigen3 -y
conda install eigen -y
conda install -c r libiconv -y

module load cmake
cd libs/ply_c
cmake . -DPYTHON_LIBRARY=$CONDA_PREFIX/lib/libpython3.6m.so -DPYTHON_INCLUDE_DIR=$CONDA_PREFIX/include/python3.6m -DBOOST_INCLUDEDIR=$CONDA_PREFIX/include -DEIGEN3_INCLUDE_DIR=$CONDA_PREFIX/include/eigen3
make
cd ../../

pip install tensorboardX h5py pyyaml pillow plyfile torchnet transforms3d scikit-learn tqdm 

# Rate controls the pts/sp ratio for FPS and k-means
RATE=0.0069
sh tool/sh_test.sh s3dis 20220121 config/spnet.yaml 850 $RATE

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