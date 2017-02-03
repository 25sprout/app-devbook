# Android Performance Patterns

這篇文章是翻譯自這個[網站](https://medium.com/@laanayabdrzak/android-performance-patterns-rescue-tips-8c1e4c7cb1f0#.n6vao1590)，主要是提醒一些增進Performance的寫法，看不懂的就貼原文囉~
---
##Patterns to avoid bad performance
1. 不要Block Main Thread
2. Avoid **unnecessary** invalidations that may **trigger** a cascade of invalidations
3. 不要巢狀地使用有`layout_weight`的`LinearLayout`，因為會導致onMeasure被呼叫很多次
4. Custom View不要亂做。編按：改天來分享做Custom View的心得
5. 不要 new 沒用的 object
6. 要建立`常數`，請使用 `static final`
7. 盡量使用基本資料型態(**Primitives**)，例如`int`, `float`, `char`, ...
8. 在class內部，少用`setter` & `getter`
9. 善用 Enhanced loops ***詳見下面註解**
10. 善用 `Package`，取代 Private Inner Classes
11. 小心使用 `Native Mathod`(是廢話嗎！？)

> 關於`第9點`，因為使用 Enhanced Loop，通常都是使用`iterator`去跑。根據一些
> **Android 工程師**的說法，反而是建議我們不要使用`iterator`，用原本的 `loop + index`還比較省資源且快速

##Patterns for Custom views
1. **KISS**(Keep it as Simple as Possible, 個人覺得是廢話)
2. 如果Custom View要`inflater layout`，該 XML Resource 的 Root Tag 請使用`<merge></merge>`
3. 善用`include` tag
4. 盡量在`onDraw`減少 `new object`或 `過多的運算`
5. 小心使用`invalidate()`，減少呼叫的個數。可以的話利用`invalidate(Rect)`限制刷新的範圍(一次刷新整個View是很消耗資源低~)
6. 不仿考慮建立自己的ViewGroup
7. 不要再用`ListView` or `GridView`，`RecyclerView`是你的新歡

##Patterns to avoid memory churn
1. 減少 new 沒必較的 object，例如`String`，或是AutoBoxing導致的Integer, Boolean
2. 考慮使用`Object Pool`
3. 小心使用 `emun`。**Android 工程師**的建議是：`不要使用`

<br /><br />
待續...
