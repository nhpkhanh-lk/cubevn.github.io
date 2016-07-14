---
layout: default
title: Web API β version Plugin startup Guide
---

---

# EC-CUBE API β version Plugin Startup Guide

## Contents of this chapter

- EC-CUBE API β version
    - Install method
    - Operating confirmation method that used Swagger
    - Document that needs for developing

- Target
	- Based on OAuth2.0 and OpenID Connect to assume about the person who has experience in using authentication /API   
	（Create liking App of Facebook/Twitter/Google)

- Revelant page  
	[API開発指針](/api.html)
	[Web API Authorization ガイド](/api_authorization.html)  

## β version  Basic requirement

1. Can CRUD all tables
    - DELETE is just table which exists del_flg
    - public アクセス可能なテーブル、Customer 認証時に参照/更新可能なテーブル、 フィールドは別途定める
    - API definition depends on Table structure
    - The hashed data will use as character string
1. β version is testing implementation, so perhaps spec will change until official release. 
	- Name of Endpoint
	- Getable data structure (depend on Table Layout in EC-CUBE)
	- Access limitation to each Table
	- etc..
1. Authentication base on OAuth2.0/OpenID Connect 
    - There us spec which un-support one part (Plan that supports later)
    - There is parameter which is changing into 「推奨→必須」(recommendation ->required) in spec of OpenID Connect in order to strenghthen security.
    - Safety of Authentication depends on  SymfonySecurity, php-oauth2-server, EC-CUBE 3.0のプラグイン機構に依存
1. シングルサインオンの機能はサポートしない
1. API ドキュメント生成に使用している swagger-ui は master ブランチの未リリースのものを使用（SHA:b856d6c）
1. PHP5.4以降にて動作確認
1. PostgreSQL9.2, MySQL5.5, SQLite3 にて動作確認
1. <a href="https://github.com/EC-CUBE/eccube-api/issues" target="_blank">現状把握している課題(Issue)</a>

## Installation

Please get from EC-CUBE Owner's store.  
  
<a href="http://www.ec-cube.net/products/detail.php?product_id=1116" target="_blank">プラグインダウンロードページ</a>  
  
※β version is trial release, so please **本番環境にはインストールしない** (do not install in real environment)

## public アクセス可能なAPIエンドポイントへのアクセス

- EC-CUBE APIβ版プラグインインストール後に、下記のURLにアクセスすることで、商品情報を取得できます。

	URL： http://<サイトURL> /api/v0/product  
	レスポンス形式： JSON形式

    ※public アクセス可能なAPIの一覧については、本章の`APIエンドポイント一覧`を参照してください。

## 認証が必要なAPIエンドポイントへのアクセス

public アクセス不可のAPIエンドポイントを使用する場合は、クライアントの登録と認証が必要です。  
このドキュメントではSwaggerをクライアントとして使用して、動作確認を行います。  
以下に手順を示します。

### 1. APIクライアントの登録	

認証を行うにはクライアントの登録が必要です。  
管理画面>設定>メンバー管理 から`APIクライアントの追加`を行ってください。

---

![EC-CUBE管理API登録画面1](/images/img-webapi-b-register-client-1.png)

---

![EC-CUBE管理API登録画面2](/images/img-webapi-b-register-client-2.png)

---

登録ボタンを押してクライアントの登録を完了します。  
このドキュメントでは`Swagger`を利用して動作確認をするため、「redirect_uri」の項目は登録画面の最下部の説明のとおりに設定してください。

---

![EC-CUBE管理画面Swaggerリダイレクト](/images/img-webapi-b-swgger-redirect.png)

---

### 2. クライアントとしてSwaggerを利用した動作確認

EC-CUBE 3.0 では、認証方法として`OAuth2.0 Authorization`及び`OpenID Connect`をサポートしています。  
この認証に対応したクライアントとして、`Swagger`を利用して動作確認を行います。  
「APIドキュメントを開く」をクリックすると`Swagger`の画面にアクセスできます。
 
---

![EC-CUBE管理画面Swaggerリンク](/images/img-webapi-b-swgger-access.png)

---

### 3. 認証手順

1. 画面右上の「Authorize」ボタンを押下します。
1. 使用したいスコープのチェックボックスをONにして子画面内の「Authorize」ボタンを押してください。  
EC-CUBEの管理画面に遷移するので、ここで「許可する」を選択すると、そのスコープにアクセスできるようになります。  
（内部的にはアクセストークンを取得した状態になります）
 

