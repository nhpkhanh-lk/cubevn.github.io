---
layout: default
title: フォーム情報を整理して入力値チェックも追加しよう
---

---

# {{ page.title }}


## FormType

- Divide contents of Form definition that created in previous chapter in FormType

- Purpose is that if definition of Form is collected in one place, Maintenance becomes easy.

- When create Form in EC-CUBE 3, normally use method of this chapter. 

### Menu of this chapter

- In this chapter, conduct the following part

    1. Create FormType file

    1. Explain definition method of Validation

    1. Explain about registration method to Service Provider in order to use FormType from Controller.

    1. Explain about method of transferring Request for Controller method.

    1. Explain about method that use FormType file from Builder to create Form Object

    1. Explain about connection of Request and Form Object

    1. Explain about judgement method of Validation

    1. Explain in brief about Validation mechanism

    1. Modify Twig file

    1. Explain about judgement in Twig file

    1. Explain about the change method of the display contents based on judgement of Twig file

### Creation of FormType

#### Creation of File

- FormType is saved in below

1. /src/Eccube/Form/Type/Front
    - 「Type」フォルダまでは、「管理画面」「ユーザー画面」共通です
      1. 管理画面 : Admin
      1. ユーザー画面 : Front

#### Rename file 

- •	Next, create CrudType.php

- •	Copy ContactType.php, rename

    - **CrudType.php**(contents will copy ContactType.php)

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudType_before.php"></script>

<!--
```
<?php
/*
 * This file is part of EC-CUBE
 *
 * Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.
 *
 * http://www.lockon.co.jp/
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program; if not, write to the Free Software
 * Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
 */


namespace Eccube\Form\Type\Front;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\Validator\Constraints as Assert;

class ContactType extends AbstractType  ★名称の変更
{
    public $config;

    public function __construct($config)
    {
        $this->config = $config;
    }

    /**
     * {@inheritdoc}
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder  ★以下を編集する
            ->add('name', 'name', array(
                'required' => true,
            ))
            ->add('kana', 'kana', array(
                'required' => false,
            ))
            ->add('zip', 'zip', array(
                'required' => false,
            ))
            ->add('address', 'address', array(
                'required' => false,
            ))
            ->add('tel', 'tel', array(
                'required' => false,
            ))
            ->add('email', 'email', array(
                'required' => true,
                'constraints' => array(
                    new Assert\NotBlank(),
                    new Assert\Email(),
                ),
            ))
            ->add('contents', 'textarea', array(
                'help' => 'form.contact.contents.help',
                'constraints' => array(
                    new Assert\NotBlank(),
                ),
            ))
            ->addEventSubscriber(new \Eccube\Event\FormEventSubscriber());
    }

    /**
     * {@inheritdoc}
     */
    public function getName()
    {
        return 'contact';  ★名前を編集する
    }
}
```
-->

#### Creation of CrudType

- Change above as below

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudType_after.php"></script>

<!--
```
<?php
/*
 * This file is part of EC-CUBE
 *
 * Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.
 *
 * http://www.lockon.co.jp/
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program; if not, write to the Free Software
 * Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
 */


namespace Eccube\Form\Type\Front;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\Validator\Constraints as Assert;

class CrudType extends AbstractType  ★CrudTypeに変更
{
    public $config;

    public function __construct($config)
    {
        $this->config = $config;
    }

    /**
     * {@inheritdoc}
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        // 投稿種別の配列
        $post_type = array( ★セレクトボックスの値生成
            '1' => '質問',
            '2' => '提案',
        );

        $builder->add( ★以下をコントローラーから引用
            'reason',
            'choice',
            array(
                'label' => '投稿種別',
                'required' => true,
                'choices' => $post_type, ★上部で宣言した、セレクトボックス値を設定します
                'mapped' => false,
                'expanded' => false,
                'multiple' => false,
            )
        )
        ->add(
            'title',
            'text',
            array(
                'label' => '投稿のタイトル',
                'required' => false,
                'mapped' => false,
            )
        )
        ->add(
            'notes',
            'textarea',
            array(
                'label' => '内容',
                'required' => false,
                'mapped' => false,
                'empty_data' => null,
                'attr' => array(
                    'style' => 'height:100px;', ★高さを設定
                ),
            )
        )
        ->addEventSubscriber(new \Eccube\Event\FormEventSubscriber());
    }

    /**
     * {@inheritdoc}
     */
    public function getName()
    {
        return 'crud';★名前を編集する
    }
}
```
-->

