# ラボ02: GitHub Copilot を使用して新機能を開発する

## 概要

このラボでは、GitHub Copilotを活用して図書館アプリケーションに新しい「書籍の在庫確認」機能を追加します。実際の開発現場で使われる手法（ブランチ作成、機能開発、プルリクエスト）を体験しながら、AI支援開発の効果を実感していただきます。

---

## 演習5: 新しい「書籍の在庫確認」機能を開発する

### 演習の目標
司書が書籍の貸出状況を素早く確認できる機能を追加し、図書館業務の効率化を図ります。

### 実装する機能
- **新機能名**: SearchBooks（書籍検索）
- **機能概要**: 書籍タイトルで検索し、貸出可能か貸出中かを表示
- **表示メッセージ例**:
  - 「Book Oneは貸出可能です」
  - 「Book Oneは別の利用者に貸し出されています。返却期限は2024-01-15です」

---

## タスク1: 開発用ブランチの作成

### ステップ1: 現在の状態を確認

1. **Visual Studio Codeで AccelerateDevGitHubCopilot プロジェクトが開かれていることを確認**
   - プロジェクトが開かれていない場合は、Lab01で作成したプロジェクトフォルダーを開いてください

2. **ソース管理ビューを開く**
   - 左サイドバーの**ソース管理アイコン**（Y字型のアイコン）をクリック
   - または **Ctrl + Shift + G** キーを押下

3. **リモートリポジトリとの同期確認**
   - 画面下部のステータスバーに「同期」「プル」「プッシュ」ボタンが表示されている場合
   - **「同期」** ボタンをクリックして最新状態にしてください
   - 認証ダイアログが表示された場合は、GitHubアカウントで承認してください

**重要**: すべての変更がリモートリポジトリと同期されていることを確認してください

### ステップ2: 新しいブランチを作成

1. **現在のブランチを確認**
   - 画面左下のステータスバーに **「main」** と表示されていることを確認
   - この表示がブランチ名を示しています
   
2. **新しいブランチを作成**
   - 左下の **「main」** 表示をクリック
   - **注意**: クリックすると上部にコマンドパレットが開きます
   - 表示されたメニューで **「+ 新しいブランチの作成」** を選択
   - ブランチ名として **「book-availability」** と入力
   - **Enter** キーを押下

3. **ブランチをリモートに公開**
   - 新しいブランチ作成後、ソース管理ビュー内のリポジトリが **「book-availability」** に切り替わり、右側に **「ブランチの発行」** ボタンが表示されます。
   - **「ブランチの発行」** をクリック
   - GitHub認証が求められた場合は **「許可」** をクリックして承認してください

**完了確認**: 左下の表示が **「book-availability」** に変わったことを確認してください

---

## タスク2: 新しいSearchBooksアクションの実装

### 作業対象ファイル
以下のファイルを順番に更新していきます：
1. `CommonActions.cs` - 新しいアクション定義を追加
2. `ConsoleApp.cs` - ユーザーインターフェース部分を更新

### ステップ1: CommonActions列挙型を更新

1. **ソリューションエクスプローラービューを開く**
   - 左サイドバーの**フォルダーアイコン**をクリック
   - **注意**: 「エクスプローラー」ではなく「ソリューション エクスプローラー」を使用してください

2. **CommonActions.csファイルを開く**
   - ソリューションエクスプローラーで以下の階層を辿ります：
     ```
     AccelerateDevGitHubCopilot
     └── src
         └── Library.Console
             └── CommonActions.cs
     ```
   - **CommonActions.cs** ファイルをダブルクリックして開きます

3. **CommonActions列挙型全体を選択**
   ```csharp
   [Flags]
   public enum CommonActions
   {
       Repeat = 0,
       Select = 1,
       Quit = 2,
       SearchPatrons = 4,
       RenewPatronMembership = 8,
       ReturnLoanedBook = 16,
       ExtendLoanedBook = 32
   }
   ```
   **操作手順**: 列挙型の開始行から終了行まで全体をドラッグして選択してください

