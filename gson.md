# Gson

Google 開發Java Library，主要用途為將 Java Object 轉乘Json字串，或是把Json字串成Json Object

##Gradle
``` java
compile 'com.google.code.gson:gson:2.5'
```
##使用

假如我們有個json file如下
``` json
{
    "ID":264,
    "nickname":"crazyma",
    "fullname":{
        "First_Name":"David",
        "Last_Name":"Ma"
    },
    "houses":[
        {
            "ID":19,
            "address":"xxxxxxx"
        },
        {
            "ID":83,
            "address":"yyyyyyy"
        }
    ],
}
```
則我們首先要建立一個對應的Class
``` java
public class PersonData{
    private int ID;
    private String nickname;
    private FullNameData fullname;
    private List<HouseData> houses;
    
    public class FullNameData{
        private String First_Name,Last_Name;
    }
    
    public class HouseData{
        private int ID;
        private String address;
    }
}
```
>注意
1. Data Type 要相對應
2. 變數名稱要一樣
3. Object Name/Data Type 的名稱可以不一樣

進行轉換
``` java
String jsonStr; //  the json string your downloaded

/*  Json -> Object  */
Gson gson = new Gson();
PersonData personData = gson.fromJson(jsonStr, PersonData.class);

/*  Object -> Json  */
String anotherJsonStr = gson.toJson(personData);
```

##更改變數名稱
如果要改變Java Class裡的變數名稱(也就是與Json定義的變數名稱不同)，可以用Annotation的方式設定
``` java
class MyJsonObj{
    // "first_name" 為 Json File 定義的 variable name
    @SerializedName("first_name")
    private String firstNameStr;

    @SerializedName("last_name")
    private String lastNameStr;
        ...    
}
```

##Integer/Float is null in Json File
如果 Java Class 有一個變數為 `int` or `float` 之類的型態，在作轉換的時候，如果發現 Json 裡的值為`null`，此變數會自動為 `0`。如果你希望能夠有自訂的初始值，可以利用以下的方式設定

##### 1. 宣告 TypeAdapter。此設定會強制把 Float 型態的變數，如果數值為null，就轉成-99f

``` java
public class FloatTypeAdapter extends TypeAdapter<Float> {

        @Override
        public void write(JsonWriter out, Float value)
                throws IOException {
            out.value(value);
        }

        @Override
        public Float read(JsonReader in) throws IOException {
            if (in.peek() == JsonToken.NULL) {
                in.nextNull();
                return -99f;
            }
            try {
                String result = in.nextString();
                if ("".equals(result)) {
                    return -99f;
                }
                return Float.parseFloat(result);
            } catch (NumberFormatException e) {
                throw new JsonSyntaxException(e);
            }
        }
    }
```

##### 2. 設定 Gson
``` java
Gson gson = new GsonBuilder()
                .registerTypeAdapter(float.class, new FloatTypeAdapter())
                .registerTypeAdapter(Float.class, new FloatTypeAdapter())
                .create();
                
MyObject myObject = gson.fromJson(jsonStr, MyObject.class);
```

##Reference
- [Gson](https://github.com/google/gson)