- Transfer Form structure that described in Controller above, to [FormType] 

#### Definition of the input value check (Validation)

- I created FormType already above, here, I will define the input value check (is called that Validation) of each Item of Form.
- I will add in Option column of each Item of the created [CrudType] 

    - CrudType.php

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudType_add_valodate.php"></script>

<!--
```
<?php
/*
 * This file is part of EC-CUBE
 *
 * Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.
 *
 * http://www.lockon.co.jp/
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program; if not, write to the Free Software
 * Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
 */


namespace Eccube\Form\Type\Front;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\Validator\Constraints as Assert; ★バリデーションを追加する際は、必ず必要となってきます。

class CrudType extends AbstractType
{
    public $config;

    public function __construct($config)
    {
        $this->config = $config;
    }

    /**
     * {@inheritdoc}
     */
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        // 投稿種別の配列
        $post_type = array(
            '1' => '質問',
            '2' => '提案',
        );

        $builder->add( ★これ以降にバリデーションを追記
            'reason',
            'choice',
            array(
                'label' => '投稿種別',
                'required' => true, ★必須を有効に変更
                'choices' => $post_type,
                'mapped' => false,
                'expanded' => false,
                'multiple' => false,
            ),
        )
        ->add( ★ハンドルネームの項目追加
            'name',
            'text',
            array(
                'label' => '投稿者ハンドルネーム',
                'required' => true,
                'mapped' => false,
                 new Assert\Regex( ★正規表現でのバリデーション
                    array(
                        'pattern' => '/^[^\da-zA-Z]+$/u', ★条件
                        'message' => '半角英数字のみ入力可能です。', ★エラー時表示メッセージ
                    )
                )
            )
        )
        ->add(
            'title',
            'text',
            array(
                'label' => '投稿のタイトル',
                'required' => false,
                'mapped' => false,
                'constraints' => array(
                    new Assert\Length( ★文字入力の長さをチェック
                        array(
                            'min' => '0',
                            'max' => '100',
                            'maxMessage' => '100文字以内で入力してください',
                        )
                    )
                )
            )
        )
        ->add(
            'notes',
            'textarea',
            array(
                'label' => '内容',
                'required' => false,
                'mapped' => false,
                'empty_data' => null,
                'attr' => array(
                    'style' => 'height:100px;',
                ),
                'constraints' => array(
                    new Assert\Length( ★文字入力の長さをチェック
                        array(
                            'min' => '0',
                            'max' => '100',
                            'maxMessage' => '1000文字以内で入力してください',
                        )
                    )
                )
            )
        )
        ->addEventSubscriber(new \Eccube\Event\FormEventSubscriber());
    }

    /**
     * {@inheritdoc}
     */
    public function getName()
    {
        return 'crud';
    }
}
```
-->

#### The description method of Validation

- As describing above, Validation will add by the following syntax

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudType_example.php"></script>

<!--
```
->add(
  [name属性],
  [type属性],
  array( ★オプション(連想配列)
    [オプションキー] => [設定値],
    'constraints' => array( ★バリデーション設定開始(連動配列で多重に設定可能)
      new Assert\Length( ★該当となるバリデーションのクラスをインスタンス化
        array( ★連想配列でバリデーションのオプション値を設定
          'min' => '0',
          'max' => '100',
          'maxMessage' => '1000文字以内で入力してください',
        )
      )
    )
  )
)
```
-->

#### Validation that is using this time