4. **GitHub Copilotで新しいアクションを追加**
   - 選択状態で **Ctrl + I** キーを押してインラインチャットを開く
   - **重要**: インラインチャットが開かない場合は、GitHub Copilot拡張機能が有効になっているか確認してください
   - 以下のプロンプトを日本語で入力：
   ```plaintext
   選択範囲を更新して、新しい SearchBooks アクションを追加してください。値は64を使用してください。
   ```
   - **Enter** キーを押下

5. **生成されたコードを確認して承諾**
   - 以下のような結果が表示されることを確認：
   ```csharp
   [Flags]
   public enum CommonActions
   {
       Repeat = 0,
       Select = 1,
       Quit = 2,
       SearchPatrons = 4,
       RenewPatronMembership = 8,
       ReturnLoanedBook = 16,
       ExtendLoanedBook = 32,
       SearchBooks = 64
   }
   ```
   - 内容が正しければ **「承諾」** または **「同意する」** をクリック

### ステップ2: メニューオプション表示機能を更新

1. **ConsoleApp.csファイルを開く**
   - ソリューションエクスプローラーで以下の階層を辿ります：
     ```
     AccelerateDevGitHubCopilot
     └── src
         └── Library.Console
             └── ConsoleApp.cs
     ```

2. **WriteInputOptionsメソッドを検索**
   - ファイルが開いたら **Ctrl + F** で検索ダイアログを開く
   - **「WriteInputOptions」** と入力して検索
   - **Enter** で該当箇所にジャンプ

3. **WriteInputOptionsメソッド全体を選択**
   ```csharp
   static void WriteInputOptions(CommonActions options)
   {
       Console.WriteLine("Input Options:");
       // メソッドの内容全体を選択
       // 最後の } まで含めて選択
   }
   ```

4. **GitHub Copilotで機能を追加**
   - 選択状態で **Ctrl + I** でインラインチャットを開く
   - 以下のプロンプトを入力：
   ```plaintext
   選択範囲を更新して、CommonActions.SearchBooks アクションのオプションを追加してください。文字「b」と「書籍の在庫確認」メッセージを使用してください。
   ```

5. **生成されたコードを確認**
   - 以下のようなif文が追加されることを確認：
   ```csharp
   if (options.HasFlag(CommonActions.SearchBooks))
   {
       Console.WriteLine(" - \"b\" で書籍の在庫確認");
   }
   ```
   - 適切な位置（他のオプション表示と同じ場所）に追加されていることを確認

### ステップ3: 入力処理機能を更新

1. **ReadInputOptionsメソッドを検索**
   - 同じファイル内で **Ctrl + F** で **「ReadInputOptions」** を検索
   - **注意**: このメソッドはやや長いメソッドです

2. **switch文のある部分を特定**
   ```csharp
   action = userInput switch
   {
       "q" when options.HasFlag(CommonActions.Quit) => CommonActions.Quit,
       "s" when options.HasFlag(CommonActions.SearchPatrons) => CommonActions.SearchPatrons,
       // この部分にさらにケースがあります
   };
   ```

3. **switch文全体を選択**
   - `action = userInput switch` の行から `};` までを選択

4. **GitHub Copilotで入力処理を追加**
   - 選択状態で **Ctrl + I** でインラインチャットを開く
   - 以下のプロンプトを入力：
   ```plaintext
   選択範囲を更新して、CommonActions.SearchBooks アクションのケースを追加してください。文字「b」を入力キーとして使用してください。
   ```

5. **生成されたコードを確認**
   - switch文に以下のようなケースが追加されることを確認：
   ```csharp
   "b" when options.HasFlag(CommonActions.SearchBooks) => CommonActions.SearchBooks,
   ```

### ステップ4: PatronDetailsメソッドを更新

1. **PatronDetailsメソッドを検索**
   - **Ctrl + F** で **「PatronDetails」** を検索
   - **注意**: `async Task<ConsoleState> PatronDetails()` のメソッドを探してください

2. **メソッドの主要部分を確認**
   - このメソッドは利用者詳細画面を表示し、ユーザーの選択を処理します
   - `ReadInputOptions` を呼び出している部分を特定します

