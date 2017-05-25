# 設定 Gitlab CI

- 在專案中包含 `.gitlab-ci.yml`，gitlab 就會自動偵測
- 以後只要有 **push commit** or **merge quest**，就會執行 CI
- 下面就是我採用的`.gitlab-ci.yml` sample，以及另外兩個會被執行的 script files(`wait-emulator` & `run_instrument_test`)

#### .gitlab-ci.yml
``` yml
image: openjdk:8-jdk

before_script:
  - export ANDROID_HOME=/Users/25sprout/Workspace/Android/sdk/

stages:
  - test
  - buildApk

unitTests:
  tags:
    - android_ci_sample
  stage: test
  script:
    - ./gradlew test
  artifacts:
    when: on_failure
    paths:
    - app/build/reports/tests/testDebugUnitTest/


instrumentTests:
  tags:
    - android_ci_sample
  stage: test
  script:
    - chmod +x ./wait-emulator
    - chmod +x ./run_instrument_test
    - /Users/25sprout/Workspace/Android/sdk/tools/emulator -avd Nexus9API23 -no-audio & ./wait-emulator
    - ./run_instrument_test
  artifacts:
    when: on_failure
    paths:
    - app/build/reports/androidTests/connected/

buildUnsignedApk:
  tags:
    - androidci
  stage: buildApk
  script:
    - ./gradlew assembleDebug
  artifacts:
    paths:
    - app/build/outputs/
```

#### wait-emulator
``` scrip
#!/bin/bash
# Originally written by Ralf Kistner <ralf@embarkmobile.com>, but placed in the public domain
set +e
bootanim=""
failcounter=0
timeout_in_sec=360
until [[ "$bootanim" =~ "stopped" ]]; do
  bootanim=`adb -e shell getprop init.svc.bootanim 2>&1 &`
  if [[ "$bootanim" =~ "device not found" || "$bootanim" =~ "device offline"
    || "$bootanim" =~ "running" ]]; then
    let "failcounter += 1"
    echo "Waiting for emulator to start"
    if [[ $failcounter -gt timeout_in_sec ]]; then
      echo "Timeout ($timeout_in_sec seconds) reached; failed to start emulator"
      exit 1
    fi
  fi
  sleep 1
done
echo "Emulator is ready"
```

#### run_instrument_test
``` script
./gradlew connectedDebugAndroidTest
RESULT=$?
adb emu kill
exit $RESULT
```

## 解釋 .gitlab-ci.yml
首先你可以先看 [這個](https://medium.com/@ome450901/gitlab-ci-for-android-%E7%AD%86%E8%A8%98-ae8115781454) 瞭解一下 CI 的架構，至少先理解`stage` & `job` 的關係

### stage
這份 gitlab ci 設定了兩個 stage:
1. `test`: 用來跑所有的測試。目前設定了兩個 jobs: `unitTests` & `instrumentTests`
2. `buildApk`: 用來產生 unsigned or signed apk。目前只有一個 job: `buildUnsignedApk`

### jobs
#### job: *unitTests*
``` script
script:
    - ./gradlew test
```
- 只執行了一行 script，也就是執行 `unit test`

``` script
artifacts:
    when: on_failure
    paths:
    - app/build/reports/tests/testDebugUnitTest/
```
- 如果發生錯誤，則會把產生的 report 打包上傳至 gitlab

#### job: *instrumentTests*
``` script
script:
    - chmod +x ./wait-emulator
    - chmod +x ./run_instrument_test
    - /Users/25sprout/Workspace/Android/sdk/tools/emulator -avd Nexus9API23 -no-audio & ./wait-emulator
    - ./run_instrument_test
```
- 這裡使用模擬器來跑測試。你需要**事先建好你的模擬器**
- 因為執行 `instrument test` 的步驟比較複雜，所以寫了兩個 script 來執行
    1. wait-emulator: 因為是使用模擬器來跑，所以需要暫停 job，等待模擬器啟動後才可以繼續執行
    2. run_instrument_test: 執行 instrument test，並且將測試的結果回傳給 ci console

``` script
artifacts:
    when: on_failure
    paths:
    - app/build/reports/androidTests/connected/
```
如果發生錯誤，會把 report 打包上傳至 gitlab


#### job: *buildUnsignedApk*
``` script
script:
    - ./gradlew assembleDebug
```
- 打包 debug apk

``` script
artifacts:
    paths:
    - app/build/outputs/
```
- 不論成功或失敗，把 outputs folder 打包上傳，其中包含 apk file

## Demo
- pipelines
![](https://i.imgur.com/TaQRNoG.png)

- 正確執行的結果
![](https://i.imgur.com/Tgjeqz6.png)
- Download APK
![](https://i.imgur.com/c0Sl6UG.png)
