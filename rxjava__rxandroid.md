# RxJava / RxAndroid

最近幾年非常紅的`RxJava`，大概用了快一年了。來稍微寫的心得分享吧。
> 關於 `RxJava` 的原理，網路上有一些大神(例如[這個](http://gank.io/post/560e15be2dca930e00da1083))，我相信我應該是沒辦法寫更好了，所以這篇就是著重在 `如何使用`囉


## What is RxJava
直接引用官方說法：`a library for composing asynchronous and event-based programs using observable sequences for the Java VM`。<br><br>
我個人認為重點是兩個字：`asynchronous` & `sequences`！RxJava強大的地方是，可以用非常簡單的方式，在不同`thread`之間，`依序`執行一連串的 `program`。處此之外，它提供了許多 operator，可以讓我們在一連串的 `program`之中，進行方便的整理或操作。

## 使用原理
首先你一定要先知道的，因為他是一種 `event-base programming`，所以一定會有兩個元素：`Observable`(可被觀察的對象/被觀察者) & `Observer`(觀察者)。一般時候 `Observable`要被指定 `Observer`之後，才會去執行。<br><br>

一旦 `Observable` & `observer` 都設定好，`Observable`就會去觸發event(官網是使用`emit`這個字)，`Observer`就會去接收並處理event。<br><br>

恩...所以到底是什麼意思呢XD<br><br>


###Observable
先從 `Observable` 開始。來個最基本的範例來解釋
``` java
Observable.just("test string")
          .subscribe(observer)
```

上面這個例子很簡單，`Observable`擊發(`emit`)一個string object，然後由 `observer` 接收並處理<br><br>

下一個範例就比較特別且常用
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribe(observer)
```
這個例子，他不是擊發`Array`，而是依序擊發`Array Item`，所以`observer`會依序地呼叫三次，且三次所帶的 event(或是參數)會分別是 `David`,`Cathy` & `Lulu`<br><br>

`Observable.from()` & `Observable.just()`是最容易且最常見create `Observable` 物件的方式，當然你也可以客製化自己的 `Observable` 物件，方法是使用 `Observable.create()`，如此可以自己定義`擊發規則`及`event型態`。<br>
例如以下範例:
``` java
Observable observable = Observable.create(new Observable.OnSubscribe<String>() {
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("No. 1");
        subscriber.onNext("No. 2");
        subscriber.onNext("No. 3");
        subscriber.onCompleted();
    }
});
```
> 該 Observable 會依序擊發 `No. 1`, `No. 2`, `No. 3`的字串給 Observer，然後結束

###Observer
Observer 基本上有三個部分：`onNest`, `onCompleted`, & `onError`，如下：
```java
Observer<String> observer = new Observer<String>() {
    @Override
    public void onNext(String s) {
        Log.d(tag, "Item: " + s);
    }

    @Override
    public void onCompleted() {
        Log.d(tag, "Completed!");
    }

    @Override
    public void onError(Throwable e) {
        Log.d(tag, "Error!");
    }
};
```

- OnNext：是你要處理每次`擊發`的地方。這個範例僅只是做輸出
- onCompleted：如果所有`擊發`完成，你可以在這裡執行想做的事
- onError：如果在整個`sequence`中任何地方發生`Exception`，他會一律呼叫onError。這也凸顯一個好處：**我們只需要在一個地方統一處理所有可能 Exception**
- **提醒**：`onCompleted` & `onError`，不會同時被呼叫，且僅僅只會被呼叫一次。`onNext`則是會依照需求，可能被呼叫1次或多次，甚至不會呼叫。

所以一個完整的使用會長的像這樣：
``` java
Observer<String> observer = new Observer<String>() {
    @Override
    public void onNext(String s) {
        Log.d(tag, "name: " + s);
    }

    @Override
    public void onCompleted() {
        Log.d(tag, "Completed!");
    }

    @Override
    public void onError(Throwable e) {
        Log.d(tag, "Error!");
    }
};

String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribe(observer)

```

執行結果：
```
name: David
name: Cathy
name: Lulu
Complete!
```

但是有時候我們為了節省時間，不會完整定義`Observer`，而只是 implement 我們想要的部分。所以就會有以下的寫法：
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  Log.d(tag, "name: " + s);
              }
          });
```
這個範例只 implement 了 `onNext` method，而不是完整 `Observer`物件。基本上這樣寫是沒有問題的，只是如果有`Exception`，就沒有辦法處理了。那你可以這樣寫：
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  Log.d(tag, "name: " + s);
              }
          }, new Action1<Throwable>(){
              @Override
              public void onError(Throwable e) {
                Log.d(tag, "Error!");
              }
          });