3. **options変数の初期化部分を選択**
   ```csharp
   CommonActions options = CommonActions.SearchPatrons | CommonActions.Quit | CommonActions.Select | CommonActions.RenewPatronMembership;
   ```

4. **GitHub Copilotでオプションを追加**
   - 選択状態で **Ctrl + I** でインラインチャットを開く
   - 以下のプロンプトを入力：
   ```plaintext
   選択範囲を更新して、CommonActions.SearchBooksをoptionsに追加してください。
   ```

5. **SearchBooks処理を追加**
   - PatronDetailsメソッド内のelse if文の連続している部分を見つけます
   - 最後のelse if文の後に新しい処理を追加します
   - 以下のコードを手動で追加：
   ```csharp
   else if (action == CommonActions.SearchBooks)
   {
       return await SearchBooks();
   }
   ```

---

## タスク3: SearchBooksメソッドの実装

### ステップ1: メソッドの設計を理解

SearchBooksメソッドの処理フロー：
1. ユーザーから書籍タイトルを入力
2. Books.jsonから一致する書籍を検索
3. Loans.jsonで貸出状況を確認
4. 結果メッセージを表示
5. PatronDetailsに戻る

### ステップ2: 基本的なSearchBooksメソッドを作成

1. **ConsoleApp.csの末尾に移動**
   - ファイルの最後の `}` の直前に新しいメソッドを追加します

2. **基本的なメソッドの骨組みを作成**
   ```csharp
   async Task<ConsoleState> SearchBooks()
   {
       // この部分をGitHub Copilotで実装します
       return ConsoleState.PatronDetails;
   }
   ```

### ステップ3: GitHub Copilotを使用した包括的な実装

1. **チャットビューを開く**
   - **Ctrl + Alt + I** でチャットビューを開く

2. **必要なファイルをチャットコンテキストに追加**
   以下のファイルをドラッグ&ドロップでチャットビューに追加：
   - `ConsoleApp.cs`（現在編集中のファイル）
   - `JsonData.cs`（Library.Infrastructure/Data/ フォルダー内）
   - `Books.json`（Library.Console/Json/ フォルダー内）
   - `Loans.json`（Library.Console/Json/ フォルダー内）

3. **包括的な実装指示を送信**
   ```plaintext
   @workspace SearchBooksメソッドを実装してください。以下の仕様で作成してください：

   1. ユーザーに「検索する書籍タイトルを入力してください: 」とプロンプトを表示
   2. 書籍タイトルの入力を受け取る（空白やnullの場合は再入力を求める）
   3. JsonDataクラスを使用してBooks.jsonから入力されたタイトルと一致する書籍を検索
   4. 書籍が見つからない場合は「指定された書籍が見つかりません」と表示
   5. 書籍が見つかった場合、Loans.jsonを確認して貸出状況をチェック
   6. 貸出可能な場合：「{書籍タイトル}は貸出可能です」と表示
   7. 貸出中の場合：「{書籍タイトル}は別の利用者に貸し出されています。返却期限は{返却期限}です」と表示
   8. 最後にConsoleState.PatronDetailsを返す

   JsonDataクラスにSearchBookByTitleメソッドが必要な場合は、その実装も提案してください。
   ```

### ステップ4: 生成されたコードの実装

GitHub Copilotからの提案に基づいて、以下の更新を行ってください：

1. **JsonData.csの更新**
   - SearchBookByTitleメソッドが提案された場合は追加
   ```csharp
   public Book? SearchBookByTitle(string title)
   {
       return Books?.FirstOrDefault(b => b.Title.Equals(title, StringComparison.OrdinalIgnoreCase));
   }
   ```

2. **ConsoleApp.csの更新**
   - JsonDataフィールドの追加（まだ追加されていない場合）
   - コンストラクターの更新
   - SearchBooksメソッドの完全な実装

3. **必要なusing文の追加**
   ```csharp
    using Library.Infrastructure.Data;
    using Microsoft.Extensions.Configuration;
   ```
 - **注意**: 必要に応じてusing文を追加してください
### ステップ5: 依存関係の確認と修正

