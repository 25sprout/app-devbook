# SproutCycle

Android 一定會遇到的 **Lifecycle**，想必大家都不陌生(還有點陌生的請點[這裡](http://developer.android.com/intl/zh-tw/reference/android/app/Activity.html) & [這裡](http://developer.android.com/intl/zh-tw/guide/components/fragments.html))。但是開發久了，總覺得`onCreate`，`onResume`等 method name，很不直覺，而且有時候在找 code 的時候很不方便，所以決定自己做了Interface，客製化 **Activtiy/Fragment**，讓我們有更易懂的 LifeCycle。


>提醒：請先對於 Activity/Fragment 的 Lifecycle 有一定的認識，因為有時候遇到一些實作上的問題，還是得使用`onCreate`等原生的method會比較容易。

##Example
先給個範例比較好理解。如果用這套寫法，你的 `Activity` 會長這樣：
<br />
###一般版本
``` java
public class FirstActivity extends BaseActivity {
    
    @Override
    protected int getBaseType() { // 2016/04/27 新增，用來區分兩種Base Type
        return BASE_TYPE_NORMAL; 
    }
    
    @Override
    public void init() {
        /*  初始化的設定  */
    }

    @Override
    public int getContentViewResource() {
        /*  回傳這個Activity使用的layout resource   */
        return R.layout.activity_first; 
    }

    @Override
    public void onSetup() {
        /*  你的主要設定放這裡    */
    }
}
```
<br />
###Data Binding版本
``` java
/*
 * 別忘了要指定泛型
 */ 
public class BindingActivity extends BaseActivity<ActivityBinding> {

    @Override
    protected int getBaseType() {
        return BASE_TYPE_BIND;
    }

    @Override
    public void init() {

    }

    @Override
    public int getContentViewResource() {
        return R.layout.activity_binding;
    }

    @Override
    public void onSetup(Bundle savedInstanceState) {
        /*  你的主要設定放這裡    */
    }
}
```
> 你需要記得宣告泛型：`ActivityBinding`，也就是 data binding 的 **layout 對象**

<br />上述示範的method 是**必帶**。另外還有其他兩個 method，可以斟酌使用：
```java
    @Override
    public void onBinding(ActivityBinding binding) {
        /*  
         * data binding 設定
         * 如果 使用 BASE_TYPE_BIND 才需要 implement
         */
        binding.setMessage("25sprout Binding Activity");
    }
    
    @Override
    public void findViews() {
        /*  
         * 所有 view 的 mapping 可以放這裡
         * 如果 使用 BASE_TYPE_NORMAL 才需要 implement
         */
        textView = (TextView) findViewById(R.id.text_view);
    }

    @Override
    public void setupActionBar() {
        /*  所有 Toolbar/Actionbar 的設定可以放這裡 */
    }
```

這麼做的優點：
1. 新增一個 **method** 之後，依據它的特性(是在做*初始化*，還是做*View的設定*)，你可以清楚知道它的歸屬。
2. 可以統一 **method name**：假如我做了一個 method，是用來設定*RecyclerView*，我就會取名為`setupRecyclerView`，並且放進`onSetup`。
3. 你可以很清楚知道每個 **method 是在做什麼**：以前使用`onCreate`，你可能不太確定`onCreate`有呼叫哪些東西。
4. 可以藉由`必須implement`的特性，提醒** child class** 去implement特定功能。該特性在 `Fragment` 比較明顯。


這麼做的缺點：
1. **沒有缺點**
2. 硬要說的話，就是要多設定一點東西，有點麻煩。

至於`Fragment`，其設定如下：
``` java
public class FirstFragment extends BaseFragment {
    @Override
    public void init() {
        /*  初始化的設定  */
        /*  注意，不要在此做任何跟View有關的設定  */
    }

    @Override
    public void handleSavedInstanceState(Bundle savedInstanceState) {
        /*  處理 savedInstanceState */
    }

    @Override
    public void findViews(View view) {
        /*  所有 view 的 mapping 可以放這裡     */
    }
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        /*  在這裏 inflate 你的 layout resource */
        /*  注意，不要忘了要 Override 這個method */
        return inflater.inflate(R.layout.fragment_first, container, false);
    }

    @Override
    public void onSetup() {
        /*  你的主要設定放這裡    */
    }
}
```
其他可以斟酌使用的method：
```java
    @Override
    public void setupActionBar(ActionBar actionBar) {
        /*  所有 Toolbar/Actionbar 的設定可以放這裡 
            如果你是使用預設的ActionBar，請使用這個method
        */
        super.setupActionBar(actionBar);
    }

    @Override
    public void setupActionBar(android.support.v7.app.ActionBar actionBar) {
        /*  所有 Toolbar/Actionbar 的設定可以放這裡 
            如果你是使用support.v7的ActionBar，請使用這個method
        */
        super.setupActionBar(actionBar);
    }

    @Override
    public void loadOnActivityCreated() {
        super.loadOnActivityCreated();
        /*  如果有需要loading的code，建議放這裡 */
    }

    @Override
    public void loadOnStart() {
        super.loadOnStart();
        /*  如果有需要loading的code，建議放這裡 */
    }
```

>這裏有兩個 method：`loadonActivityCreated` & `loadOnStart`，其差別是僅僅只是一個是在`onCreate`之後呼叫，一個是在`onStart`之後呼叫，所以可以自己評估自己的load function要被呼叫的時機，再決定要放在哪裡。




#套件使用
接下來介紹如果把這個介面套用在專案裡。<br />一旦導入了`sproutlib`，`SproutCycle`會提供兩組 Interface，要請你利用這兩組 Interface，建立**abstract Activity/Fragment**，之後開發就依照這個設計來設計 Activity/Fragment

##Interface
`sproutlib`提供兩個 Interface，用來取代原生的 lifecycle method：

**SproutActivityCycle**
``` java
public interface SproutActivityCycle<T extends ViewDataBinding> {
    public void init();
    public void findViews();
    public void setupActionBar();
    public int getContentViewResource();
    public void onBinding(T binding);  // New
    public void onSetup(Bundle savedInstanceState);  // update
}
```
**SproutFragmentCycle**
``` java
public interface SproutFragmentCycle {
    void init();
    void handleSavedInstanceState(Bundle savedInstanceState);
    void findViews(View view);
    void setupActionBar(android.support.v7.app.ActionBar actionBar);
    void setupActionBar(android.app.ActionBar actionBar);
    void onSetup();
    void loadOnActivityCreated();
    void loadOnStart();
}
```
##Implement
實作上，需要開發者建立 abstract activity/fragment，並且之後在新增任何 activity/fragment 時，都繼承該 class。

>總之就是下面三段class加進你的Project就對了 XD

>你可以直接利用阿汪開發的 [Android Studio Plugin:SproutCycle](https://drive.google.com/a/25sprout.com/file/d/0B3qrqm_XhaPmUldoQTZzTXZ5MHM/view?usp=sharing) 加入以下的Code喔


##[2016/4/27 updated. (ver.1.2.0)]
因應`Data Binding`，`BaseActivity`多了一些設定

###SproutActivityCycle
**BaseActivity**
``` java
abstract public class BaseActivity<T extends ViewDataBinding> extends AppCompatActivity implements SproutActivityCycle<T> {
    
    public static final int BASE_TYPE_NORMAL = 0;
    public static final int BASE_TYPE_BIND = 1;
    
    protected Toolbar toolbar;  // if you need

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        init();

        switch (getBaseType()){
            case BASE_TYPE_NORMAL:
                setContentView(getContentViewResource());
                findViews();
                break;
            case BASE_TYPE_BIND:
                onBinding((T) DataBindingUtil.setContentView(this, getContentViewResource()));
                break;
        }

        setupActionBar();
        onSetup(savedInstanceState);
    }

    /**
     * New Method
     */
    @Override
    public void onBinding(T binding) {
       /*  Option ! 
        *  如果使用 Base_TYPE_BIND，請在這裡 find view
        */
       toolbar = binding.getMyToolbar;
    }

    @Override
    public void findViews() {
        /*  Option ! 
         *  如果使用 Base_TYPE_NORMAL，請在這裡 find view
         */
        toolbar = (Toolbar) findViewById(R.id.toolbar);
    }

    @Override
    public void setupActionBar() {
        /*  Option ! 
         *  如果有使用到toolbar，可以在這裡設定 
         */
        setSupportActionBar(toolbar);
        ActionBar actionBar = getSupportActionBar();
        actionBar.setDisplayHomeAsUpEnabled(false);
        actionBar.setDisplayShowHomeEnabled(true);
    }
    
    /*
     * new abstract method
     */
    abstract protected @BaseType int getBaseType();

    @IntDef({BASE_TYPE_NORMAL, BASE_TYPE_BIND})
    @Retention(RetentionPolicy.SOURCE)
    public @interface BaseType{}
}

```

###SproutFragmentCycle
** BaseFragment**
``` java
abstract public class BaseFragment extends Fragment implements SproutFragmentCycle {

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        findViews(view);
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);

        if(getActivity() instanceof AppCompatActivity){
            setupActionBar(((AppCompatActivity) getActivity()).getSupportActionBar());
        }else{
            setupActionBar(getActivity().getActionBar());
        }

        init();
        handleSavedInstanceState(savedInstanceState);
        onSetup();
        loadOnActivityCreated();
    }

    @Override
    public void onStart() {
        super.onStart();
        loadOnStart();
    }

    @Override
    public void setupActionBar(ActionBar actionBar) {

    }

    @Override
    public void setupActionBar(android.support.v7.app.ActionBar actionBar) {

    }

    @Override
    public void loadOnActivityCreated() {

    }

    @Override
    public void loadOnStart() {

    }
}
```

如此一來，你就可以直接利用`extends BaseActivity` or `extends BaseFragment`的方式開發囉。


>稍微看上面的code，應該就大致上知道在幹什麼了吧。基本上做法就是利用`SproutCycle`，讓我們在原本的`Android Lifecycle`之上，實作自製的介面。但是依照需求，還是可以直接 override 原本的 `onCreate` 等 method。

##提醒
上述這些設定，都是一些**開發的經驗累積**所驅使，有時候並沒有一定的理由，就只是個人想法。因此有一些設計想法的小撇步，在此特別說明：
1. 基本上大部份的SproutCycle的 **method**，是發生在 `Activity#onCreate` 裡發生，所以大部份的設定，都適用於`初始設定`，如果有需要監測狀態改變的code，還是建議放在原生的 `onResume` 裡設定。
2. 使用這個方式，你會建立一個 `abstract Activity/Fragment`，請善加利用它。如果有一些所有Activity/Fragment可以使用的設定，例如`Handler` 或是 `Google Analystic`...等等，可以直接放在這裏。



