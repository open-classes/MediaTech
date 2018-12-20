# code-py: 创建多版本python环境

## 1 根据操作系统安装最新版本的python

- 下载python注意匹配操作系统和CPU架构
- 从官网: https://www.python.org/ 下载并安装最新的3.x版本的python。假设安装到C:/MediaDev/python-env/python-3.6.7
- 从官网: https://www.python.org/ 下载并安装最新的2.x版本的python。假设安装到C:/MediaDev/python-env/python-2.7.15
- 安装python时, 配置支持tcl/tl，否则GUI无法渲染显示。
- 目录仔细规划好，没有整齐的道场，不好干活哦。

## 2 python多版本兼容

## 2.1 简单兼容多版本python

- 将 C:/MediaDev/python-env/python-3.6.7 加到环境变量(PATH);
- 将 C:/MediaDev/python-env/python-3.6.7/Scripts 加到环境变量(PATH)
- 检查环境变量 >> 高级系统属性 - 高级 - 环境变量 - 系统变量 - PATH
- 环境测试

``` shell
E:\MediaDev\PythonDev> py.exe --help
E:\MediaDev\PythonDev> py.exe -2 --version
E:\MediaDev\PythonDev> py.exe -3 --version
E:\MediaDev\PythonDev> python -m pip install --upgrade pip
```

## 2.2 使用virtualenv配置多版本

``` shell
$  pip --version
$  pip install  virtualenv
$  pip show virtualenv
$  cd E:\MediaDev\PythonDev
$  virtualenv -p E:\MediaDev\PythonEnv\python-2.7.15\python.exe venv-python-2.7

# 查看window-shell运行限制，启用脚本运行
$  get-executionpolicy
$  get-help set-executionpolicy
$  set-executionpolicy remotesigned
$  cd E:\MediaDev\PythonDev
$  source venv-python-2.7/Scripts/activate  (bash-shell方式)
$  .\venv-python-2.7\Scripts\activate (windows方式)
(venv-python-2.7) PS E:\MTMediaDev\MTMediaAI\PythonEnv>
$  deactivate  (bash-shell方式)
```

## 2 安装python科学&人工智能库

- 使用windows shell或者bash shell命名行安装常用python软件包。
- 科学计算软件包: numpy/scipy/pandas/opencv
- 人工智能软件包: tensorflow/pytorch/scikit-learn/mxnet

``` shell
$ pip search numpy
numpy (1.15.4)  - array processing for numbers, strings, records, and objects.
$ pip install numpy

$ pip search scipy
scipy (1.1.0)   - Scientific Library for Python
$ pip install scipy

$ pip search matplotlib
matplotlib (3.0.2) - Python plotting package
$ pip install matplotlib

$ pip search pandas
pandas (0.23.4) - Powerful data structures for data analysis, time series, and statistics
$ pip install pandas

$ pip search pillow
Pillow (5.3.0) - Python Imaging Library (Fork)
$ pip install pillow

$ pip list
Package         Version
--------------- -------
matplotlib      3.0.2
numpy           1.15.4
pandas          0.23.4
Pillow          5.3.0
pip             18.1
scipy           1.1.0
virtualenv      16.1.0

$ pip search tensorflow
tensorflow (1.12.0)     - TensorFlow is an open source machine learning framework
tensorflow-gpu (1.12.0) - TensorFlow is an open source machine learning framework
$ pip install tensorflow
$ pip install tensorflow-gpu

$ pip install pytorch
# 从 https://pytorch.org/ 获取 gpu版本的安装命令
$ pip3 install http://download.pytorch.org/whl/cpu/torch-1.0.0-cp36-cp36m-win_amd64.whl
$ pip3 install torchvision

$ pip search scikit-learn
scikit-learn (0.20.1) - A set of python modules for machine learning and data mining
$ pip install scikit-learn

$ pip search mxnet
$ pip search mxnet
mxnet (1.3.1)      - MXNet is an ultra-scalable deep learning framework.
mxnet-mkl (1.3.1)  - MXNet is an ultra-scalable deep learning framework.
mxnet-cu91 (1.3.0) - MXNet is an ultra-scalable deep learning framework.
mxnet-cu92 (1.3.1) - MXNet is an ultra-scalable deep learning framework. 
$ pip install mxnet

$ pip list
Package             Version
------------------- ----------
tensorboard         1.12.0
tensorflow          1.12.0
torch               0.4.1
mxnet               1.3.0

matplotlib          3.0.2
numpy               1.14.6
pandas              0.23.4
pip                 18.1
virtualenv          16.1.0
```

备注：查看CUDA版本的方法：控制面板 - 小图标显示 - Nvidia控制面板 - 帮助 - 系统信息 - 组件(CUDA)。哈哈，我的驱动紧跟时代，CUDA-9.2。安装Tensorflow和Pytorch可以安装CUDA-9.2版本的。从可以

## 3 Python库版本维护

https://www.python.org/
python -m pip install --upgrade pip 安装pip安装工具
windows专有python库非官方发布：
https://www.lfd.uci.edu/~gohlke/pythonlibs/#opencv

``` shell
$ pip list
$ pip list --outdated
$ pip install --upgrade package-name
$ pip show matplotlib

$ pip install package-name # latest version
$ pip install package-name==1.0.4 # specific version
$ pip install package-name>=1.0.4' # minimum version

$ pip uninstall chainer
$ pip install chainer --no-cache-dir

$ pip check tensorflow
$ pip uninstall tensorflow
$ pip install tensorflow-gpu
```
