# Laravel API実装手順（TDD）

Controller → Action → Service → Eloquent Model
の依存関係を前提とした実装手順。

## 基本方針

-   テストを先に作成する。
-   Red → Green → Refactor を繰り返す。
-   Serviceはドメイン単位で作成し、同一ドメインのCRUDを集約する。
-   Actionはユースケース単位で作成する。
-   Repositoryは設けず、ServiceからEloquent Modelを直接利用する。

------------------------------------------------------------------------

## 1. ファイル作成チェックリスト

例として、ユーザー登録・取得・更新・削除APIを実装する場合。

### 1.1 アプリケーション側

-   [ ] `app/Http/Controllers/UserController.php`
-   [ ] `app/Http/Requests/StoreUserRequest.php`
-   [ ] `app/Http/Requests/UpdateUserRequest.php`
-   [ ] `app/Http/Resources/UserResource.php`
-   [ ] `app/Actions/User/CreateUserAction.php`
-   [ ] `app/Actions/User/FindUserAction.php`
-   [ ] `app/Actions/User/UpdateUserAction.php`
-   [ ] `app/Actions/User/DeleteUserAction.php`
-   [ ] `app/Services/UserService.php`
-   [ ] `app/Models/User.php`
-   [ ] `app/Exceptions/DuplicateEmailException.php`（必要な場合）

### 1.2 テスト側

-   [ ] `tests/Feature/UserControllerTest.php`
-   [ ]
    `tests/Unit/Actions/User/CreateUserActionTest.php`（Actionの責務・分岐を検証する場合）
-   [ ]
    `tests/Unit/Actions/User/FindUserActionTest.php`（Actionの責務・分岐を検証する場合）
-   [ ]
    `tests/Unit/Actions/User/UpdateUserActionTest.php`（Actionの責務・分岐を検証する場合）
-   [ ]
    `tests/Unit/Actions/User/DeleteUserActionTest.php`（Actionの責務・分岐を検証する場合）
-   [ ]
    `tests/Feature/Services/UserServiceTest.php`（DBを利用する場合はFeature/Integration相当として扱う）
-   [ ]
    `tests/Unit/Services/UserServiceTest.php`（DBに依存しないロジックを検証する場合）

### 1.3 DB・ルート

-   [ ] `database/migrations/xxxx_xx_xx_create_users_table.php`
-   [ ] `database/factories/UserFactory.php`
-   [ ] `routes/api.php`（既存ファイルにルートを追加）

### 1.4 条件に応じて追加するファイル

-   [ ] 認証・認可用Middleware
-   [ ] Policy
-   [ ] 追加の独自例外クラス
-   [ ] 関連テーブルのMigration
-   [ ] 関連Model・Factory
-   [ ] API共通エラーレスポンス用クラス

> ファイルは必要になった段階で作成する。単純な処理のために、形式的にクラスやテストを増やさない。

------------------------------------------------------------------------

## 2. 実装手順

### ① API仕様・テストケースの設計

-   [ ] APIのエンドポイントを決める。
-   [ ] HTTPメソッドを決める。
-   [ ] リクエスト項目を決める。
-   [ ] レスポンス項目を決める。
-   [ ] HTTPステータスコードを決める。
-   [ ] 正常系のテストケースを洗い出す。
-   [ ] 異常系のテストケースを洗い出す。
-   [ ] DBの変更内容を整理する。

### ② Feature Testの作成

-   [ ] `UserControllerTest` を作成する。
-   [ ] 正常系のテストを作成する。
-   [ ] 異常系のテストを作成する。
-   [ ] リクエスト内容を定義する。
-   [ ] レスポンス内容を検証する。
-   [ ] HTTPステータスコードを検証する。
-   [ ] DBの変更結果を検証する。
-   [ ] テストを実行し、失敗することを確認する（Red）。

### ③ APIの骨組みを実装

-   [ ] `routes/api.php` にルートを追加する。
-   [ ] `UserController` を作成する。
-   [ ] Controllerにアクションメソッドを追加する。
-   [ ] 対応するActionクラスを作成する。
-   [ ] ControllerからActionを呼び出す。
-   [ ] Actionから仮のレスポンスを返す。
-   [ ] Feature Testを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ④ FormRequestによるバリデーション

-   [ ] `StoreUserRequest` を作成する。
-   [ ] 登録リクエストのバリデーションルールを定義する。
-   [ ] `UpdateUserRequest` を作成する。
-   [ ] 更新リクエストのバリデーションルールを定義する。
-   [ ] 必須・任意・型・文字数などの条件を設定する。
-   [ ] バリデーションエラーのFeature Testを作成する。
-   [ ] ControllerにFormRequestを適用する。
-   [ ] `$request->validated()` で検証済みデータを取得する。
-   [ ] Feature Testを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑤ Actionの実装

-   [ ] `CreateUserAction` を作成する。
-   [ ] `FindUserAction` を作成する。
-   [ ] `UpdateUserAction` を作成する。
-   [ ] `DeleteUserAction` を作成する。
-   [ ] 必要なActionのテストを作成する。
-   [ ] Actionが対応するServiceを呼び出すことを検証する。
-   [ ] Actionにユースケースの処理・制御を実装する。
-   [ ] ControllerからActionを呼び出す構成にする。
-   [ ] Actionのテストを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑥ Serviceの実装

-   [ ] `UserService` を作成する。
-   [ ] CRUDのメソッドを定義する。
    -   [ ] `create()`
    -   [ ] `find()`
    -   [ ] `update()`
    -   [ ] `delete()`
