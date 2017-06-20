# Andorid - Sharding Test With Spoon

## Steps
1. Download the latest [spoon](https://github.com/square/spoon) jar file and put into your project folder.
2. Run `./graldew assembleDebug` & `./gradlew assembleAndroidTest` to generate apks for test.
3. Run the following Spoon command :
```
java -jar spoon-runner-1.7.1-jar-with-dependencies.jar \
    --sdk /Users/willy/Library/Android/sdk \
    --output app/build/reports/androidTests/connected/ \
    --apk app/build/outputs/apk/app-debug.apk \
    --test-apk app/build/outputs/apk/app-debug-androidTest.apk \
    -serial emulator-5554 \
    -serial emulator-5556 \
    --shard
```

> You need to use command `./emulator -port 5556` to fixed the port of emulator when you start a emulator.

4. Check out your test result in `project/spoon-output`.


### Required Parameters
`--sdk /Users/willy/Library/Android/sdk` : set up the android sdk path.
`--apk app/build/outputs/apk/app-debug.apk` : set up your apk path.
`--test-apk app/build/outputs/apk/app-debug-androidTest.apk` : set up your test apk path
`-serial emulator-5554` : the device you want to run your test.

More options in [Spoon](https://github.com/square/spoon) website.
