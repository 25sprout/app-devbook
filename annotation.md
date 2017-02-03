# Annotation
Annotation 目前已經越來越普遍使用了。這裡介紹 Android Support Library 所提供的 Annotation。<br>
其他有很多 Library 有使用 Annotaion，例如 EventBus, ButterKnife...這裡就不敘述囉。

## Explain
這些 Android Support Library 提供的註解，主要是 compile 前，先依據 Anotation 的設定檢查不合理的地方，一旦有錯誤，會直接在 Android Studio 上提醒你。所以並不是在 Compile Time or Running Time 的時候進行檢查

## Setup 
現在新的Project都會自帶`com.android.support:appcompat`，所以已經有包含 Annotations Library，如果你沒有引入，可以新增以下dependency
``` xml
dependencies {
    compile 'com.android.support:support-annotations:22.2.0'
}
```
## Type
以下列舉一些種類

| Type  | Note  | Example  |
|---|---|---|
| Resources  | 用在 Resource ID  | @ColorRes, @IdRes, @LayoutRes, @MenuRes, ...  |
| Null  | 此變數可否為 Null  | @NonNull, @Nullable  |
| Value  | 用來指定array的size，或是 int/float的 range  | @Size, @IntRange, @FloatRange  |
| Definition / Enum  |用來指定該method只吃哪些 instance value。需要一些設定，請參照下面的範例   | @IntDef, @StringDef  |
| RGB  | 限制為Color Type  | @ColorInt  |
| Override  | 用來指定該method需要call super  | @CallSuper  |
| Return Value  | 指定使用該method的人要檢查/使用 return value  | @CheckResult  |
| Thread| 指定該 method 要跑在什麼 Thread | @UIThread, @MainThread, @WorkerThread, @BinderThread |
|Proguard|防止混淆|@Keep|


#Example
> ***Null Type***

``` java
  ...
  @Nullable
  public Item getItem(){
    ...
    return null;
  }
  ...
  public void setItem(@NonNull String name){
    ...
  }
  
```

> ***Resource Type***

``` java

  public setTitle(@StringRes int resId){
  
  }
  
  public setBackground(@DrawableRes int resId){
  
  }

```

> ***Value Type***

``` java
  public void setArray(@Size(2) int[] intArray){
    
  }
  
  public void setDatas(@Size(max=10) String[] stringArray){
  
  }
  
  public void setValues(@Size(multiple = 3) float[] floatArray){
      // array長度要為 3 的倍數
  }
  
  ...
  
  public void setScrore(@IntRange(from=60, to=100) int score){
  }
```
<br>
> *** Definition Type / Enum type***

你可以自訂特定的 int/string type，然後宣告 method argument 為該 custom type。有點像 Enum。跟Enum不同的是，該變數實際上依然是 int type，所以雖然實際實用上跟 Enum 無異，但是會比較節省資源。
```java
  public static final int SHAPE_SQUARE = 0;
  public static final int SHAPE_TRIANGLE = 1;

  @IntDef({SHAPE_SQUARE, SHAPE_TRIANGLE})
  @Retention(RetentionPolicy.SOURCE)
  public @interface ShapeType{}
  
  ...
  
 protected @ShapeType int getShapeType(){
 }
 
 protected void setShapeType(@ShapeType int shapeType){
 }
```

> *** Override Type***

``` java
  class MyObject{
    @CallSuper
    public void doSomething(){
    }
  }
  
  ...
  
  class ChildObject extends MyObject{
    @Override
    public void doSomething(){
          super.doSomething(); // must call
    }
  }
```
<br>
> *** Return Value***

確認你是否有使用 method 的回傳值
``` java
  public @CheckResult String trim(String s) { return s.trim(); }
  ...
  s.trim(); // this is probably an error
  s = s.trim(); // ok
 
```
<br>
> *** Thread Type ***

此方法可以用在 AsyncTask 之類的 method
``` java
  new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            doSomething(); // correct
            updateViews(); //error
            return null;
        }
      
        @Override
        protected void onPostExecute(Void result) {
           updateViews();  // correct
       }
    };

  @UiThread
  public void updateViews() {
   
  }
  
  @@WorkerThread
  public void doSomething(){
  }
```
<br>
> ***Proguard***

``` java
  @Keep
  public static void doSomethging(Bitmap bitmap) {
      // this method wouldn't be confused
  }
```


