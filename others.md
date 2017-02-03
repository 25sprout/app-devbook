# others
其他雜七雜八的紀錄

---


###Android Debug Keystore: `~/.android/debug.keystore`
``` xml
Keystore name: "debug.keystore"
Keystore password: "android"
Key alias: "androiddebugkey"
Key password: "android"
CN: "CN=Android Debug,O=Android,C=US"
```

---
### Application ID vs Package Name
 - `application id`：对应 apk 中 manifest 定義的應擁用包名(package name)，同時用於設置 以及 Google Play 的應用唯一標準。
 - `package`：用於在 Source Code 中引用 R class 以及解析註冊相關的 activity/service，對應 Java 的包名(package name)概念。
 - `application id`依據不同的設置，會有所不同，但是`package name`會應用在 Source Code 中，是不會改變
 - `application id` 是對外，`Package Name`是對內
 - 依照經驗，如果你改`application id`，`class path`會改變，所以要如果要使用第三方SDK要註冊 `class name` 的時候，記得使用：`{ApplicaiontId}.MyObject`，而不是`{PackageName}.MyObject`

Reference
- 
