# Run no-window emulator
在測試的時候，你不一定需要真的看到 emulator 跑起來的樣子
所以你可以使用 `no-window` 參數
但有時候你又會想要看到 emulator 的狀況
最好的方法，就是在 gitlab > Variable 加入參數設定

## step 1
在 gitlab 新增 `no-window` 變數
![](https://i.imgur.com/TK00IU2.png)

## step 2
create new script file: `launch_emulator`
``` script
if [ "$no_window" == "yes"  ]; then
  echo "launch emulator with NO window ..."
  /Users/25sprout/Workspace/Android/sdk/tools/emulator -avd Nexus9API23 -no-audio -no-window -gpu off & ./wait-emulator
else
  echo "launch emulator with window ..."
  /Users/25sprout/Workspace/Android/sdk/tools/emulator -avd Nexus9API23 -no-audio & ./wait-emulator
fi

```

## step 3
修改 **gitlab-ci**
``` yml
instrumentTests:
  tags:
    - android_ci_sample
  stage: test
  script:
    - chmod +x ./wait-emulator
    - chmod +x ./run_instrument_test
    - chmod +x ./launch_emulator      # NEW
    - ./launch_emulator               # NEW
    - ./run_instrument_test
  artifacts:
    when: on_failure
    paths:
    - app/build/reports/androidTests/connected/
```

## step 4
這樣你可以隨時在 Gitlab 上修改變數(yes or no)，來決定要不要顯示 emulator window
