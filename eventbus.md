# EventBus
有點像 Android 的 `BroadcastReceiver` 的功能，但是設定相對簡單寫容易使用。

## 使用情境
1. 取代 `Handler` or `Callback` ，特別是在類似 **"存取網路資源"** 等，會消耗時間的工作
2. 不同物件的溝通：例如有時候需要把 data 從 A -> B -> C ...

##Gradle
``` xml
compile 'de.greenrobot:eventbus:2.4.0'
```

##使用
- 將你的 Data 包成 XXXEvent class，這裏是以回傳 Error 的情境為例，做了一個 `ErrorEvent` 物件

```java
public class ErrorEvent {
    public int errorCode;

    public ErrorEvent(int errorCode) {
        this.errorCode = errorCode;
    }

    public int getErrorCode() {
        return errorCode;
    }
}

```
- 把接收者，例如 `Activity` or `Fragment`，註冊/反註冊至 **EventBus**

``` java
public class MainActivity extends AppCompatActivity {

    ...

    @Override
    protected void onResume() {
        super.onResume();
        EventBus.getDefault().register(this);
    }

    @Override
    protected void onPause() {
        super.onPause();
        EventBus.getDefault().unregister(this);
    }

    ...

}
```

- 在接收者中，定義接收 `ErrorEvent` 的 method

``` java
public class MainActivity extends AppCompatActivity {

    ...
    
    public void onEvent(ErrorEvent event) {
        /* 處理事件 */
        Log.i("TAG", event.getMessage());
    };

    ...

}
```
> 注意！method name有規定。下面會介紹。

- 在你想要的時機，丟出 `ErrorEvent`。這裡以一個 **AsyncTask** 為例

``` java
private class DownloadFilesTask extends AsyncTask<URL, Integer, String> {
    protected String doInBackground(URL... urls) {
        ...
        return resultStr;
    }

    protected void onPostExecute(String resultStr) {
        if(resultStr == null){
            ErrorEvent errorEvent = new ErrorEvent(-1);
            EventBus.getDefault().post(errorEvent);
        }else
            showDialog(resultStr);
    }
 }
```

##Thread Handling : 利用 `method name`
EventBus 提供四種 **method name**，指定程式跑在哪個 `Thread`
1. onEvent：使用原本 caller 所在的 Thread
2. onEventMainThread：不論 caller 在什麼 Thread，程式一定會跑在`UI Thread`
3. onEventBackgroundThread，如果 caller 已經跑在 `非UI Thread`，則會**維持在同一個 Thread**。如果caller是在 `UI Thread`，會 create 一個 `Background Thread` 並且在其執行
4. onEventAsync：不論在哪個 Thread，都會建立一個新的Thread，並且跑在新的 Thread 上

##Reference
- [EventBus](https://github.com/greenrobot/EventBus)
- [HowTo](https://github.com/greenrobot/EventBus/blob/master/HOWTO.md)