1. **Program.csの確認**
   - `src/Library.Console/Program.cs` を開く
   - JsonDataの依存関係注入が含まれていることを確認：
   ```csharp
   services.AddSingleton<JsonData>();
   ```

2. **ビルドエラーの確認**
   - **Ctrl + Shift + P** → **「Tasks: Run Build Task」** を実行
   - または ソリューションエクスプローラーで **GuidedProjectApp** を右クリック → **「ビルド」**

3. **エラーの修正**
   - using文の不足やコンストラクターパラメーターの不一致があれば修正
   - 警告は無視して構いませんが、エラーは必ず解決してください

---

## タスク4: 機能テストの実施

### ステップ1: アプリケーションの準備

1. **ソリューションをクリーン**
   - ソリューションエクスプローラーで **GuidedProjectApp** を右クリック
   - **「クリーン」** を選択
   - ビルドキャッシュをクリアして確実に最新コードで実行

2. **ソリューションをビルド**
   - 再度 **GuidedProjectApp** を右クリック
   - **「ビルド」** を選択
   - エラーがないことを確認

### ステップ2: アプリケーションの実行

1. **デバッグ実行を開始**
   - **Library.Console** プロジェクトを右クリック
   - **「デバッグ」** → **「新しいインスタンスを開始」** を選択

2. **実行環境の確認**
   - コンソールウィンドウが開くことを確認
   - アプリケーションが正常に起動することを確認

### ステップ3: 新機能のテスト

1. **基本操作フローの実行**
   - パトロン名の入力プロンプト: **「One」** と入力（Oは大文字）
   - **Enter** キーを押下
   - 利用者選択: **「2」** と入力
   - **Enter** キーを押下

2. **書籍在庫確認機能のテスト**
   - Input Optionsで利用可能なオプションを確認
   - **「b」** と入力（書籍検索オプション）
   - **Enter** キーを押下

3. **貸出中の書籍をテスト**
   - 「検索する書籍タイトルを入力してください」プロンプトで **「Book One」** と入力
   - **Enter** キーを押下
   - 期待される結果：「Book Oneは別の利用者に貸し出されています。返却期限は[日付]です」

4. **利用可能な書籍をテスト**
   - 再度 **「b」** を入力
   - **Enter** キーを押下
   - 書籍タイトル: **「Book Nine」** と入力
   - **Enter** キーを押下
   - 期待される結果：「Book Nineは貸出可能です」

5. **存在しない書籍をテスト**
   - 再度 **「b」** を入力
   - 書籍タイトル: **「Non-Existent Book」** と入力
   - 期待される結果：「指定された書籍が見つかりません」

6. **アプリケーション終了**
   - **「q」** を入力してアプリケーションを終了

