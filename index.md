---
layout: default
title: HOME
description: Here is Document site of EC-CUBE. We provide all information such as Development Guideline, Concept of elemental technology, Tutorial for Development on main EC-Cube and Plugin, Cookbook, etc.
---

---

# EC-CUBE 3 Development Document

## GitHub

- <a href="https://github.com/EC-CUBE/ec-cube" target="_blank">EC-CUBE 3 Development Repository</a>
- <a href="https://github.com/EC-CUBE/ec-cube.github.io" target="_blank">EC-CUBE 3 Development Document Repository</a>

## Quick Start

- [System requirement](/requirement.html)
- [Development environment structure](development-environment.html)
- [How to install](/install.html)
- [How to update](/update.html)

## EC-CUBE 3 Specification

- [Directory and file structure](/spec-directory-structure.html)
  1. Main directory・role
  1. Setting file
  1. Constant
  1. Replacement 2 system・3 system
- [Template searching order](/template.html)
- <a href="https://github.com/EC-CUBE/eccube3-doc/blob/master/feature_list.xls" target="_blank">Function list</a>
- <a href="https://github.com/EC-CUBE/eccube3-doc/tree/master/ER-D" target="_blank">Table・ER Diagram</a>
- <a href="https://github.com/EC-CUBE/eccube3-doc/tree/master/IntegrationTest" target="_blank">Integration test item document</a>

## Plugin Specification

- [Plugin Specification・tutorial](/plugin.html)
- [Install Specification](/plugin_install.html)
- [Priority control specification by handler](/plugin_handler.html) 
- [Develop Plugin using php app/console plugin:develop ](/plugin_console.html)
- [Plugin test](plugin-test.html)

## Web API Specification

- [Web API Plugin start-up Guide verβ](/web-api-doc.html)
- [Web API Development Policy](/api.html)
- [Web API認証 ( Authorization ) Guide](/api_authorization.html)

## Development Guideline
-We provide the main flow and prerequisite knowledge used when you develop 

- [General development](workflow-general-image.html)
- [Coding rule](coding_style.html)
- [Migration guide](migration.html)
- <a href="http://qiita.com/nanasess/items/350e59b29cceb2f122b3" target="_blank">Log design guideline</a>
- [Development step using Git](workflow.html)
- [Customize Reference](customize-reference.html)
1. Created・changed file when customize
2. External component

## Development help

- [デバッグ・Tips](tips.html)

## Technique used in EC-CUBE 3
- We provide overview of Core technique in EC-CUBE 3 and some reference site

	- [Technique list](/architecture.html)
		1. Silex 
		1. Symfony2
		1. Database abstraction layer 
		1. Template engine 
		1. Library management 


## Tutorial

- In turorial, which was made finally

    - Make [CRUD] of database same with screen display.

    - Can get the source completed in this tutorial in link below
    
        - <a href="https://github.com/geany-y/ec-cube/tree/documents/tutorial" target="_blank">GitHub</a>

### Tutorial list

- **Setting URL**
    - [ルーティングとコントローラープロバイダー](tutorial-1.html)

- **Try displaying View from Controller**
    - [ビューのレンダリング](tutorial-2.html)

- **Try transferring variable to screen**
    - [Twig構文とView変数](tutorial-3.html)

- **Try displaying Form**
    - [Formとフォームビルダー](tutorial-4.html)

- **Arrange Form info and add the check input value**
    - [FormType](tutorial-5.html)

- **Let’s create Database**
    - Because this content is explained in [Development Guideline] so we only show the Table specification of this Tutorial 
    - Please refer the link below for detail
        - [マイグレーションガイド](migration.html)
        - [本チュートリアルのテーブル定義](tutorial-6.html)

- **Let’s set Database structure for Doctrine**
    - [データーベーススキーマ定義](tutorial-7.html)

- **Let’s create Entity file for Doctrine**
    - [エンティティ](tutorial-8.html)

- **Let's register Database**
    - [エンティティマネージャーを利用した情報の登録](tutorial-9.html)

- **Let's get information from database and display as Table list**
    - [データーベース情報の取得とViewのループ処理](tutorial-10.html)

- **Let's arrange Database operation processing in repository**
    - [レポジトリとデータベース操作](tutorial-11.html)

- **Let's edit the list**
    - [条件検索とアップデート処理](tutorial-12.html)

- **Let's delete unnecessary information**
    - [レコードの削除](tutorial-13.html)


## Cookbook

- In this cookbook, different from tutorial, we explain how to customize more practically.

### Adding management screen item

1. [Customize Main](cookbook-1-cube3-customize-admin-add.html)

### How to add GoogleAnalitics

1. [Add JavaScript using Management function block](cookbook-2-cube3-customize-js.html)
