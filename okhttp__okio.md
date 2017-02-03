# OKHttp / OKio
`OKHttp` 是 **Square** 所開發的 Library，幫我們處理 `http` 的溝通。同時因為在進行 http 溝通時，通常也會有本地端的 `I/O` 行為，所以 **Square** 基於原生 Java I/O 上，開發了 `OKio`，宣稱是對於有 I/O 存取一定程度的優化。

##Gradle
``` xml
compile 'com.squareup.okhttp:okhttp:2.6.0'
```

##使用
不要使用這個，直接用小馬寫的 [JsonCake](https://github.com/crazyma/JsonCake) 吧！ XDD

>JsonCake其實也是使用OKHttp喔。如果真的有興趣了解 `OKHttp`，直接去[官網](http://square.github.io/okhttp/)看看吧！

##Reference
- [OKHttp](https://github.com/square/okhttp)
- [OKio](https://github.com/square/okio)