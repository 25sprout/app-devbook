# Designer Support Guide

這篇是給製作APP UI 的 Designer 看的文章。  
將詳細說明怎麼同時設計 Android and iOS UI，及其需要注意的地方。

* 收集到的 Sketch UI kit [看這邊 內部專屬唷](https://drive.google.com/drive/folders/0B4FiKSJ3us2LY1JxbWZWTkxHeXM)
* iOS Design Guideline : [https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html\#//apple\_ref/doc/uid/TP40006556](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556)
* Android Design Guideline [http://developer.android.com/intl/zh-tw/design/index.html](http://developer.android.com/intl/zh-tw/design/index.html)

## 設計師需提供的 APP UI Resource

在你完成每個 APP UI 設計時，你應該提供以下檔案給工程師，**也需要特別注意在 iOS 跟 Android 上不同的畫面**。

懶人包下載位置：[內部專屬唷](https://drive.google.com/drive/folders/0B4FiKSJ3us2LMEdIODBIQUtMUkk)

* **Design File**：自己保留，不一定需要給工程師。
* **Flow**：APP 的 Flow Document，通常我是直接會出一整頁的 sketch PDF，你也可以用別的工具 EX: POP or Xmind，之所以沒有選用後者，是因為通常 UI 會改很很多次，所以後來的更改通常都會懶得上到 POP XD。
* **Resource Folder**：所有會出的檔案
  * **Android**：Android UI assets，需再細分為五個解析度 folder。
  * **iOS**：iOS Assets，不用再細分 folder 檔名會有 @2x 跟沒有 @2x 的差異。
  * **launcher**：所有的 launcher icon 們。
  * **screenshot**：所有畫面的一張一張的圖，APP 工程師 layout UI 用。
  * **color file**：整個 APP 的色碼表：所有用到的 APP 顏色。

---

## 工具Sketch

建議使用 Sketch 開發，因為它可以大量切圖，省去非常多功夫。而且他是全向量的。本章節所提供的懶人包也都會是 Skecth Format

---

## UI 設計尺寸

由於 Android 跟 iOS 螢幕解析度不一致，且 Device 種類太多，所以設計的時候，建議做一個可以放大的基本尺寸。

#### \[ Android \]

使用 `android_UI.sketch` 可以省下很多工夫。

|  | dpi | 學名 | 別名 | 以 360 \* 640 螢幕為例尺寸 | 裝置範例 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1倍 | 160dpi | Medium density | **mdpi** | 360px \* 640px |  |
| 1.5倍 | 240dpi | High density | **hdpi** | 480px \* 800px | Nexus S |
| 2倍 | 320dpi | Extra high density | **xhdpi** | 720px \* 1280px | HTC One X |
| 3倍 | 480dpi |  | **xxhdpi** | 1080px \* 1920px | Nexus 5 HTC One M8 |
| 4倍 | 640dpi |  | **xxxhdpi** | 1440px \* 2560px | Nexus 6 |

**這邊要特別注意，標給 Android 的 margin, padding... 等 Measurement 都必須是以 **`160dpi`** 為 1 倍單位。**

假設你是使用 `360 * 640` 做 UI，有一個 margin 是 10px 那你就要標 `10dp`，有一張圖片大小是 20px _ 20px 那就是 _`20dp`_，但你如果是用 \`720 _ 1280`做 UI 設計，有一個 margin 是 40px，那你就要把`40 / 2`也就是 20dp。依此類推，所以建議你使用 1 倍的`360 \* 640\` 做UI設計。

**問題：假設你拿到一個 Device 是 1200 \* 1920 而且是 320dpi，則你要做 UI 時一倍的螢幕 pixel 是多少呢？**

```
答案是 600 * 900px 為此一倍的大小。因為 320dpi 是 2倍，所以要除以二唷。
1200 / 2 = 600;
1920 / 2 = 960;
```

#### dp & px 轉換公式
```
px = dp * (dpi / 160)
``` 
依據此公式可以得知，如果 `dpi` 剛好為 160 的倍數，結果就會如上面的圖表，`1 dp = (1 or 2 or 3) px`。所以對於 `Nexus5, xxhdpi(480dpi), 1080*1920 px` 來說，一個 `width = 360dp` 的 View 會剛好等於螢幕寬<p>

但依據手機尺寸大小，可能會不一定剛好 `dpi = 160*X`。以 `Nexus 5X, 420dpi, 1080*1920 px`，如果你要畫一個螢幕寬的 View ，你需要設定 `width = 411dp`，也就是說，`Nexus5X` 雖然 pixel 數目跟 `Nexus5` 一樣，但是可以顯示的內容是比較多的

#### Configuration examples(Width DP 參考)
- 320dp: a typical phone screen (240x320 ldpi, 320x480 mdpi, 480x800 hdpi, etc).
- 480dp: a tweener tablet like the Streak (480x800 mdpi).
- 600dp: a 7” tablet (600x1024 mdpi).
- 720dp: a 10” tablet (720x1280 mdpi, 800x1280 mdpi, etc).

```
res/layout/main_activity.xml           # For handsets (smaller than 600dp available width)
res/layout-sw600dp/main_activity.xml   # For 7” tablets (600dp wide and bigger)
res/layout-sw720dp/main_activity.xml   # For 10” tablets (720dp wide and bigger)
```


參考網址：

* 凱西整理建議尺寸：[https://docs.google.com/spreadsheets/d/1b-qyqvTmatyC6gCy71QuKbXE-Wqj\_oxjM5wHfT3Q72M/edit\#gid=0](https://docs.google.com/spreadsheets/d/1b-qyqvTmatyC6gCy71QuKbXE-Wqj_oxjM5wHfT3Q72M/edit#gid=0) \(內部 Only\)
* [https://design.google.com/devices/](https://design.google.com/devices/)
* [http://iconhandbook.co.uk/reference/chart/android/](http://iconhandbook.co.uk/reference/chart/android/)
* Google 建議UI 間距：[https://www.google.com/design/spec/layout/metrics-keylines.html\#metrics-keylines-keylines-spacing](https://www.google.com/design/spec/layout/metrics-keylines.html#metrics-keylines-keylines-spacing)
* 教你認識 DPI : [http://sebastien-gabriel.com/designers-guide-to-dpi/](http://sebastien-gabriel.com/designers-guide-to-dpi/)

#### \[ iOS \]

使用 `ios_UI.sketch` 可以省下很多工夫。Folder 裡面有一個 @2x 的，僅參考用唷。請都改用 `320 * 568` 設計。

|  | 螢幕基本尺寸 | 裝置範例 |
| --- | --- | --- |
| 1倍 | 320 \* 568 | iPhone 5, 5C, 5S, iPod Touch 5g |
| 2倍\(@2x\) | 640 \* 1136 | iPhone 6, iPhone 6s |

* 參考：[http://iosres.com/](http://iosres.com/)
* 參考：[https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html)

## Launcher Icon 尺寸

要跨雙系統需要非常多不同尺寸的launcher icon，詳列如下。  
另外您可以使用 `launcher.sketch` 可以省下很多工夫。

#### \[ Android \]

| 檔名 | 尺寸 |
| --- | --- |
| GooglePlay.png | 512 \* 512 |
| GooglePlayMarket.jpg | 1024 \* 500 |
| GooglePlayMarket.png | 1024 \* 500 |
| ic\_launcher-mdpi.png | 48 \* 48 |
| ic\_launcher-hdpi.png | 72 \* 72 |
| ic\_launcher-xhdpi.png | 96 \* 96 |
| ic\_launcher-xxhdpi.png | 144 \* 144 |
| ic\_launcher-xxxhdpi.png | 192 \* 192 |

#### \[ iOS \]

| 檔名 | 尺寸 |
| --- | --- |
| icon\_29x29.png | 29 \* 29 |
| icon\_40x40.png | 40 \* 40 |
| icon\_58x58.png | 58 \* 58 |
| icon\_76x76.png | 76 \* 76 |
| icon\_80x80.png | 80 \* 80 |
| icon\_120x120.png | 120 \* 120 |
| icon\_152x152.png | 152 \* 152 |
| iTunesArtwork.png | 512 \* 512 |
| iTunesArtwork@2x.png | 1024 \* 1024 |

---

## 檔名 Rule

Android 跟 iOS 檔名請都不要用 `-` DASH，請用 `_` 底線。副檔名請都使用 `.png` 格式，僅有 Android 有 `.9.png` 格式。常見的檔名如下：

* icon\_btn\_back.png： `icon_btn` 代表可以點擊的按鈕們。
* icon\_indicator\_calendar.png： `icon_indicator` 代表一些清單上的小 icon。
* icon\_notify\_small.png： `icon_notify` 是推播的 icon。
* login\_btn\_bg.9.png： `.9.png` 的圖是可以延展的。
* sprout\_logo\_begin.png：其他的圖，就可以隨著客戶隨便命名。

---

## Sketch 匯出

基本上只要每個 slice 都有設定好匯出應該會蠻順利的。唯一需要注意的是， `Android 的 Resource` 必須放到不同 Resolution 名稱的 folder 裡，所以你會出的時候會發現，sketch 產生的檔名範例如下：

* icon\_btn\_star\_mdpi.png
* icon\_btn\_star\_hdpi.png
* icon\_btn\_star\_xhpi.png
* icon\_btn\_star\_xxhpi.png
* icon\_btn\_star\_xxxhpi.png

但他們應該要是：檔名都是 `icon_btn_star.png` 但是在不同解析度的 Folder 裡。

* /mdpi/icon\_btn\_star.png
* /hdpi/icon\_btn\_star.png
* /xhpi/icon\_btn\_star.png
* /xxhpi/icon\_btn\_star.png
* /xxxhpi/icon\_btn\_star.png

這時候一個一個改檔名會非常麻煩，所以教你一個快速的方法：  
參考文章：[https://medium.com/@lmindler/using-sketch-3-and-a-bit-of-fairy-dust-for-a-better-android-workflow-f667d0048855](https://medium.com/@lmindler/using-sketch-3-and-a-bit-of-fairy-dust-for-a-better-android-workflow-f667d0048855)

* 先在你要放 Android 圖檔的資料夾匯出所有的 Android 圖檔後
* 打開 cmd，移至散亂檔案的 folder，使用第一段指另，開好五個空白的資料夾（如果你還沒開的話）。
* 使用第二段指令，就會自動幫你移動圖片們，並重新命名。

```bash
//第一段指令
mkdir drawable-mdpi
mkdir drawable-hdpi
mkdir drawable-xhdpi
mkdir drawable-xxhdpi
mkdir drawable-xxxhdpi
```

```bash
//第二段指令
for file in $(find . -type f -iname '*-xxxhdpi*'); do
  mv "$file" "drawable-xxxhdpi/${file/-xxxhdpi/}"
done
for file in $(find . -type f -iname '*-xxhdpi*'); do
  mv "$file" "drawable-xxhdpi/${file/-xxhdpi/}"
done
for file in $(find . -type f -iname '*-xhdpi*'); do
  mv "$file" "drawable-xhdpi/${file/-xhdpi/}"
done
for file in $(find . -type f -iname '*-hdpi*'); do
  mv "$file" "drawable-hdpi/${file/-hdpi/}"
done
for file in $(find . -type f -iname '*-mdpi*'); do
  mv "$file" "drawable-mdpi/${file/-mdpi/}"
done
```

---

## 色碼教學

Android 跟 iOS 使用的色碼方式不同：

|  | 舉例紅色 | 舉例 30% 透明的紅色 |
| --- | --- | --- |
| Android | \#FF0000 | \#4D0000 |
| iOS | \[UIColor colorWithRed:238.0f/255.0f green:204.0f/255.0f blue:204.0f/255.0f alpha:1.0\] | \[UIColor colorWithRed:238.0f/255.0f green:204.0f/255.0f blue:204.0f/255.0f alpha:0.3\] |

* iOS 的 color Generator : [http://www.briangrinstead.com/blog/ios-uicolor-picker](http://www.briangrinstead.com/blog/ios-uicolor-picker)

\[Android\] 的透明度色碼表：

| 透明度 | 代碼 | 透明度 | 代碼 |
| --- | --- | --- | --- |
| 100% | FF | 95% | F2 |
| 90% | E6 | 85% | D9 |
| 80% | CC | 75% | BF |
| 70% | B3 | 65% | A6 |
| 60% | 99 | 55% | 8C |
| 50% | 80 | 45% | 73 |
| 40% | 66 | 35% | 59 |
| 30% | 4D | 25% | 40 |
| 20% | 33 | 15% | 26 |
| 10% | 1A | 5% | 0D |
| 0% | 00 | -- | -- |



