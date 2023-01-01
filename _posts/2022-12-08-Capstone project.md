---
layout: post
title: Virtual pen
author: [劉子維·謝凱亦]
category: [Lecture]
tags: [ai]
---
### Virtual pen (虛擬筆)
**期末專題描述**

透過 Python OpenCV 圖像識別及視訊鏡頭偵測顏色及輪廓並使用各種顏色的筆來寫出彩色的字及線條

---
**開發過程:**

1. 讀取鏡頭
2. 偵測並過濾顏色範圍
3. 偵測不同顏色筆的筆尖並選取輪廓
4. 同樣的作法套用在其他顏色上
5. 做出墨水出水點

---
**執行環境**
 這次使用的環境在軟硬體上要求非常低，全部在編譯器上即可完成，唯一的要求必須要有**視訊鏡頭**。
 
 以下是使用版本供參考
 
 * python 3.11
 * opencv
 * numby
 
 直接從編譯器安裝即可

### 編譯器

* Pycharm community Edition 2022.3.1

![](https://scontent.ftpe7-1.fna.fbcdn.net/v/t1.15752-9/322738771_468128355307488_1754149219618568078_n.png?_nc_cat=106&ccb=1-7&_nc_sid=ae9488&_nc_ohc=efMgIWEhYmgAX9CGJn3&_nc_ht=scontent.ftpe7-1.fna&oh=03_AdTdn_6JR2vHp8_GjdyfXD39_Y0KovOau5H9lbCowRvOoQ&oe=63D8FFA9)

----
### 基本色彩介紹
**RGB Color system**

RGB 又稱三原色系統，一張彩色的圖片可以由三張單一顏色組合而成，分別是紅、藍及綠。

![](https://scontent.ftpe7-4.fna.fbcdn.net/v/t1.15752-9/322322337_703527114646949_6883571111480502282_n.png?_nc_cat=103&ccb=1-7&_nc_sid=ae9488&_nc_ohc=cFieXciHPtAAX__oWB5&_nc_ht=scontent.ftpe7-4.fna&oh=03_AdT_tv_vdrpk56JivEbQF6EtXoVLydF8H-sfGQWltZJYMQ&oe=63D910D9)
### Pose Recognition (姿態辨識)

**專題實作步驟:**
1. 建立身體動作之姿態照片資料集 (例如：5 poses , take 20 pictures of each pose)<br>
2. 始用**MMPose** 辨識出照片中的各姿勢之身體關鍵點 (use MMPose convert 16 keypoints (x,y) of each pose)<br>
3. 產生姿態關鍵點資料集 x_train.append(pose_keypoints) ( x_train.shape = (20x5, 16, 2), y_train.shape= (20x5, 1) )<br>
4. 建立DNN模型並訓練模型, 然後下載模型檔`pose_dnn.h5`至PC <br>
5. 於PC建立帶camera輸入之服務器程式, 載入模型`pose_dnn.h5`進行姿態動作辨識 <br>

**模型建構與訓練之程式樣本** (PC or Kaggle)<br>

```
input_shape=(16,2)
num_classes=5

inputs = layers.Input(shape=input_shape)
x = layers.Dense(128)(inputs)
outputs = layers.Dense(num_classes, activation="softmax")(x)
model = models.Model(inputs=inputs, outputs=outputs)

models.compile(loss = 'categorical_crossentropy', optimizer = 'adam' , metrics = ['accuracy'])

history = model.fit(x_train, y_train, batch_size=1, epochs=20, validation_data=(x_test, y_test))
models.save_model(model, 'pose_dnn.h5')
```

**姿態辨識服務器之程式樣本** (PC with Camera)<br>

```
model = models.load_model('models/pose_dnn.h5')
labels = ['stand', 'raise-right-arm', 'raise-left-arm', 'cross arms','both-arms-left']

cap = cv2.VideoCapture(0)

while(cap.isOpened()):
    ret, frame = cap.read()
    image = cv2.cvtColor(frame,cv2.COLOR_BGR2RGB)
    
    mmdet_results = inference_detector(det_model, image) # 人物偵測產生BBox
    person_results = process_mmdet_results(mmdet_results, args.det_cat_id) # 記住人物之BBox  
    pose_results, returned_outputs = inference_top_down_pose_model(...) # 感測姿態產生pose keypoints
    
    x_test = np.array(preson_results).reshape(1,16,2) # 將Keypoints List 轉成 numpy Array
    preds = model.fit(x_test) # 辨識姿態動作
    maxindex = int(np.argmax(preds))
    txt = labels[maxindex]
    print(txt)
```

---
### Head Pose Estimation
**Kaggle:** [rkuo2000/head-pose-estimation](https://kaggle.com/rkuo2000/head-pose-estimation)<br>
<iframe width="652" height="489" src="https://www.youtube.com/embed/BHwHmCUHRyQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---
### VTuber-Unity 
**Head-Pose-Estimation + Face-Alignment + GazeTracking**<br>

<u>Build-up Steps</u>:
1. Create a character: **[VRoid Studio](https://vroid.com/studio)**
2. Synchronize the face: **[VTuber_Unity](https://github.com/kwea123/VTuber_Unity)**
3. Take video: **[OBS Studio](https://obsproject.com/download)**
4. Post-processing:
 - Auto-subtitle: **[Autosub](https://github.com/kwea123/autosub)**
 - Auto-subtitle in live stream: **[Unity_live_caption](https://github.com/kwea123/Unity_live_caption)**
 - Encode the subtitle into video: **[小丸工具箱](https://maruko.appinn.me/)**
5. Upload: YouTube
6. [Optional] Install CUDA & CuDNN to enable GPU acceleration
7. To Run <br>
`$git clone https://github.com/kwea123/VTuber_Unity` <br>
`$python demo.py --debug --cpu` <br>

<p align="center"><img src="https://github.com/kwea123/VTuber_Unity/blob/master/images/debug_gpu.gif?raw=true"></p>

---
### OpenVtuber
<u>Build-up Steps</u>:
* Repro [Github](https://github.com/1996scarlet/OpenVtuber)<br>
`$git clone https://github.com/1996scarlet/OpenVtuber`<br>
`$cd OpenVtuber`<br>
`$pip3 install –r requirements.txt`<br>
* Install node.js for Windows <br>
* run Socket-IO Server <br>
`$cd NodeServer` <br>
`$npm install express socket.io` <br>
`$node. index.js` <br>
* Open a browser at  http://127.0.0.1:6789/kizuna <br>
* PythonClient with Webcam <br>
`$cd ../PythonClient` <br>
`$python3 vtuber_link_start.py` <br>

<p align="center"><img src="https://camo.githubusercontent.com/83ad3e28fa8a9b51d5e30cdf745324b09ac97650aea38742c8e4806f9526bc91/68747470733a2f2f73332e617831782e636f6d2f323032302f31322f31322f72564f33464f2e676966"></p>

---
### Hand Pose
![](https://github.com/facebookresearch/InterHand2.6M/blob/main/assets/teaser.gif?raw=true)
**Dataset:** [InterHand2.6M](https://github.com/facebookresearch/InterHand2.6M)<br>

1. Download pre-trained InterNet from [here](https://drive.google.com/drive/folders/1BET1f5p2-1OBOz6aNLuPBAVs_9NLz5Jo?usp=sharing)
2. Put the model at `demo` folder
3. Go to `demo` folder and edit `bbox` in [here](https://github.com/facebookresearch/InterHand2.6M/blob/5de679e614151ccfd140f0f20cc08a5f94d4b147/demo/demo.py#L74)
4. run `python demo.py --gpu 0 --test_epoch 20`
5. You can see `result_2D.jpg` and 3D viewer.

**Camera positios visualization demo**
1. `cd tool/camera_visualize`
2. Run `python camera_visualize.py`


<br>
<br>

*This site was last updated {{ site.time | date: "%B %d, %Y" }}.*