**重要な注意点**:
- JSONデータファイルの場所により、更新内容の反映場所が異なります
- デバッガー実行: `Library.Console\bin\Debug\net8.0\Json\`
- dotnet run実行: `Library.Console\Json\`

---

## 演習6: 変更をメインブランチにマージする

### タスク1: 変更のコミットとプッシュ

1. **ソース管理ビューを開く**
   - 左サイドバーの**ソース管理アイコン**をクリック

2. **変更ファイルの確認**
   - 「変更」セクションに以下のファイルが表示されることを確認：
     - `CommonActions.cs`
     - `ConsoleApp.cs`
     - その他更新されたファイル

3. **AIコミットメッセージの生成**
   - コミットメッセージボックス右側の**スパークルアイコン**（AI生成アイコン）をクリック
   - GitHub Copilotが以下のようなコミットメッセージを生成します：
     ```
     書籍在庫確認機能を追加：SearchBooksアクションとメソッドを実装
     ```

4. **変更をステージングしてコミット**
   - **「変更をステージ」** ボタンをクリック（すべての変更をステージング）
   - または個別ファイルの **「+」** アイコンをクリック
   - **「コミット」** ボタンをクリック

5. **リモートリポジトリに同期**
   - **「同期」** または **「プッシュ」** ボタンをクリック
   - GitHub認証が求められた場合は承認してください

### タスク2: プルリクエストの作成

1. **GitHubリポジトリを開く**
   - ブラウザーでGitHubアカウントにアクセス
   - AccelerateDevGitHubCopilotリポジトリを開く
   - 必要に応じてページを更新してください

2. **プルリクエストの通知を確認**
   - リポジトリページ上部に黄色の通知バーが表示される場合があります
   - **「Compare & pull request」** ボタンが表示されていればクリック

3. **手動でプルリクエストを作成（通知がない場合）**
   - **「Pull requests」** タブをクリック
   - **「New pull request」** ボタンをクリック

4. **ブランチの設定を確認**
   - **base**: `main` ブランチ（マージ先）
   - **compare**: `book-availability` ブランチ（マージ元）
   - 正しく設定されていることを確認

5. **プルリクエストの詳細入力**
   - タイトル: **「書籍在庫確認機能の追加」**
   - 説明欄に以下を入力：
     ```
     ## 変更内容
     - SearchBooksアクションをCommonActionsに追加
     - ConsoleAppにSearchBooksメソッドを実装
     - ユーザーが書籍タイトルで在庫状況を確認できる機能

     ## テスト結果
     - 貸出中書籍の確認：正常動作
     - 利用可能書籍の確認：正常動作
     - 存在しない書籍の処理：正常動作
     ```

6. **プルリクエストの作成**
   - **「Create pull request」** ボタンをクリック

### タスク3: マージの実行

1. **チェックの完了を待機**
   - GitHubが自動的に実行するチェック（あれば）が完了するまで待機
   - すべてのチェックが緑色（成功）になることを確認

2. **プルリクエストの確認**
   - 変更されたファイルの内容を確認
   - **「Files changed」** タブで差分を確認

3. **プルリクエストのマージ**
   - **「Conversation」** タブで **「Merge pull request」** ボタンをクリック
   - マージ方法は **「Create a merge commit」** を選択（デフォルト）
   - **「Confirm merge」** ボタンをクリック

4. **ブランチの削除（推奨）**
   - マージ完了後、**「Delete branch」** ボタンが表示されます
   - **「Delete branch」** をクリックしてfeatureブランチを削除

### タスク4: ローカルでの更新

1. **Visual Studio Codeに戻る**

2. **mainブランチに切り替え**
   - 左下の **「book-availability」** をクリック
   - **「main」** ブランチを選択

3. **最新の変更を取得**
   - ソース管理ビューで **「プル」** ボタンをクリック
   - リモートの最新変更をローカルに取得

4. **完了確認**
   - mainブランチに新機能が正しくマージされていることを確認
   - 必要に応じてアプリケーションを再度実行してテスト

---

## トラブルシューティング

### Q: ビルドエラーが発生する
**A**: 以下の点を確認してください
- using文の追加：`using Library.Infrastructure.Data;`
- プロジェクト参照の確認
- JsonDataの依存関係注入がProgram.csに含まれているか

### Q: GitHub認証エラーが発生する
**A**: 以下の手順で解決してください
1. Visual Studio Codeを再起動
2. GitHub Copilot拡張機能を無効化→有効化
3. GitHubアカウントで再認証

### Q: 書籍在庫確認が正しく動作しない
**A**: 以下を確認してください
- JSONファイルの読み込み場所とデータの整合性
- 書籍タイトルの大文字小文字一致（StringComparison.OrdinalIgnoreCaseの使用）
- 貸出データの返却日（ReturnDate）がnullかどうかの判定

### Q: 「SearchBooks」オプションが表示されない
**A**: 以下を確認してください
- CommonActionsにSearchBooksが正しく追加されているか
- WriteInputOptionsメソッドにSearchBooksの表示処理が追加されているか
- PatronDetailsメソッドでoptionsにSearchBooksが含まれているか

### Q: プルリクエストでマージできない
**A**: 以下を確認してください
- ブランチの設定が正しいか（base: main, compare: book-availability）
- マージコンフリクトが発生していないか
- 必要な権限があるか

---

**次のステップ**: パート3では、作成した機能の単体テストを開発します。品質保証の観点から重要な作業となります。