# Code Style


[edit on 11/7, 2016]
我發現了一個整理的[文件](https://github.com/ribot/android-guidelines/blob/master/project_and_code_guidelines.md)，說明了很多Code Style的規定，未來會全面依據其建議做開發，下面的舊東西就不用看囉XD。<br><br>
大概提一下文件中提到什麼：
- 命名。包含Class name, Image Resource, Layout file,
- Java Style Rule 
- 程式碼的排版(空格, 換行)
- method 的排序
- method 參數排序及命名規則
- 常數，變數的命名
- XML(Layout) 的排版

> 日前發現官方有提供一份 [Code Style](http://source.android.com/source/code-style.html) 文件，也可以一起參考

---
稍微簡述一下一些變數取名的規則


##method
- 保持第一個單子全小寫，接續的單字第一個字母大小
- 名字可以取長一點，最好可以一看就知道這個method是在幹嘛
- 可以的話，盡量不要用底線(但不強制)
 
``` java
init()
setupRecyclerView()
parseInfos()
showNoInfoMessage
```

## variable
- 基本上跟 method 差不多
- 常數(static final)全大寫，每個單字用底線隔開
- 如果有List, Array 型態的變數，請使用List結尾
- 取名時，與API的名字相同(優先)

``` java
private int averageDistance; 
private float givenDistance;
private List<CityData> cityList;

/*  以下的變數名稱與api所提供的相同   */
private String car_title,url;
private List<YearData> year_list;
private PriceData price;
```



##layout
- 由大範圍到小範圍
 
``` xml
<!-- activity 使用的 layout -->
activity_main.xml
activity_welcome.xml

<!-- fragment 使用的 layout -->
fragment_first.xml
fragment_second.xml

<!-- drawer 使用的 layout -->
drawer_section.xml
drawer_item.xml

<!-- 其他客製化或是小區域所使用的 layout -->
layout_header.xml
custom_linear_layout.xml
```

###resources
``` xml
<!-- option menu action 所使用的文字 -->
<string name="action_send_edit_user">送出修改</string>
<string name="action_delete_car">刪除此車輛</string>

<!-- error 訊息-->
<string name="error_no_internet">網路連線失敗，請檢查您裝置的網路連線狀態</string>
<string name="error_failed_to_connect_to_server">連線失敗，請稍後再試</string>

<!-- login 畫面 所使用的文字-->
<string name="login_fail">登入失敗，請再重新登入</string>
<string name="login">登入</string>

<!-- 編輯使用者 畫面 所使用的文字-->
<string name="edit_user_info_successfully">使用者資料修改完成</string>
<string name="edit_user_info_with_large_image">修改資料完成，但圖片上傳失敗，請檢查圖檔大小是否過大</string>


<!-- 使用者頁面 元件大小 設定-->
<dimen name="user_info_title_width">80dp</dimen>
<dimen name="user_banner_avatar_width">76dp</dimen>

<!-- drawer 元件大小 設定-->
<dimen name="drawer_sub_item_padding">60dp</dimen>
<dimen name="drawer_item_icon_length">28dp</dimen>
```