---

![Swagger認証](/images/img-webapi-b-swgger-authorize.png)

---

![Swagger認証コールバック](/images/img-webapi-b-swgger-authorize-callback.png)

---

- ご注意
    - Swagger側で選択したスコープに関わらず、全てのスコープに対してリクエストが行われてしまいますが、これはSwagger不具合によるものです。EC-CUBE APIの認証の不具合ではありません。

### 4. 商品情報をGET（取得）する
1. 「GET /api/v0/product」を選択します。
1. 「実際に実行」ボタンを押下すると、商品情報を取得できます。

---

![SwaggerGetSample](/images/img-webapi-b-swgger-get-sample.png)

---
 
### 5. 商品情報をPOST（作成）する
1. 「POST /api/v0/product」を選択します。
1. 画面の①部分にパラメータのサンプルが表示されています。
1. 画面の①部分をクリックすると、②部分にサンプルが挿入されます。
1. 「実際に実行」ボタンを押下すると、商品情報を作成できます。

---

![SwaggerPostSample](/images/img-webapi-b-swgger-post-sample.png)

---


再度、商品情報をGET（取得）すると、商品情報が追加できている事を確認できます。  
  
レスポンスコードが401の場合は認証に失敗しています。  
認証をやり直すか、それでもうまくいかない場合は本章のトラブルシューティングを確認してください。  
 
## クライアントの認証フローについて

EC-CUBE 3.0の認証が必要なAPIにアクセスするためには、クライアントにOAuth2.0, OpenID Connectの認証フローを実装する必要あります。  
詳細は下記のドキュメントをご確認ください。  
[Web API認証 ( Authorization ) ガイド](http://ec-cube.github.io/api_authorization.html)

### 実装サンプル
<a href="https://github.com/nanasess/eccube3-oauth2-client" target="_blank">PHP(Symfony2) での実装例</a>  
<a href="https://github.com/nanasess/eccube3-oauth2-client-for-python" target="_blank">Python(Flask) での実装例</a>  
<a href="https://github.com/nanasess/eccube3-oauth2-client-for-nodejs" target="_blank">Node.js(Express) での実装例</a>  
<a href="https://github.com/nanasess/DotNetOpenAuth" target="_blank">C# での実装例(Web/Wpf)</a>  
<a href="https://github.com/nanasess/eccube3-oauth2-client-for-java" target="_blank">Java での実装例</a>  
<a href="https://developers.google.com/oauthplayground/" target="_blank">Google OAuth 2.0 Playground</a>  	
    - OAuth 2.0 Configuration -> OAuth endpoint -> Custom にて動作確認済み  
    - Authorization Endpoint に ```?state=<random_state>``` を付与する必要があります

## 利用できるAPIエンドポイントについて

EC-CUBE 3.0では、RESTの原則に基づいたAPIの実装を行っています。  
詳細は下記のドキュメントをご確認ください。  

API開発ドキュメント  
[http://ec-cube.github.io/api](http://ec-cube.github.io/api)  
APIエンドポイント一覧  
[https://github.com/EC-CUBE/ec-cube.github.io/blob/master/documents/api/EC-CUBE_API_Endpoint.pdf](https://github.com/EC-CUBE/ec-cube.github.io/blob/master/documents/api/EC-CUBE_API_Endpoint.pdf)

## APIで取得できる情報について
β版ではEC-CUBE 3.0の各テーブルに対してCRUDアクセスを提供しています。  
そのためAPIから取得したデータの定義は、EC-CUBE 3.0のテーブル定義に依存します。  
EC-CUBE 3.0のテーブル定義は下記を参照してください。  
  
EC-CUBE 3.0テーブル定義  
[https://github.com/EC-CUBE/eccube3-doc/tree/master/ER-D](https://github.com/EC-CUBE/eccube3-doc/tree/master/ER-D)  

## トラブルシューティング

1. 認証したのにレスポンスが401となる

    一部のレンタルサーバーや SAPI CGI/FastCGI の環境では、認証情報(Authorization ヘッダ)が取得できず、 401 Unauthorized エラーとなってしまう場合があります。
    この場合は、 <ec-cube-install-path>/html/.htaccess に以下を追記してください。

    ```
    RewriteCond %{HTTP:Authorization} ^(.*)
    RewriteRule ^(.*) - [E=HTTP_AUTHORIZATION:%1]
    ```
