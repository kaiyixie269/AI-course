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
* [RGB和CMYK的色彩模式差異](https://jessielab.com/rgb%E5%92%8Ccmyk%E7%9A%84%E8%89%B2%E5%BD%A9%E6%A8%A1%E5%BC%8F%E5%B7%AE%E7%95%B0%EF%BC%8C%E9%97%9C%E6%96%BC%E8%A8%AD%E8%A8%88%E9%87%8D%E8%A6%81%E7%9A%84%E5%B0%8F%E4%BA%8B/)

  RGB 又稱三原色系統，一張彩色的圖片可以由三張單一顏色的值組合而成，分別是紅、藍及綠。

![](https://scontent.ftpe7-4.fna.fbcdn.net/v/t1.15752-9/322322337_703527114646949_6883571111480502282_n.png?_nc_cat=103&ccb=1-7&_nc_sid=ae9488&_nc_ohc=cFieXciHPtAAX__oWB5&_nc_ht=scontent.ftpe7-4.fna&oh=03_AdT_tv_vdrpk56JivEbQF6EtXoVLydF8H-sfGQWltZJYMQ&oe=63D910D9)

**HSV Color system**
* [HSL and HSV](https://zh.wikipedia.org/zh-tw/HSL%E5%92%8CHSV%E8%89%B2%E5%BD%A9%E7%A9%BA%E9%97%B4)

  HSV 同樣與 RGB 為顏色表達方式，但不同的是 HSV 分為三種，分別是 Hue(色調)、Saturation(飽和度)以及Value(亮度)，利用這三者的值來表達顏色。

![](https://scontent.ftpe7-4.fna.fbcdn.net/v/t1.15752-9/322600269_717995306710863_4803001096202598556_n.png?_nc_cat=105&ccb=1-7&_nc_sid=ae9488&_nc_ohc=sDNiSE6LbUcAX82Vkhg&_nc_ht=scontent.ftpe7-4.fna&oh=03_AdS4mmYV-2f3Sq505-m5akTltBLcE-l3S81RT-aoxRYWVg&oe=63D91924)

  HSV 相較於 RGB 更容易過濾出單一的顏色，所以在執行時必須由 RGB 轉為 HSV。

  顏色過濾示意

![](https://scontent.ftpe7-1.fna.fbcdn.net/v/t1.15752-9/322354254_830394458021770_6704450352573886447_n.png?_nc_cat=100&ccb=1-7&_nc_sid=ae9488&_nc_ohc=OSdzHaTS4k0AX9jWMCG&_nc_ht=scontent.ftpe7-1.fna&oh=03_AdQXDVlsp40xWDjXAbbHnn_yLnaFkxDlN3H-leleIjk0_g&oe=63D90719)

### 程式說明

程式主要分為**過濾顏色**，**建立輪廓**及**標示出水點**

---

**讀取視訊鏡頭**

  使用 VideoCapture 的函式來讀取
```
cap = cv2.VideoCapture(0)
while True:
    ret, frame = cap.read()
    if ret:
        cv2.imshow('video', frame)
    else:
        break
    if cv2.waitKey(1) == ord('q'):
        break
 ```
![](https://scontent.ftpe7-1.fna.fbcdn.net/v/t1.15752-9/321890406_932923970928537_9062583774154398667_n.png?_nc_cat=106&ccb=1-7&_nc_sid=ae9488&_nc_ohc=uqR-JeAt938AX_5DzXS&_nc_ht=scontent.ftpe7-1.fna&oh=03_AdQUuZdZdF2udRFf1ZnaoYWVdP01YknRpuMNmpAY-j-yzg&oe=63D927D1)

**偵測並過濾顏色**

讀取每一偵的畫面
```
ret, img = cap.read()
```
將每一偵畫面轉為 HSV
```
hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
```
建立一個動態的控制條來調整 HSV 的各項數值以過濾顏色
```
cv2.namedWindow('TrackBar')
cv2.resizeWindow('TrackBar', 640, 320)

cv2.createTrackbar('Hue Min', 'TrackBar', 0, 179, empty)
cv2.createTrackbar('Hue Max', 'TrackBar', 179, 179, empty)
cv2.createTrackbar('Sat Min', 'TrackBar', 0, 255, empty)
cv2.createTrackbar('Sat Max', 'TrackBar', 255, 255, empty)
cv2.createTrackbar('Val Min', 'TrackBar', 0, 255, empty)
cv2.createTrackbar('Val Max', 'TrackBar', 255, 255, empty)
```
即時取得改變後的數值
```
while True:
    h_min = cv2.getTrackbarPos('Hue Min', 'TrackBar')
    h_max = cv2.getTrackbarPos('Hue Max', 'TrackBar')
    s_min = cv2.getTrackbarPos('Sat Min', 'TrackBar')
    s_max = cv2.getTrackbarPos('Sat Max', 'TrackBar')
    v_min = cv2.getTrackbarPos('Val Min', 'TrackBar')
    v_max = cv2.getTrackbarPos('Val Max', 'TrackBar')
    print(h_min, h_max, s_min, s_max, v_min, v_max)
```
過濾後顯示即時的圖片
```
 lower = np.array([h_min, s_min, v_min])
    upper = np.array([h_max, s_max, v_max])

    mask = cv2.inRange(hsv, lower, upper)
    result = cv2.bitwise_and(img, img, mask=mask)

    cv2.imshow('img', img)
    #cv2.imshow('hsv', hsv)
    cv2.imshow('mask', mask)
    cv2.imshow('reslut', result)
    cv2.waitKey(1)
```
![](https://scontent.xx.fbcdn.net/v/t1.15752-9/322262274_720590975999374_8670511542716617065_n.png?stp=dst-png_p206x206&_nc_cat=108&ccb=1-7&_nc_sid=aee45a&_nc_ohc=BEFXQJtc2MEAX9JWr_E&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdS7IykRQxlnY7ezXX7RlxGMErnBn9g_hh73G0E1uPyGFQ&oe=63D91C50)              ![](https://scontent.xx.fbcdn.net/v/t1.15752-9/321977649_730547511840179_1477310677725218409_n.png?stp=dst-png_p206x206&_nc_cat=106&ccb=1-7&_nc_sid=aee45a&_nc_ohc=2aJJ-c8voYEAX_a6tYf&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdTAq-5lmV5G_vRcYKTlaf5z8ftamZE83AaXsE6eaiMPDQ&oe=63D918CB)

過濾後取得顏色的六項數值

**偵測顏色輪廓**

偵測輪廓
```
contours, hierarchy = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE)
```
劃出輪廓
```
cv2.drawContours(imgContour, cnt, -1, (255, 0, 0), 4)
```
使用多邊形近似輪廓
```
peri = cv2.arcLength(cnt, True)
vertices = cv2.approxPolyDP(cnt, peri * 0.02, True)
```
得出這個多邊形的四項座標
```
x, y, w, h = cv2.boundingRect(vertices)
```
![](https://scontent.xx.fbcdn.net/v/t1.15752-9/322854187_2286169691550731_3726623165607082009_n.png?stp=dst-png_p206x206&_nc_cat=102&ccb=1-7&_nc_sid=aee45a&_nc_ohc=5goDnG_ThX8AX-4lTE8&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdRLKe6Z2-DhgMmZ7Q5uibmCS6DmXCzN5YFcKfo8ujvopg&oe=63D93F53)

**標出墨水出水點**

出水點座標

```
return x+w//2, y
```
![](https://scontent.xx.fbcdn.net/v/t1.15752-9/318450774_869177634320911_6055296241311174056_n.png?stp=dst-png_p206x206&_nc_cat=100&ccb=1-7&_nc_sid=aee45a&_nc_ohc=3n0tuGzf-WsAX_Cf7Zw&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdQktIDO0lSP2uiN19VXlbm0bFCvjL8I8YqxBEd7VbYFGQ&oe=63D94401)

**加入另外兩個顏色黃跟綠**

黃與綠的六項數值
```
penColorHSV = [[103, 102, 104, 133, 255, 255],
               [70, 118, 76, 108, 255, 245],
               [25, 93, 134, 56, 255, 255]]
```
![](https://scontent.xx.fbcdn.net/v/t1.15752-9/316841778_708471490659550_2012643439193290649_n.png?stp=dst-png_p206x206&_nc_cat=106&ccb=1-7&_nc_sid=aee45a&_nc_ohc=iBeck9iqxq0AX-hdpQG&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdSjP-96o5HSjmN9r1qBi5-QyPns-yey7BY0M894oXWDKg&oe=63D92D84)

將筆尖出水點改為對應顏色，並將輪廓刪去較為明顯。
```
penColorBGR = [[255, 0, 0],
               [0, 255, 0],
               [0, 255, 255]]
```
![](https://scontent.xx.fbcdn.net/v/t1.15752-9/319130721_533708955361233_6011164299438475289_n.png?stp=dst-png_p206x206&_nc_cat=103&ccb=1-7&_nc_sid=aee45a&_nc_ohc=K4vb1NT_gPcAX-FVTIl&_nc_ad=z-m&_nc_cid=0&_nc_ht=scontent.xx&oh=03_AdQrWCS_y8fsfgaQyt3pYWgfSysHJsAcL23PzYireqFnoA&oe=63D93092)
