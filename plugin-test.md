---
layout: default
title: プラグインのテスト
---

---

# {{ page.title }}

## Create a Test 

- Even when make a Plugin, if we inherit test class of EC-CUBE , we can create unit test easily.

- In this chapter, I use simple plugin as an example to explain how to use continuous integration when "Creating a test and push it on  repository"

### Goal of Test

- Check that doest it operate in version of main EC-CUBE.

- When improve plugin used continous, we check dose it run in other version in the pass of EC-Cube.

### Unitest order

1. Creat code of test in local.

1. Carry out Unit test in local and check that is there any problems

1. Push to personal GitHub repository

1. Use "Travis" to test in order to check does it meet with EC-CUBE 3 target environment or not.

### Prerequisite

1. Plugin has been created first then check action of install・uninstall and basic function.

1. This time, only create plugins as examples below.

1. Please clone the following and refer.
  - <a href="https://github.com/geany-y/ExamleTest" target="_blank">ExampleTestプラグイン</a>

1. Test target this time is service class [ExampleService.php] of above repository.

### File function of Test target

1. The method [getPluginInstallDateFormatJa] in [ExamleService.php] is the target method.

1. In case passing plugin code as an argument, get the date this plug-in was installed from ** dtb_plugin **.

1. If do not find out plugin had suitable code, return false.

### Create test file

1. Please create folder as below.

  - /app/Plugin/[folder name of Plugin you made]/Tests/Service
  - Because way to create folder changes by the environment, I will omit it.
  - Inside the folder is similar with root directory structure of Plugin, if there is Service, please create in Service folder
  - Example this time is created directly in the folder below

  ---

  ![テストフォルダの作成](images/img-plugin-test-create-folder.png)

  ---

2.Create file next

  - Please create base on the order below.
  - In this example, because we create only the service test, please copy / rename the following files.
    - [EC-CUBEインストールディレクトリ]/tests/Eccube/Tests/Service/ShoppingServiceTest.php
  - After copying, delete all but except methods of initialization・ending process such as setUp and tearDown.

3.以下のファイルを作成済みの**/app/Plugin/[自身で作成したプラグインフォルダ名]/Tests/Service**にコピーします。

  - 以下の様に修正・メソッドの追記を行います。

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/plugin_test/ExampleServiceTest.php"></script>

<!--
```
<?php

/*
 * This file is part of the ExampleTest
 *
 * Copyright (C) 2016 LockOn
 *
 * For the full copyright and license information, please view the LICENSE
 * file that was distributed with this source code.
 */

namespace Plugin\ExampleTestPlugin\Tests\Service; ★テストファイルの名前空間を記述

use Eccube\Tests\EccubeTestCase;
use Plugin\ExampleTestPlugin\ServiceProvider\ExampleTestServiceProvider; ★テスト対象ファイルの名前空間定義

class ExampleServiceTest extends EccubeTestCase ★クラス名称を修正
{
    public function setUp() ★テスト開始時に行う処理があれば記述
    {
        parent::setUp();
    }

    /**
     * プラグインのインストール時間取得メソッド失敗パターンのテスト
     * ・インストールされていないコードをサービスに渡す
     * ・戻り値としてfalseが返却される
     */
    public function testGetPluginInstallDateFormatJaFromErrorCode() ★まず正常系エラーのメソッドを追記します
    {
        $errorCode = 'Test'; ★インストールされていないプラグインコードを設定
        $this->actual = $this->app['eccube.plugin.service.example']->getPluginInstallDateFormatJa($errorCode); ★取得値はactualに格納

        $this->assertFalse($this->actual); ★falseが返却される事を定義
    }

    /**
     * プラグインのインストール時間取得メソッド成功パターンのテスト
     * ・インストールされているコードをサービスに渡す
     * ・事前にメソッドと同じ条件でデーターベースからインストール日付を取得しておく
     * ・戻り値としてインストール日付が返却される
     */
    public function testGetPluginInstallDateFormatJaFromSuccessCode() ★次は正常系の正常値テストのメソッドを追記します
    {
        $successCode = 'ExampleTest'; ★今回インストールしたプラグインのコードを記述します

        $qb = $this->app['orm.em']->createQueryBuilder(); ★テスト対象のサービスで取得する値を手動で取得します。
        $qb->select('p.create_date')
            ->from('\Eccube\Entity\Plugin', 'p')
            ->where('p.code = :Code')
            ->setParameter('Code', $successCode);

        try {
            $date = $qb->getQuery()->getSingleResult();
            $this->expected = $date['create_date']->format('Y年m月d日 H時i分s秒'); ★比較値をexpectedに格納します
        } catch (\NoResultException $e) {
            throw new \NoResultException();
        }

        $this->actual = $this->app['eccube.plugin.service.example']->getPluginInstallDateFormatJa($successCode); ★取得値をactualに格納します

        $this->assertEquals($this->actual, $this->expected); ★actualとexpectedが同一である事を定義
    }
}
```
-->

