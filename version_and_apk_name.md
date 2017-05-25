# App Version and Apk Name
根據上述的設定，就已經完成所有的設定。但我還有一些額外的設定
1. 希望方便管理我的 VersionCode & VersionName
2. 希望可以讓 apk file name 可以有目前版號，以方便整理

## VersionCode & VersionName

### 0. Version 的命名
關於 VersionCode 的命名規則，我是參考:
> - [The Rules of Android VersionName & VersionCode](http://blog.ma.beibeilab.com/android-version-rule/)
>
這個方法的另一個好處是，可以輕易從 versioncode 知道是哪個 branch(dev, staging, production)

### 1. 建立 version.properties
> 此檔案紀錄各個 branch(dev, staging, production)的版號
>
``` properties
version_dev=1.0.1-SNAPSHOT
version_staging=1.0.1-RC1
version_production=1.0.0
```

### 2. 編輯 build.gradle
``` gradle
def versionPropsFile = rootProject.file('version.properties')
    if (versionPropsFile.canRead()) {
        def Properties versionProps = new Properties()
        versionProps.load(new FileInputStream(versionPropsFile))
        def defVersionName = versionProps['version_dev']
        /* in staging branch    */
        // def defVersionName = versionProps['version_staging']
        /*  in production branch    */
        // def defVersionName = versionProps['version_production']

        def candidate = "99"
        def (major, minor, patch) = defVersionName.toLowerCase().replaceAll('-', '').tokenize('.')
        if (patch.endsWith("snapshot")) {
            candidate = "0"
            patch = patch.replaceAll("[^0-9]", "")
        } else {
            def rc
            (patch, rc) = patch.tokenize("rc")
            if (rc) {
                candidate = rc
            }
        }
        (major, minor, patch, candidate) = [major, minor, patch, candidate].collect {
            it.toInteger()
        }

        def defVersionCode = (major * 1000000) + (minor * 10000) + (patch * 100) + candidate;

        defaultConfig {
            applicationId "com.sprout.gitlabcisample"
            minSdkVersion 16
            targetSdkVersion 25
            versionCode defVersionCode    //    EDIT
            versionName defVersionName    //    EDIT
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        }
    }
```
這段 code 會產生兩個變數: `defVersionName` & `defVersionCode`，並在 `defaultConfig` 設定


## Change Apk Name

編輯 gradle
``` gradle
    buildTypes {
        debug {
            applicationVariants.all { variant ->
                variant.outputs.each { output ->
                    output.outputFile = new File(output.outputFile.parent,
                            output.outputFile.name.replace("-debug", "-" + defVersionName));
                }
            }
        }
        release {
            ...
            applicationVariants.all { variant ->
                variant.outputs.each { output ->
                    output.outputFile = new File(output.outputFile.parent,
                            output.outputFile.name.replace("-release", "-" + defVersionName));
                }
            }
        }
    }
```

## 完整修改

``` gradle
android {

    ...

    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    def versionPropsFile = rootProject.file('version.properties')
    if (versionPropsFile.canRead()) {
        def Properties versionProps = new Properties()
        versionProps.load(new FileInputStream(versionPropsFile))
        def defVersionName = versionProps['version_dev']
        /* in staging branch    */
        // def defVersionName = versionProps['version_staging']
        /*  in production branch    */
        // def defVersionName = versionProps['version_production']

        def candidate = "99"
        def (major, minor, patch) = defVersionName.toLowerCase().replaceAll('-', '').tokenize('.')
        if (patch.endsWith("snapshot")) {
            candidate = "0"
            patch = patch.replaceAll("[^0-9]", "")
        } else {
            def rc
            (patch, rc) = patch.tokenize("rc")
            if (rc) {
                candidate = rc
            }
        }
        (major, minor, patch, candidate) = [major, minor, patch, candidate].collect {
            it.toInteger()
        }
        def defVersionCode = (major * 1000000) + (minor * 10000) + (patch * 100) + candidate;
        defaultConfig {
            applicationId "com.sprout.gitlabcisample"
            minSdkVersion 16
            targetSdkVersion 25
            versionCode defVersionCode
            versionName defVersionName
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        }
        buildTypes {
            debug {
                applicationVariants.all { variant ->
                    variant.outputs.each { output ->
                        output.outputFile = new File(output.outputFile.parent,
                                output.outputFile.name.replace("-debug", "-" + defVersionName));
                    }
                }
            }
            release {
                signingConfig signingConfigs.sign
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
                applicationVariants.all { variant ->
                    variant.outputs.each { output ->
                        output.outputFile = new File(output.outputFile.parent,
                                output.outputFile.name.replace("-release", "-" + defVersionName));
                    }
                }
            }
        }
    }
}
```