<a href="http://symfony.com/doc/current/reference/constraints/Length.html" target="_blank">Length</a>

<a href="http://symfony.com/doc/current/reference/constraints/Regex.html" target="_blank">Regex</a>

#### Kind of Validation

- Please confirm on Site of Symfony2

<a href="http://symfony.com/doc/current/book/validation.html#basic-constraints" target="_blank">Validation</a>

#### 補足

- The following part has been defining at the end of Form Builder in source above

```

->addEventSubscriber(new \Eccube\Event\FormEventSubscriber());

```

- 上記はフォームの処理に対して割り込むイベントを定義していますが、慣例的なものとして、記述を削除しないでくだい。
- 通常は利用の必要がないため、ここでの説明は割愛させていただきます。

### FromTypeのサービス登録Register service of FromType

- FormTypeの定義が完了したら、コントローラー内「$app」で呼び出せる様に、**サービスプロバイダー**への登録が必要です。

#### Modify Service Provider 

- **ServiceProvider**has been saving in below

    1. /src/Eccube/ServiceProvider

        - **EccubeServiceProvider.php**in folder is the corresponding file.

        - This time is [FormType] that relates to User screen (Front screen). Thus, when you open file, please search [front].
        - If you search, [Type定義](Type definition) that relates to User screen is collected, so define the created [CrudType] in the that lowest part as below.
            - EccubeServiceProvider.php

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/EccubeServiceProvider_add_type.php"></script>

<!--
```
// front
$types[] = new \Eccube\Form\Type\Front\EntryType($app['config']);
$types[] = new \Eccube\Form\Type\Front\ContactType($app['config']);
$types[] = new \Eccube\Form\Type\Front\NonMemberType($app['config']);
$types[] = new \Eccube\Form\Type\Front\ShoppingShippingType();
$types[] = new \Eccube\Form\Type\Front\CustomerAddressType($app['config']);
$types[] = new \Eccube\Form\Type\Front\ForgotType();
$types[] = new \Eccube\Form\Type\Front\CustomerLoginType($app['session']);
$types[] = new \Eccube\Form\Type\Front\CrudType($app['config']); ★追記
```
-->

- This time is not required, but I am transferring Config into in parameter.

### Modification of Controller

- Next, remove setting of Form Item that described on Controller, and describe loading of CrudType.

- First, remove all of Form definition parts from place that generating Builder by **createBuilder**

- Become as below

    - CrudController.php

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudController_remove_form.php"></script>

<!--
```
<?php
/*
 * This file is part of EC-CUBE
 *
 * Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.
 *
 * http://www.lockon.co.jp/
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program; if not, write to the Free Software
 * Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
 */


namespace Eccube\Controller\Tutorial;


use Eccube\Application;
use Eccube\Controller\AbstractController;

class CrudController extends AbstractController
{
    public function index(Application $app)
    {
        //$viewname = 'このビューは「Tutorial/crud_top.twig」が表示されています。';



        return $app->render(
            'Tutorial/crud_top.twig',
            array(
                //'viewname' => $viewname,
                //'forms' => $forms->createView(), ★コメントアウト
            )
        );
    }
}
```
-->

#### Validation of call of FormType and submit value

- Next, check the inputting value for call of FormType in Controller and submitted value.

- Add the following part in Controller 

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/CrudController_call_form.php"></script>

