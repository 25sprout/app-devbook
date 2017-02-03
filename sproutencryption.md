# SproutEncryption

如果需要做字串加密，可以使用這個小套件。

##使用
```java
    SproutEncryption encryption = SproutEncryption.getInstance();
    /*  把 "original message" 做 MD5加密   */
    String result = 
        encryption.encrypt(SproutEncryption.TYPE_MD5,"original messgae");
    
    /*  把 "We love 25sprout." 做 MD5加密   */    
    String result2 = 
        encryption.encrypt(SproutEncryption.TYPE_MD5,
            "We ",
            "love ",
            "25sprout.");
```

>注意：目前僅提供MD5加密 >w<