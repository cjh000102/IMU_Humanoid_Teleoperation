# GlobalPose Setting for Ubuntu
GlobalPose를 Ubuntu에서 사용하기 위한 세팅
(우분투 22.04 / Python 3.8 / CUDA 11.8 기준)
## 1. Git Clone
```
git clone https://github.com/Xinyu-Yi/GlobalPose
git clone https://github.com/Xinyu-Yi/carticulate
```

## 2. 라이브러리 & 패키지
### 1) 시스템 패키지 (apt)
```
sudo apt update && sudo apt install -y build-essential git pkg-config libsuitesparse-dev libfmt-dev libomp-dev
```

### 2) 빌드 툴
```
python -m pip install -U pip setuptools wheel "cmake>=3.27" ninja
```

### 3) C++ 의존
#### 3-1) Eigen 3.4
```
conda install -c conda-forge "eigen==3.4.*" -y
```
#### 3-2) Sophus
```
git clone https://github.com/strasdat/Sophus.git
cd Sophus && rm -rf build && mkdir build && cd build
cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DSOPHUS_BUILD_TESTS=OFF -DBUILD_SOPHUS_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX="$TP/sophus-1" -DEigen3_DIR="$CONDA_PREFIX/share/eigen3/cmake" ..
cmake --build . --parallel 1 && sudo cmake --install . && sudo ldconfig
```
#### 3-3) g2o
```
git clone https://github.com/RainerKuemmerle/g2o.git
cd g2o && rm -rf build && mkdir build && cd build
cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DG2O_BUILD_EXAMPLES=OFF -DG2O_BUILD_APPS=OFF -DG2O_USE_OPENGL=OFF -DCMAKE_INSTALL_PREFIX="$TP/g2o-0.2" ..
cmake --build . --parallel 1 && sudo cmake --install . && sudo ldconfig
```

### 4) Python 의존
```
pip install -U "pybind11>=2.11"
pip install --upgrade --force-reinstall "numpy==1.23.5"
pip install chumpy scipy opencv-python yacs tqdm PyYAML vctoolkit==0.1.5.39 numpy-quaternion tensorboard "thop==0.1.1.post2209072238" "qpsolvers[open_source_solvers]" "protobuf<4" "lit<17"
pip install torch==2.0.1 torchvision==0.15.2 --index-url https://download.pytorch.org/whl/cu118
```
## 3. carticulate 빌드
```
PYBIND_DIR=$(python -m pybind11 --cmakedir)

git clone https://github.com/Xinyu-Yi/carticulate.git
cd carticulate
```

carticulate/CMakeLists.txt 수정

kinematic_optimizer.cpp 수정

```
rm -rf build && mkdir build && cd build

cmake -G Ninja -DPython3_EXECUTABLE=$(which python) -DCMAKE_BUILD_TYPE=Release ..
cmake --build . --parallel 1
```

carticulate/build/python/~.so 파일 GlobalPose/carticulate/ 로 이동