<!--
```
<?php
/*
 * This file is part of EC-CUBE
 *
 * Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.
 *
 * http://www.lockon.co.jp/
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program; if not, write to the Free Software
 * Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
 */


namespace Eccube\Controller\Tutorial;

use Eccube\Application;
use Eccube\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Request; ★リクエストを取得するため追加します。

class CrudController extends AbstractController
{
    public function index(Application $app, Request $request) ★リクエストを引数で取得するために追記します。
    {
        $builder = $app['form.factory']->createBuilder('crud', null); ★ビルダーからType名を指定して、CrudTypeを取得します。

        $form = $builder->getForm();

        $form->handleRequest($request); ★リクエストとフォーム入力を関連させるために、必要です。

        $message = array('default' => '');

        if ($form->isSubmitted() && $form->isValid()) { ★入力値のチェックです
            $message = array('success' => '入力値に問題はありません');
        } elseif($form->isSubmitted() && !$form->isValid()) {
            $message = array('error' => '入力値に誤りがあります');
        }

        $forms = $builder->getForm();

        return $app->render(
            'Tutorial/crud_top.twig',
            array(
                'message' => $message,
                'forms' => $forms->createView(),
            )
        );
    }
}
```
-->

- Conduct explanation of contents above


    1. Firstly, set [名前空間(name space)] and parameter of Method in order to receive Request.
    - Conduct **リクエストクラスの読み込み宣言**(declaration of loading Request class) of [Silex(Symfony2)] in name space
    - Try to set Type-hinting in parameter of method, specify Request type, receice Request Object.
    - About **リクエストオブジェクト**(request  Object), [Silex(Symfony2)] will conduct **自動で処理** (automatically process), and transfer Request contents from Client.
    1. Next, generate **フォームオブジェクト**(Form Object) by method **createBuilder** of **$app[form.factory]** in order to generate Form.
    - Transfer FormType name **crud** that defined by method **getName** of CrudType as the first parameter of createBuilder
    - In the second parameter, this time doesn’t have revelant Entity, so specify [null].
    - Option will not specify.

    1. Next, **関連**(associate) **FormType** with **リクエストオブジェクト**(Request Object) that got by handleRequest 
    - Conduct comparision submit value and FormType value carefully.

    1. Next is the input value check, I think whether I will describe as below or not.

        ```
        if ($form->isSubmitted() && $form->isValid()) { 
        ```

    - This is explanation of the input value check as below

    - First, check whether it is value that is submitted from Form by  [isSubmitted] or not
      - For Security
    - Next, base on contents of FormType in [isValid] to conduct the input value check
      - If there is no problem in the input value, return **true**, if there os problem in contents, return **false**.
      - In Form Object,about Item that there is error, its error message is also stored so we can display error message by setting of View.
      - ※However,in order to **バリデーションエラー**(Validation error) is stored,**エンティティが必要ですが**(Entity is required). In this chapter, Entity is not stored, so maybe error will not be displayed automatically in View. Thus, this time will judge by Controller, and transfer message according to judgement for View.

### Modification of Twig file

- Finally, lets modify Twig file

    - **crud_top.twig**

    - Currently, this is display as below

<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/crud_top_add_form_before.twig"></script>

<!--
```
｛＃
This file is part of EC-CUBE

Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.

http://www.lockon.co.jp/

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
＃｝
｛％ extends 'default_frame.twig' ％｝

｛％ set body_class = 'front_page' ％｝

｛％ block javascript ％｝
｛％ endblock ％｝

｛％ block main ％｝
    <div class="row">
       <div class="col-sm-12">
            <div class="main_wrap">
                <h1>CRUDチュートリアル</h1>
                <p>投稿を行なってください</p>
                <dl>
                    <dt>コントローラーから取得した変数です</dt>
                    <dd>
                        ｛＃｛｛ viewname ｝｝＃｝★削除を行いメッセージ表示欄として利用します
                    </dd>
                </dl>
            </div>
           <div id="form-wrapper"> ★Form定義を追記します。
               ｛｛ form_widget(forms._token)｝｝
               ｛｛ form_widget(forms)｝｝
           </div>
        </div>
    </div>
｛％ endblock ％｝

```
-->

- コントローラーの修正にあわせて以下の様に追記、変更を行います。


<script src="http://gist-it.appspot.com/https://github.com/EC-CUBE/ec-cube.github.io/blob/master/Source/tutorial_5/crud_top_add_form_after.twig"></script>

