# Timber

Timber是世界知名的 Android Developer：JakeWharton 開發的小套件(跟他其他開發或是參與的Project，這真的只是小case)。其目的只是用來取代 Android 原生 `Log` System

##使用
就像使用`Log`一樣，只要在需要log的地方，寫上`Timber.d("message")`，這個套件也一樣提供了5種狀態讓開發者使用：`Timber.v()`，`Timber.d()`，`Timber.i()`，`Timber.w()`，`Timber.e()`
>注意：使用Timber不用取 **Tag Name**，會自動使用目前的 `Class Name` 作為 **Tag Name**

##設定
新增一個 **Custom Application**，並且在 `onCreate` 裡面加一行 code，如下：
``` java
public class CustomApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        SproutUnit.setupLog(BuildConfig.DEBUG);
    }
}
```
 
 修改 `AndroidManifest` 的 **application name**：
 ``` xml
 <manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:name="com.sprout.surveycaketest.CustomApplication"
            ...
        >
        ...
    </application>

</manifest>

 ```
 
 ##參考
 
 其他更詳細的使用，可以直接去 [官方 github](https://github.com/JakeWharton/timber) 查看喔。