- 上記の説明を行います。

    1. コピー後、編集ファイルの名前空間の修正を行います。
    - 今回作成するファイルの格納フォルダを指定してください。

    1. 今回テスト対象の、ExampleServiceの名前空間を指定します。

    1. クラス名を、今回テスト対象ファイルのあわせて変更します。

    1. メソッドを追加します。
    - はじめに正常系のエラーテストを追加しています。
    - 次に正常系のテストを追加しています。

#### 備考

  - 本章では、テストコードの書き方については、一切説明を行いません。
  - 以下を参考に作成を行なってください。
  - <a href="http://qiita.com/chihiro-adachi/items/f2fd1cbe10dccacb3631" target="_blank">EC-CUBE 3のメモ - ユニットテスト -</a>

### ローカルでのテストの実行

- ここまでの作業でテストが作成できました。
- 一度ローカルで確認して、単一環境で機能として問題ないか確認をおこないます。

  1.コンソールを起動する。

  - ご自身の環境に合わせたコンソールを起動してください。
  - ※Windows環境であれば、環境パスに、PHPの実行パスは指定済みとします。

  2.以下の様に**EC-CUBE 3のインストールディレクトリ**に移動してください。

---

![EC-CUBE 3インストールディレクトリ](images/img-plugin-test-open-console.png)

---

  3.以下コマンドを実行します。

```
vendor/bin/phpunit ./app/Plugin/[自身で作成したプラグインのフォルダ名]
```

- 内容が正しければ、以下の様な内容が表示されるはずです。


---

![ユニットテスト結果](images/img-plugin-test-unit-result.png)

---

### 継続的インテグレーションを使った複数環境でのテスト

- 前項で問題がなければ、自身のGitHub環境にプッシュし、継続的インテグレーションを提供する、「Travis-CI」で複数環境でのテストをおこないます。

#### Travis-CI設定ファイルの作成

1.プラグインのルートディレクトリに**.travis.yml**を作成します。

2.今回の例では以下フォルダが該当です。

  - [EC-CUBE 3インストールディレクトリ]/app/Plugin/ExampleTest

3.フォルダ内にファイルを作成し、以下を記述します。

  - 変更が必要な箇所のみ★印を付与して説明しています。

  - .travis.yml

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/plugin_test/Travis.yml"></script>