<!--
```
｛＃
This file is part of EC-CUBE

Copyright(c) 2000-2015 LOCKON CO.,LTD. All Rights Reserved.

http://www.lockon.co.jp/

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
＃｝
｛％ extends 'default_frame.twig' ％｝

｛％ set body_class = 'front_page' ％｝

｛％ block javascript ％｝
｛％ endblock ％｝

｛％ block main ％｝
    <div class="row">
       <div class="col-sm-12">
            <div class="main_wrap">
                <h1>CRUDチュートリアル</h1>
                <p>投稿を行なってください</p>
               ｛％ if message.error is defined ％｝★コントローラーで入力値判定した結果を表示します。
                <p class="text-danger">｛｛ message.error ｝｝</p>
               ｛％ elseif message.success is defined ％｝
                <p class="text-success">｛｛ message.success ｝｝</p>
               ｛％ else ％｝
                <p class="text-success">｛｛ message.default ｝｝</p>
               ｛％ endif ％｝
            </div>
           <div id="form-wrapper"> ★サブミット出来るようにフォーム定義を追記します
               <form name="bbs-top-form" method="post" action="｛｛ url('tutorial_crud') ｝｝">
               ｛｛ form_widget(forms._token) ｝｝
               ｛｛ form_widget(forms) ｝｝
               </form>
           </div>
        </div>
    </div>
｛％ endblock ％｝
```
-->

- 上記の説明を行います。

    1. Twigでのif文
        - メッセージ表示箇所で、変数の有無を判定しています。
        - **if [変数] is defined**がそうですが、この記述中の**defined**はPHPの**issetと同義**です。
        - Twigの**ロジック部は｛％％｝**で囲っています。
        - 条件により、表示されるタグが異なります。

    1. フォームの追加
        - 次にhtmlのフォームを定義します。
        - **action属性**以外は通常のフォーム定義と同様です。
        - **action属性**に記載されているのが、Twig構文で**url([ルーティング名])**、指定したURLを取得できます。
          - ここで云う**ルーティング名**とは**FrontControllerProvider**でルーティングの設定を行なった際に、**bind()に設定した値**です。

### 表示内容の確認

#### 通常表示

- 最後に確認のためにブラウザにアクセスしてみましょう。

    1. ブラウザのURLに「http://[ドメイン + インストールディレクトリ]/tutorial/crud」を入力してください。

    1. フォームビルダーで構築したフォームが表示されています。

---

![FormTypeのレンダリング](/images/img-tutorial5-view-rendar-default.png)

---

#### 入力値正常表示

- 入力値が正常な場合の表示を確認します。

    1. 投稿ハンドルネームに「a」、投稿のタイトルに「a」を入力

    1. この内容で登録するをクリック

---

![FormTypeのレンダリング](/images/img-tutorial5-view-rendar-success.png)

---

#### 入力値エラー表示

- 入力値にエラーがある場合の表示を確認します。

    1. 投稿ハンドルネームに「あ」、投稿のタイトルに「あ」を入力

    1. この内容で登録するをクリック

---

![FormTypeのレンダリング](/images/img-tutorial5-view-rendar-error.png)

---

## この章のまとめ

1. コントローラーに記述された、Form定義情報をFormTypeに移設しました。
1. FormTypeで、バリデーションを設定しました。
1. サービスプロバイダに作成したFormTypeを登録しました。
1. Twigにformを記述する際、「action」で「url構文」を利用しルーティング名により「URL」の取得を行いました。
1. $app['form.factory']の「createBuilder」の第一引数にフォームタイプ名を記述し、フォーム定義を取得しました。
1. フォーム定義をフォームオブジェクトへ変換した後に、リクエストオブジェクトに紐付けしました。
1. コントローラーのメソッドからリクエストオブジェクトを取得しました。
1. リクエストがサブミット値か、さらに入力値に異常がないかの判定を行いました。
1. 取得結果で、表示文言がかわるように、Twigで「if文」を用いました。
1. Twigの構文「defined」を利用して、変数の有無を判定しました。
