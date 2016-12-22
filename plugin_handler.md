---
layout: default
title: ハンドラによる優先制御仕様
---

---

# {{ page.title }}

Priority control between EC-CUBE 3 Plugins

## Issue
When install number of Plugin hooking the same event at the same time in operating environment then rewrite event handler of each Plugin into parameters, unexpected action may occur depend on the order.
In addition, I also want to control the starting order of handlers need to start before or after other Plugin such as inspector, logging, debugging.

##### Example
When rewrite Plugin using FormEvent for card payment when purchase or deferred payment into event handler, element of rewrite form need to use in later handler can be deleted in previous handler.

## Solution
Make it able for user to set the starting order of each event handler in Management screen
Ex：lauch event handler of FormEvent likes order: deferred payment(3)→Card payment(2) →inspector(1)
Starting order in Management screen can be saved to handler priority table (temporary) in DB 
Corresponding to specification of EventDispatcher of Symfony, order of execution is descending order of -511 to + 511

### Table structure
The handler priority table have the following fields

* id:int    (surrogate key)
* event:string not null (event name)
* priority:int not null (priority)
* plugin_id:int not null(plugin ID)
* handler:string not null(handle name)

    2 fields event,priority are unique

## Starting priority and handler type

* 優先度は+400～-399は**通常型ハンドラ**用とする
* 優先度-400～-499は**後発型ハンドラ**用とする(デバッグ、監査用を想定)
* 優先度+500～+401は**先発型ハンドラ**用とする(同上)
* 優先度0は実行時にハンドラを登録しない(ハンドラを無効化)

## プラグインインストール時の動作

* 各プラグインのgetSubscribedEvents内のイベントハンドラと優先度をハンドラ優先度テーブルに挿入する
* 当該イベントにハンドラが1件も登録されていない場合のデフォルト優先度は以下のとおり
* * 先発型:500
* * 通常型:400
* * 後発型:-400

* ハンドラが既に登録されている場合、既に存在する同型ハンドラ
のイベントの優先度の最大値-1を登録する
（ハンドラが既に400に登録されている場合に通常ハンドラを登録すると399に登録される）
* ハンドラ種別毎に決められた優先度枠に空きがない場合、プラグインのインストールは失敗する

## Webアプリケーション実行時の動作

* プラグインのイベンドハンドラをディスパッチャに登録する際(Application.php等)、優先度テーブルの優先度(昇順)に各ハンドラを登録する
* イベントが実際に発生すると登録された順にハンドラが起動する
* 優先度テーブルに登録されていないイベントハンドラがプラグインに定義されている場合、優先度-500(全てのハンドラの後)とみなされる

## ハンドラ優先度変更画面の動作
* 各ハンドラの優先度をユーザ入力に基づいてアップデートする
* ただし、優先度はハンドラ種別（通常、先発、後発）毎の範囲内に限定される

## プラグイン開発者への影響

* getSubscribedEventsはハンドラのメソッド名と優先度（数値）を返すが、メソッド名と種別（通常、先発、後発）を返すように変更
* （開発者向けの指針）通常型プラグインとの衝突を防ぐため、先発型、後発型のハンドラでは渡されたパラメータを書き換えないことが望ましい
