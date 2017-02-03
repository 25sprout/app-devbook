# Picasso

由 **Square** 所推出的 Image Loading/Caching Library。相較於以前使用過的[Android-Universal-Image-Loader](https://github.com/nostra13/Android-Universal-Image-Loader)來說，設定簡單，而且Code看起來也比較順眼，後來所有顯示圖片的部份都利用 Picasso 代勞囉

##Gradle
``` xml
compile 'com.squareup.picasso:picasso:2.5.2'
```

##基本用法
只要會這幾招，應該就可以應付 80% 的狀況
- 基本寫法
``` java
/**
 *   url : 圖片的網址
 *   imageView : 操作的ImageView Object
 */
Picasso.with(context)
    .load(url)
    .into(imageView);
```
- 加上 Placeholder & Error Image
``` java
Picasso.with(context)
    .load(url)
    .placeholder(R.drawable.user_placeholder)
    .error(R.drawable.user_placeholder_error)
    .into(imageView);
```

- 除了網路圖片，也可以讀取 Local 的圖片
``` java
Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
Picasso.with(context).load(new File(...)).into(imageView3);
```

##進階用法
偶爾會有一些特別的需求需要克服

- 加上 `CallBack`
``` java
Picasso.with(context).load(url).into(imageView, new Callback() {
        @Override
        public void onSuccess() {
        
        }

        @Override
        public void onError() {
        
        }
});
```

- 縮放圖片，尤其來源是大圖的時候，可以節省流量 & memory
``` java
/**
 * resize : 只取解析度 50 * 50
 * centerCrop : 取原圖的中間
 */
Picasso.with(context)
    .load(url)
    .resize(50, 50)
    .centerCrop()
    .into(imageView)
/**
 *  如果嫌麻煩，可以直接用fit()
 *  可以直接依據 imageView，縮放(注意!有放大!)至適當的大小
 */
Picasso.with(context)
    .load(url)
    .fit()
    .into(imageView)
```
- 要抓 `Drawable`，如不是直接塞進 ImageView
``` java
Picasso.with(this).load(url).into(new Target() {
        @Override
        public void onBitmapLoaded(Bitmap bitmap, Picasso.LoadedFrom from) {      
      
        }

        @Override
        public void onBitmapFailed() {
      
        }
});
```

##Reference
1. [Picasso](http://square.github.io/picasso/)
2. 某人寫的，更詳盡的[使用說明](https://futurestud.io/blog/picasso-series-round-up)

  