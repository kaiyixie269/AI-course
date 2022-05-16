---
layout: post
title: PC Software Installation
author: [Richard Kuo]
category: [Lecture]
tags: [jekyll, ai]
---

PC Software Installation: Editor, GitBash, Python3.9, Anaconda3, CUDA & CuDNN, Tensorflow & PyTorch installation. 

---
## PC Software Installation
1. Editor
2. OS
3. GPU
4. Python
5. Tensorflow/Pytorch

---
### Editor
**Notepad++** (for Windows)<br>
Download from [https://notepad-plus-plus.org/downloads/](https://notepad-plus-plus.org/downloads/)

**nano** (for Ubuntu / MacOS)<br>
**vim** (for Ubuntu / MacOS)<br>

---
### Terminal
**Git for Windows**<br>
Download from [https://gitforwindows.org/](https://gitforwindows.org/)
![](https://gitforwindows.org/img/gw1.png)

**[Linux Command 命令列指令與基本操作入門教學](https://blog.techbridge.cc/2017/12/23/linux-commnd-line-tutorial/)**<br>
* `ls -l` (列出目錄檔案)<br>
* `cd ~` (換目錄)<br>
* `mkdir new` (產生新檔案夾)<br>
* `rm file_name` (移除檔案)<br>
* `rm –rf directory_name` (移除檔案夾)<br>
* `df .` (顯示SD卡已用量)<br>
* `du –sh directory` (查看某檔案夾之儲存用量)<br>
* `free` (檢查動態記憶體用量)<br>
* `ps –a`   (列出正在執行的程序)<br>
* `kill -9 567`  (移除程序 id=567)<br>
* `cat /etc/os-release` (列出顯示檔案內容，此檔案是作業系統版本)<br>
* `vi file_name` (編輯檔案)<br>
* `nano file_name` (編輯檔案)<br>
* `clear` (清除螢幕顯示)<br>
* `history` (列出操作記錄)<br>

---
### GPU acceleration
* [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit) 
  - [CUDA installation](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html)
* [CuDNN](https://developer.nvidia.com/cudnn)
  - [cuDNN installation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)
![](https://developer.nvidia.com/sites/default/files/akamai/cudnn/cudnn_chart.png)

---
### Python3
**Python3.9 for Windows**<br>
* Go to [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)
* Download [Windows embeddable package (64-bit)](https://www.python.org/ftp/python/3.9.12/python-3.9.12-embed-amd64.zip) of Python3.9.12
* [新手踏入Python第零步-安裝Python3.9](https://www.codingspace.school/blog/2021-04-07)
* **Customomize installation** to set directory to `C:\Python39`
![](https://www.codingspace.school/static/blog/img/content/2021-04-07/vzR7KLP.webp)
<br>

**Python3.8.10 on Ubuntu 20.04 LTS**<br>
`$ python3 -V`<br>

---
### Anaconda3  
*(Python 環境安裝用)*<br>

**Anaconda3 on Windows**<br>
* Download [Distribution](https://www.anaconda.com/products/distribution)
  - [Anaconda3 installer for Windows](https://docs.anaconda.com/anaconda/install/hashes/win-3-64/)
* [Python 初學者的懶人包 Anaconda 下載與安裝](https://walker-a.com/archives/6260)<br>

**Anaconda3 on Ubuntu**<br>
[How to Install Anaconda on Ubuntu 18.04 and 20.04](https://phoenixnap.com/kb/how-to-install-anaconda-ubuntu-18-04-or-20-04)<br>
* download Anaconda3<br>
`$ curl -O https://repo.anaconda.com/archive/Anaconda3-2022.05-Linux-x86_64.sh`<br>
* install Anaconda3<br>
`$ bash Anaconda3-2021.11-Linux-x86_64.sh`<br>
* create env<br>
`(base) $ conda create -n tensor python=3.9`<br>
* activate env<br>
`(base) $ conda activate tensor`<br>
* deactivate env<br>
`(tensor) $ conda deactivate`<br>
* remove an env<br>
`(base) $ conda-env remove -n tensor`<br> 

---
### Python packages
* Open GitBash / Ubuntu Terminal<br>
`python3 -V`
`python3 –m pip install --upgrade pip`<br>
`pip -V`
`pip install jupyter`<br>
`pip install pandas`<br>
`pip install matplotlib pillow imutils`<br>
`pip install opencv-python`<br>
`pip install scikit-learn`<br>
`git clone https://github.com/rkuo2000/cv2` (程式範例）<br>
`git clone https://github.com/rkuo2000/tf` （程式範例）<br>

---
### Tensorflow
1. using pip to install tensorflow / tensorflow-gpu
`pip install tensorflow`<br>
or<br>
`pip install tensorflow-gpu`<br>

2. using anaconda to install tensorflow / tensorflow-gpu
`$ conda activate tensor`<br>
`(tensor) $ conda install tensorflow`<br>
or <br>
`(tensor) $ conda install tensorflow-gpu`<br>

---
### PyTorch
* [PyTorch get-started](https://pytorch.org/get-started/locally/)<br>
`pip install torch torchvision`<br>


*This site was last updated {{ site.time | date: "%B %d, %Y" }}.*
