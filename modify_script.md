# 調整 script
測試時，有時會有以下需求
> 1. 有的時候會想要每次測試時都是從頭安裝，此時會希望安裝前先移除舊版的 apk
> 2. 希望測試中的狀態能夠 output 在 gitlab ci console 上

以下就介紹因應上述兩個需求所做的調整

## Issue 1
修改 `run_instrument_test`
``` script
apkCheck=`adb shell pm list packages | grep 'your.package.name\s$'`
if [ -n "$apkCheck" ]; then
  echo "app is exist. Uninstall it..."
  adb uninstall your.package.name
else
  echo "app is NOT exist"
fi
```

## Issue 2

### 顯示 unit test 狀態
在 app 的 build.gradle 新增
``` gradle
tasks.withType(Test) {
    testLogging {
        // set options for log level LIFECYCLE
        events "passed", "skipped", "failed", "standardOut"
        showExceptions true
        exceptionFormat "full"
        showCauses true
        showStackTraces true
        // set options for log level DEBUG and INFO
        debug {
            events "started", "passed", "skipped", "failed", "standardOut", "standardError"
            exceptionFormat "full"
        }
        info.events = debug.events
        info.exceptionFormat = debug.exceptionFormat
        afterSuite { desc, result ->
            if (!desc.parent) { // will match the outermost suite
                def output = "Results: ${result.resultType} (${result.testCount} tests, ${result.successfulTestCount} successes, ${result.failedTestCount} failures, ${result.skippedTestCount} skipped)"
                def startItem = '|  ', endItem = '  |'
                def repeatLength = startItem.length() + output.length() + endItem.length()
                println('\n' + ('-' * repeatLength) + '\n' + startItem + output + endItem + '\n' + ('-' * repeatLength))
            }
        }
    }
}
```

CI console 會顯示以下的結果
![](https://i.imgur.com/auvOX2i.png)

### 顯示 instrument test 狀態
在 `run_instrument_test` 中新增
``` script
adb logcat *:S TestRunner:V & LOGCAT_PID=$! ;

if [ -n "$LOGCAT_PID" ] ; then
    kill $LOGCAT_PID;
fi
```

CI console 會顯示以下結果<br/>
![](https://i.imgur.com/UxLlKd1.png)

> 因為 `Instrument Test` 的測試結果是顯示在 *Logcat* 上，且 Tag 為 `TestRunner`，用這個方式爬出 TestRunner 的內容並顯示在 ci console

## Final
> 針對 Issue1 & Issue2 修改之後的 run_instrument_test
``` script
debugApkCheck=`adb shell pm list packages | grep 'com.sprout.gitlabcisample\s$'`
if [ -n "$debugApkCheck" ]; then
  echo "app is exist. Uninstall it..."
  adb uninstall com.sprout.gitlabcisample
else
  echo "app is NOT exist"
fi

adb logcat *:S TestRunner:V & LOGCAT_PID=$! ;
./gradlew connectedDebugAndroidTest
RESULT=$?
if [ -n "$LOGCAT_PID" ] ; then
    kill $LOGCAT_PID;
fi
adb emu kill
exit $RESULT
```