-   [ ] Serviceのテストを作成する。
-   [ ] 正常系のテストを作成する。
-   [ ] 異常系のテストを作成する。
-   [ ] 必要に応じて独自例外を定義する。
-   [ ] Serviceにユーザー領域の業務ロジックを実装する。
-   [ ] ServiceからEloquent Modelを利用する。
-   [ ] DB操作を伴うテストではDBの状態も検証する。
-   [ ] テストを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑦ DB・Modelの実装

-   [ ] Migrationを作成する。
-   [ ] テーブル名・カラム名を定義する。
-   [ ] カラムの型・制約を定義する。
-   [ ] インデックス・ユニーク制約を定義する。
-   [ ] Migrationを実行する。
-   [ ] `User` Modelを作成する。
-   [ ] `$fillable` または `$guarded` を設定する。
-   [ ] 必要に応じてリレーションを定義する。
-   [ ] `UserFactory` を作成する。
-   [ ] Factoryにテストデータを定義する。
-   [ ] ServiceからModelを利用する。
-   [ ] DB操作を伴うテストを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑧ Resourceによるレスポンス整形

-   [ ] `UserResource` を作成する。
-   [ ] レスポンスに含める項目を定義する。
-   [ ] 項目の型・階層を整理する。
-   [ ] 必要に応じて関連データを含める。
-   [ ] ControllerからResourceを返す。
-   [ ] レスポンスのFeature Testを作成する。
-   [ ] 不要な項目や機密情報が含まれないことを検証する。
-   [ ] HTTPステータスコードを検証する。
-   [ ] Feature Testを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑨ 例外・認証・認可の実装

-   [ ] 業務上の例外を整理する。
-   [ ] 必要に応じて独自例外を作成する。
-   [ ] 例外とHTTPステータスコードの対応を定義する。
-   [ ] エラーレスポンスの形式を統一する。
-   [ ] 認証が必要なAPIにMiddlewareを設定する。
-   [ ] 認可が必要なAPIに認可処理を設定する。
-   [ ] 未認証・権限不足のFeature Testを作成する。
-   [ ] 異常時にDBが変更されないことを検証する。
-   [ ] テストを実行する。
-   [ ] テストが成功することを確認する（Green）。

### ⑩ リファクタリング・品質確認

-   [ ] Controllerに業務ロジックが残っていないか確認する。
-   [ ] Actionがユースケースの実行・制御に専念しているか確認する。
-   [ ] Serviceにドメインの業務ロジックが集約されているか確認する。
-   [ ] Modelの責務が適切か確認する。
-   [ ] 不要なクラス・メソッドがないか確認する。
-   [ ] 重複コードを整理する。
-   [ ] N+1問題がないか確認する。
-   [ ] トランザクションの必要性を確認する。
-   [ ] 全テストを実行する。
-   [ ] PHPStan / Larastanを実行する。
-   [ ] Laravel Pintを実行する。
-   [ ] 最終的な差分を確認する。

------------------------------------------------------------------------

## 3. クラス間の依存関係と責務

``` text
UserController
      ↓
UserAction
      ↓
UserService
      ↓
User (Eloquent Model)
      ↓
Database
```

  クラス        責務                           作成単位
  ------------- ------------------------------ -------------------
  Controller    HTTPリクエスト・レスポンス     リソース・API単位
  Action        ユースケースの実行・制御       ユースケース単位
  Service       ドメインの業務ロジック・CRUD   ドメイン単位
  Model         データ管理・リレーション       テーブル単位
  Resource      レスポンス形式への変換         リソース単位
  FormRequest   リクエストのバリデーション     リクエスト単位

### 実装ルール

-   ControllerからServiceを直接呼び出さない。
-   Actionはユースケース単位で作成する。
-   Serviceはドメイン単位で作成する。
-   同一ドメインのCRUDは原則として同じServiceに集約する。
-   ServiceからEloquent Modelを直接利用する。
-   Repositoryは設けない。
-   Controllerには業務ロジックを実装しない。
-   バリデーションはFormRequestに集約する。
-   レスポンスの整形はResourceに集約する。

------------------------------------------------------------------------

## 4. テスト設計上の補足

-   **Feature
    Test**：APIの入出力、HTTPステータス、DBへの反映を検証する。
-   **Action Test**：Actionのユースケース制御や分岐を検証する。
-   **Service Test**：Serviceの業務ロジックを検証する。
-   DBを利用するService Testは、厳密にはUnit TestではなくIntegration
    Testとして扱う。
-   Actionが単純なService呼び出しだけなら、Action専用テストを省略し、Feature
    TestとService Testで検証してもよい。
-   テストはクラス数に合わせて機械的に増やすのではなく、責務とリスクに応じて設計する。

------------------------------------------------------------------------

## 5. 最終セルフレビュー

-   [ ] Controller → Action → Service → Model の依存関係になっている。
-   [ ] Serviceをドメイン単位で作成している。
-   [ ] 同一ドメインのCRUDをServiceに集約している。
-   [ ] Actionをユースケース単位で作成している。
-   [ ] Repositoryを使用していない。
-   [ ] Feature Testを実装の初期段階で作成している。
-   [ ] Red → Green → Refactorを反復している。
-   [ ] 正常系・異常系をテストしている。
-   [ ] DBを利用するテストをUnit Testと混同していない。
-   [ ]
    FormRequest・Resource・FactoryなどLaravel標準機能を活用している。
-   [ ] 不要なクラスやテストを追加していない。