<!--
```

language: php

sudo: false

cache:
  directories:
    - $HOME/.composer/cache
    - /home/travis/.composer/cache

php: ★テスト対象のPHPバージョンを指定します
  - 5.3
  - 5.4
  - 5.5
  - 5.6
#  - 7.0

env:
  # plugin code
  global:
    PLUGIN_CODE=ExampleTest ★作成したプラグインのコードを指定します
  matrix:
#    # ec-cube master ★EC-CUBE 3のバージョンを指定します
#    - ECCUBE_VERSION=master DB=mysql USER=root DBNAME=myapp_test DBPASS=' ' DBUSER=root
#    - ECCUBE_VERSION=master DB=pgsql USER=postgres DBNAME=myapp_test DBPASS=password DBUSER=postgres
    - ECCUBE_VERSION=3.0.9 DB=mysql USER=root DBNAME=myapp_test DBPASS=' ' DBUSER=root
    - ECCUBE_VERSION=3.0.9 DB=pgsql USER=postgres DBNAME=myapp_test DBPASS=password DBUSER=postgres
    - ECCUBE_VERSION=3.0.10 DB=mysql USER=root DBNAME=myapp_test DBPASS=' ' DBUSER=root
    - ECCUBE_VERSION=3.0.10 DB=pgsql USER=postgres DBNAME=myapp_test DBPASS=password DBUSER=postgres

matrix:
  fast_finish: true

before_script: ★Travisを初期化しています
  # archive plugin
  - tar cvzf ${HOME}/${PLUGIN_CODE}.tar.gz ./*
  # clone ec-cube
  - git clone https://github.com/EC-CUBE/ec-cube.git
  - cd ec-cube
  # checkout version
  - sh -c "if [ ! '${ECCUBE_VERSION}' = 'master' ]; then  git checkout -b ${ECCUBE_VERSION} refs/tags/${ECCUBE_VERSION}; fi"
  # update composer
  - composer selfupdate
  - composer install --dev --no-interaction -o
  # install ec-cube
  - sh eccube_install.sh ${DB} none
  # install plugin
  - php app/console plugin:develop install --path=${HOME}/${PLUGIN_CODE}.tar.gz
  # enable plugin
  - php app/console plugin:develop enable --code=${PLUGIN_CODE}

script: ★プラグインをインストールしています
  # exec phpunit on ec-cube
  - phpunit app/Plugin/${PLUGIN_CODE}/Tests

after_script: ★プラグインの、インストール・アンインストール、有効化・無効化のテストを行なっています
  # disable plugin
  - php app/console plugin:develop disable --code=${PLUGIN_CODE}
  # uninstall plugin
  - php app/console plugin:develop uninstall --code=${PLUGIN_CODE}
  # re install plugin
  - php app/console plugin:develop install --code=${PLUGIN_CODE}
  # re enable plugin
  - php app/console plugin:develop enable --code=${PLUGIN_CODE}
```
-->

- 上記の設定項目内容を以下に説明します。

1. [php：]
  - phpの各バージョンを設定します。
  - 組み合わせ数が多くなるため、5.3と5.6のみなど、ある程バージョンを絞る方が適切です。

1. [env：> global：]
  - PLUGIN_CODEの右辺に作成したプラグインのコードを指定します。

1. [env：> matrix：]
  - テストのマトリクスの指定です。
  - ここでEC-CUBEの各バージョンを設定します。

1. [befoe_script：]
  - プラグインのユニットテストを実行するまでの前準備です。
    - 処理フローは以下となります。
      - プラグインをパッケージング(tarでアーカイブ)
      - ec-cube本体をclone
      - envで指定したec-cube本体のバージョンにcheckout
      - ec-cube本体のインストール
      - プラグインのインストール

1. [script：]
  - phpユニットテストを実行しています。
  - ここでインストールされたプラグインに内包されているユニットテストが実行されます。

- 以下にTravisの設定ファイルの参考を記述しておきますので、参考としてください。

- <a href="https://github.com/EC-CUBE/coupon-plugin/blob/master/.travis.yml" target="_blank">.travis.yml(参考)</a>

#### Travis-CIとGitHubの連携

- GitHubにログイン済みの状態で以下にアクセスし、連携をONにします。

- `https://travis-ci.org/profile/[user]` 

- 表示されているレポジトリの一覧から、該当レポジトリのボタン表示をスライドさせ緑色でONの状態で連携完了です。


#### GitHubへのプッシュ

- 完了したら、自身のレポジトリにプッシュを行うと自動でTravis-CIが稼働し、テストを行います。

- テスト結果はGitHubとTravis-CIを設定したページで確認できます。
