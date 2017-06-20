# Android Fingerprint 


## 保護措施
- The Android Fingerprint API protects user privacy by keeping users’ fingerprint features carefully contained within secure hardware on the device. This guards against malicious actors, ensuring that users can safely use their fingerprint, even in untrusted applications.
- AndroidKeyStore 主要是用来儲存一些 key，存在這裡 key 可以設定 KeyProtection，例如只有通過使用者認證才能取出 key 使用。這些 key 是存在系统裡，不是在 app 的目錄底下，並且每個 app 的 key 是獨立的，沒辦法獲取其他 app 的 key。假如 app1 建立了一把 key 叫做`temp`，那 app2 用`temp`這個名字去獲取這把 key 是拿不到的。

## Code Explaination
![](https://i.imgur.com/ct0ZK5S.png)
![](https://i.imgur.com/h0ObWkx.png)



