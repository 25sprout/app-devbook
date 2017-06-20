# Android Code Check Plugin

## Findbugs

### Bug Categories
![](https://i.imgur.com/Hmg11xg.png)
![](https://i.imgur.com/GKDwj3L.png)



### Check before launch
Add gradle task in the bottom of window.
![](https://i.imgur.com/EmkpWuo.png)



### Gradle Setting
```java
android {
    ...

    task findbugs(type: FindBugs, dependsOn: 'assembleDebug') {
        ignoreFailures = false
        // The higher level only shows more serious problem
        reportLevel = "medium"
        excludeFilter = new File("${project.rootDir}/findbugs/findbugs-android-exclude.xml")
        classes = files("${project.rootDir}/app/build/intermediates/classes")
        source = fileTree('src/main/java/')
        classpath = files()
        reports {
            // FindBugs tasks can only have one report enabled
            xml.enabled = false;
            html.enabled = true
            html {
                destination "$project.buildDir/reports/findbugs-results.html"
            }
        }
    }
    ...
}
```

### Exclude Filter XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<FindBugsFilter>
    <!-- http://stackoverflow.com/questions/7568579/eclipsefindbugs-exclude-filter-files-doesnt-work -->
    <Match>
        <Or>
            <Class name="~.*\.R\$.*"/>
            <Class name="~.*\.Manifest\$.*"/>
        </Or>
    </Match>
</FindBugsFilter>
```

## Android Lint
### Gradle Setting
```java
    lintOptions {
        // If set to true, turns off analysis progress reporting by lint.
        quiet false
        // if set to true (default), stops the build if errors are found.
        abortOnError true
        // if true, only report errors.
        ignoreWarnings false
        checkAllWarnings true
        xmlReport false
    }
```

## Use One Line To Run All Check with Gradle
```java
check.dependsOn 'findbugs', 'lint', 'checkStyle'
```


## Compare
![](https://i.imgur.com/mngBIpE.png)

