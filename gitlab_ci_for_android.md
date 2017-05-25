# Gitlab CI for Android

這篇記錄如果在 Gitlab 設定 CI

- [設定 Runner](setup_runner.md)
- [設定 Gitlab CI](setup_ci.md)
- [調整 script](modify_script.md)
- [在 CI 產生 Signed APK](create_signed_apk.md)
- [App Version & Apk Name](version_and_apk_name.md)
- [Run no-window emulator](run_no_window_emulator.md)

## 提醒
這裡是在公司的電腦建立環境，所以你要確認要跑測試的電腦
1. 有安裝 Java SDK & Android SDK
2. adb 有加入至環境變數
3. 有 android device 連線(模擬器或實機)
