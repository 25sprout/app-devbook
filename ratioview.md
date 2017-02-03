# RatioView
這是為了因應需要View成固定比例縮放所客製化的View，跟一般設定View的方式不太一樣。
- 一般設定 `View` 的方式
``` xml
<View
    android:layout_width="match_parent"
    android:layout_height="100dp" 
    ...
/>
```

- 使用 `RatioView`
``` xml
<RatioRelativeLayout
    xmlns:ratio_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    ratio_view:ratio_width="16"
    ratio_view:ratio_height="9"
    ratio_dependOn="width"
    ...
/>
```
> 這個設定的意思為：依據給定的`width`，算出`height`，且 width 與 height 會維持`16:9`的比例

dependency
===
``` XML
dependencies {
    compile 'com.crazyma.ratioview:ratio:1.0.3'
}
```