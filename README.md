# GitLab CI のデモ環境

GitLab 8.xから統合されたGitLab CIの機能を試す。

* https://about.gitlab.com/gitlab-ci/
* http://doc.gitlab.com/ci/

## Usage

### 仮想環境を用意する

```
$ vagrant up
```

| サーバ                    | IP              | 解説                         |
| ---                       | ---             | ---                          |
| GitLab + GitLab CI Server | 192.168.133.10  | リポジトリ＆CIレポートサーバ |
| GitLab Runner             | 192.168.133.200 | CIランナー                   |

### GitLabのセットアップをする

ブラウザで http://192.168.133.10 にアクセスしてGitLabのセットアップを行う。

| Username | Password |
| ---      | ---      |
| root     | 5iveL!fe |

### myappプロジェクトの作成

GitLabに`myapp`プロジェクトを作成する。

e.g.) http://192.168.133.10/myuser/myapp

### GitLab Runnerの設定

GitLab Runnerサーバで下記のコマンドでCIサーバを登録する。

トークンの情報はCIのページから該当するプロジェクトのRunnersページで確認する。

e.g.) http://192.168.133.10/ci/projects/1/runners

```
$ vagrant ssh runner
----------------------------------------------------------------
  CentOS 7.1.1503                             built 2016-01-08
----------------------------------------------------------------
[vagrant@localhost ~]$ sudo gitlab-runner register
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/ci):
http://192.168.133.10/ci
Please enter the gitlab-ci token for this runner:
ea0aeb7b24bdb177953d4226f4028e
Please enter the gitlab-ci description for this runner:
[localhost.localdomain]: ci-runner-01
Please enter the gitlab-ci tags for this runner (comma separated):
example,vagrant,perl
Registering runner... succeeded                     runner=3LMx5gEX
Please enter the executor: ssh, shell, parallels, docker, docker-ssh, virtualbox:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
[vagrant@localhost ~]$ exit
```

このデモではCI Runnerが動作するサーバでCIを実行するのでexecutoreには`shell`を指定する。

指定したタグは、該当するプロジェクトのRunnersページで確認できる。

### myappプロジェクトにコードをpush

プロジェクトのルートに`.gitlab-ci.yml`を配置してコードをリポジトリにpushする。

.gitlab-ci.ymlの仕様は[こちら](http://doc.gitlab.com/ci/yaml/README.html)を参考。
各ジョブは独立して実行されるので注意（他のジョブの変更を引き継がない）。

```
$ cd myapp
$ git init
$ git remote add origin git@192.168.133.10:myuser/myapp.git
$ git add .
$ git commit -m 'Initial commit'
$ git push -u origin master
```

### CIの確認

プロジェクトのページ(e.g. http://192.168.133.10/myuser/myapp )から`Continuous Integration`を辿り、CIの結果を確認する。

e.g.) http://192.168.133.10/ci/projects/1/builds/1

