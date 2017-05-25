# 設定 Runner

這個範例是把 Runner 裝在一台閒置的電腦上，也就是 **Specific Runners**，以下所有的範例也都是以這個為前提做設定


## 1. 安裝 Runner

> 首先你要先確認你的 gitlab 的版本，然後從 [gitlab-ci-multi-runner](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner) 下載安裝適合的 Runner

``` cmd
# install runner whose version is 1.11.2
$ apt-get install gitlab-ci-multi-runner=1.11.2
# or
$ sudo curl --output /usr/local/bin/gitlab-runner https://gitlab-ci-multi-runner-downloads.s3.amazonaws.com/v1.11.2/binaries/gitlab-ci-multi-runner-darwin-amd64

# if you want to install the latest version
$ sudo curl --output /usr/local/bin/gitlab-runner https://gitlab-ci-multi-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-ci-multi-runner-darwin-amd64
```

## 2. 註冊 Runner

### step 1
在 Gitlab repo 的 Runner 頁面，可以看到這個下面的畫面
請記住紅字的 **URL** & **registration token**

![](https://i.imgur.com/XfSYenY.png)

### step 2
在安裝 gitlab-runner 的電腦上執行 `$ gitlab-runner register`

``` cmd
$ gitlab-runner register
WARNING: Running in user-mode.
WARNING: Use sudo for system-mode:
WARNING: $ sudo gitlab-runner...

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
<your_setup_url_in_gitlab_repo>

Please enter the gitlab-ci token for this runner:
<your_token_in_gitlab_repo>

Please enter the gitlab-ci description for this runner:
<your_description>

Please enter the gitlab-ci tags for this runner (comma separated):
<your_runner_tag>

Whether to run untagged builds [true/false]:
[false]: false

Registering runner... succeeded

Please enter the executor: shell, ssh, virtualbox, docker+machine, docker-ssh+machine, kubernetes, docker, docker-ssh, parallels:
shell
```
> 因為是 Runner 裝在自己的電腦，所以 executor 直接使用 **shell**

## 3. 啟動 gitlab
``` cmd
$ gitlab-runner install
$ gitlab-runner start
```


## Reference
- [Runner](http://docs.gitlab.com/ce/ci/runners/README.html#shared-vs-specific-runners)
- [gitlab-ci-multi-runner](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner)
- [Install GitLab Runner](https://docs.gitlab.com/runner/install/)