```

## Operator
RxJava 令人驚豔的一個特點就是豐富的 Operator，其實目前我也還沒有完全參透(因為太多了)有興趣的話可以看[這裡](http://reactivex.io/documentation/operators.html#alphabetical)。這裡示範我自己常用的

### *map
可以幫你把型態轉換成另一個再繼續往下執行
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .map(new Func1<String, Integer>() {
                    @Override
                    public Integer call(String s) {
                        return s.length();
                    }
                })
          .subscribe(new Action1<Integer>() {
              @Override
              public void call(Integer length) {
                  Log.d(tag, "The length of string : " + length);
              }
          })
```
> 這個範例可以輸出字串的長度

輸出結果如下
```
The length of string : 5
The length of string : 5
The length of string : 4
```
### *filter
如果符合規則才會往下執行
``` java
String[] names = {"David","Badu","Cathy","Lulu"};
Observable.from(names)
          .filter(new Func1<String, Boolean>() {
                    @Override
                    public Boolean call(String s) {
                        return s.length() == 5;
                    }
                })
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  Log.d(tag, "name: " + s);
              }
          })
```
> 這個範例是希望只輸出`長度為5`的字串

輸出結果如下
```
name: David
name: Cathy
```
### *強！
RxJava 強大的地方要來了：Operator是可以**串連**的。例如我現在想要跟`前兩位`姓名`長度為5`的人`打招呼`(這啥鬼要求)，你可以這樣寫：
``` java
String[] names = {"Tasvaluan","David","Badu","Cathy","Lulu","Bruce"};
Observable.from(names)
          .filter(new Func1<String, Boolean>() {
                    @Override
                    public Boolean call(String s) {
                        return s.length() == 5;
                    }
                })
          .take(2) // 取兩位
          .map(new Func1<String, String>){
              @Override
              public String call(String s){
                  return "Hello, " + s;
              }
          }
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  Log.d(tag, "name: " + s);
              }
          })
```
> 這個範例使用到三個 operator：`filter`, `take` & `map`

則你的輸出
```
Hello, David
Hello, Cathy
```
如何，有沒有開始感受到 `RxJava` 的強大呢！所以你可以利用各種 operator，幫你`依序`處理你的資料流。


## Threading
知道基本寫法之後，就是介紹如何切換 thread！ 其實很間單，只需要加一行就可以指定 Thread：
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribeOn(Schedulers.io()) // new line
          .subscribe(observer)
```
如此就會把`依序即發 array item`的程序，設定在 io thread裡。<br>
但是為了把文字輸出在 Android Device上，你需要指定回 Android UI Thread。那你就再加一行：
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribeOn(Schedulers.io())
          .observeOn(AndroidSchedulers.mainThread()) // new line
          .subscribe(observer)
```
如此就會先在`io thread`擊發事件，然後在`UI Thread`中處理字串。<br>
沿用上面的範例，你可以這樣寫：
``` java
String[] names = {"Tasvaluan","David","Badu","Cathy","Lulu","Bruce"};
Observable.from(names)
          .subscribeOn(Schedulers.io()) // in io thread
          .filter(new Func1<String, Boolean>() {
                    @Override
                    public Boolean call(String s) {
                        return s.length() == 5;
                    }
                })
          .take(2) // 取兩位
          .map(new Func1<String, String>){
              @Override
              public String call(String s){
                  return "Hello, " + s;
              }
          }
          .observeOn(AndroidSchedulers.mainThread()) // in UI thread
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  Log.d(tag, "name: " + s);
              }
          })
```
如此你對於資料的處理就會全部發生在io thread，一旦資料處理完，就會送回 UI thread處理。這是實務上最常遇到的寫法。
>如果你需要更多次的切換 thread ，可以參考[這個](http://gank.io/post/560e15be2dca930e00da1083)的`线程控制`部分。

## more operator:flatMap
在介紹一個也非常常見但是有點難理解的 operator: `flatMap`。基本上跟`map`有點像，是用來做型態轉換，但是比較不同的是，會轉換成另一個 `Observable` 物件，所以這個物件會具有自己的`擊發規則`，以及自己的 operator。比較常見的用法是處理`被擊發的Array`。<br><br>
例如，你想要輸出每個人名的字元，不用flatMap之前你可以會這樣寫：
``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .subscribe(new Action1<String>() {
              @Override
              public void call(String s) {
                  char[] charArray = s.toCharArray();
                  for(int i=0;i<charArray.length;i++){
                      Log.d(tag, "the char of name : " + charArray[i]);
                  }
              }
          });
```
這樣的寫法，會覺得有點可惜，因為我們已經用`from`去依序擊發 names，怎麼還是用 `for loop`去處理顯示呢？<br><br>
You Need `flatMap` !

``` java
String[] names = {"David","Cathy","Lulu"};
Observable.from(names)
          .flatMap(new Func1<String, Observable<Char>() {
                    @Override
                    public Observable<Char> call(String s) {
                        char[] charArray = s.toCharArray();
                        return Observable.from(charArray);
                    }
                })
          .subscribe(new Action1<Char>() {
              @Override
              public void call(char c) {
                Log.d(tag, "the char of name : " + c);
              }
          });
```




