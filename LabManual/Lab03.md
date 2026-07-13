# ラボ03: GitHub Copilot ツールを使用して単体テストを開発する

## 概要

このラボでは、単体テストを「AIが自律的に書いて・実行して・修正する」プロセスで作成します。エージェントがテスト失敗を検知して自己修正するループを観察することが、このラボの主目的です。また、バグ修正演習で「テストが安全網として機能する」体験をします。


---

## 開始する前に

### プロジェクトの現状確認

```
UnitTests/
├── Core/
│   └── ProductServiceTests/
│       ├── GetProductByIdTest.cs    （実装済み）
│       ├── SearchByNameTest.cs      （実装済み）
│       ├── GetByCategoryTest.cs     （実装済み）
│       └── AddProductTest.cs        （空のスケルトン）
├── ProductFactory.cs
├── StockFactory.cs
├── StockHistoryFactory.cs
└── UnitTests.csproj
```

**使用技術**:
- **xUnit**: .NET 用テストフレームワーク
- **NSubstitute**: モックオブジェクト生成ライブラリ

---

## 演習 7: GitHub Copilot を使用して UnitTests プロジェクトを評価および拡張する

### タスク 1: 【Askモード】で既存のテストアプローチを分析する

> **このタスクで使うモード**: Ask（分析・相談に適切。コードは変更しません）

#### ステップ 1: チャットビューの準備

1. **チャットビューを開く** <!-- TODO:実機確認 -->
2. モードが **Ask** になっていることを確認する
3. 既存のチャット履歴をクリアして新しい会話を開始する

#### ステップ 2: テストファイルをコンテキストに追加する <!-- TODO:実機確認 -->

以下のファイルをチャットのコンテキストに追加する:

**テストファイル**:
- `tests/UnitTests/ProductFactory.cs`
- `tests/UnitTests/StockFactory.cs`
- `tests/UnitTests/Core/ProductServiceTests/GetProductByIdTest.cs`

**実装ファイル**:
- `src/InventoryManagement.Core/Services/ProductService.cs`
- `src/InventoryManagement.Data/StockData.cs`

> **注意**: `#codebase` を使うと自動的にプロジェクト全体をコンテキストとして参照できます。<!-- TODO:実機確認 -->

#### ステップ 3: テストアプローチを分析する

以下のプロンプトを入力して送信する:
```plaintext
このワークスペースで実装されている単体テストのアプローチについて説明してください。以下の観点で分析してください：
1. モックの使用方法（NSubstitute の使い方）
2. テストデータの作成パターン（ファクトリークラスの役割）
3. テストケースの構成方法（AAA パターン）
4. 成功ケースと失敗ケースの分け方
```

#### ステップ 4: 拡張方針を確認する

続けて以下を送信する:
```plaintext
Data 層（StockData クラス）のテストを追加する場合、どのようなアプローチが適切ですか？Service 層のテストと比較して、どう違いますか？
```

### 完了条件チェックリスト（タスク1）

- [ ] 既存のテストパターン（ファクトリー・モック・AAA）を説明できる
- [ ] Data 層と Service 層のテストの違いを理解した
- [ ] このタスクで **コードは変更していない**

---

### タスク 2: 【Agentモード】によるテスト生成（自己修正ループの観察）

> **このタスクの主役**: エージェントが「テスト作成→実行→失敗検知→自己修正」を繰り返す自律的なループを観察します。

#### ステップ 1: Agentモードに切り替える <!-- TODO:実機確認 -->

1. チャット欄のモードを **Agent** に変更する
2. 新しい会話を開始する

#### ステップ 2: テスト生成と自己修正を依頼する

1. 以下のプロンプトを入力して送信する:
   ```plaintext
   StockData クラスの GetStockByProductId メソッドの単体テストを作成して実行し、全テストが通るまで修正してください。

   要件:
   - テストファイルの配置: tests/UnitTests/Data/StockDataTests/GetStockByProductIdTest.cs
   - namespace: InventoryManagement.UnitTests.Data.StockDataTests
   - Substitute.ForPartsOf<StockData> を使って部分モックを作成する
   - GetAll() をモック化して StockFactory のデータを返すようにする
   - テストケース: 在庫が見つかる場合・見つからない場合（null を返す場合）
   - すべてのテストが成功するまで修正を繰り返してください
   ```

#### ステップ 3: 自己修正ループを観察する（エージェントが動いている間に記録する）

| 観察項目 | メモ欄 |
|---|---|
| テスト実行コマンドの承認を求めてきたか（`dotnet test` 等） | |
| テストが最初から通ったか、何回修正が必要だったか | |
| 失敗時のエラーメッセージをエージェントが読んで修正したか | |
| 最終的に何件のテストが追加されたか | |

> **ターミナルコマンドの承認判断**: `dotnet test` や `dotnet build` は承認して構いません。
> 内容を確認してから許可してください。

#### ステップ 4: AddProductTest の実装を依頼する

1. 引き続き同じ会話で以下を送信する:
   ```plaintext
   次に、AddProductTest.cs のテストメソッドを実装してください。
   現在は空のスケルトンになっています。

   要件:
   - 他の ProductServiceTests クラスと同じパターン（_mockProductData + _productService）
   - テストケース:
     - 有効な商品を追加して true が返る場合
     - 商品が null の場合は false が返る
     - ProductId が空文字の場合は false が返る
     - UnitPrice が無効（0以下）の場合は false が返る
   - ProductFactory の既存メソッドを使用する
   - 実行して全テストが通ることを確認してください
   ```

> 以下は生成結果の一例です。意図や実装が異なっていても、完了条件を満たしていれば問題ありません。

```csharp
// 生成例
[Fact(DisplayName = "StockData.GetStockByProductId: 商品IDで在庫が見つかる場合、正しい在庫情報を返す")]
public void GetStockByProductId_ReturnsStock_WhenProductExists()
{
    // Arrange
    var expectedStock = StockFactory.CreateTestStock("P001");
    var stocks = new List<Stock> { expectedStock };
    _stockData.GetAll().Returns(stocks);

    // Act
    var actualStock = _stockData.GetStockByProductId("P001");

    // Assert
    Assert.NotNull(actualStock);
    Assert.Equal(expectedStock.ProductId, actualStock.ProductId);
    Assert.Equal(expectedStock.Quantity, actualStock.Quantity);
}
```

### 完了条件チェックリスト（タスク2）

- [ ] `GetStockByProductIdTest.cs` が作成された
- [ ] `AddProductTest.cs` にテストメソッドが実装された
- [ ] Test Explorer でこのラボで追加したすべてのテストが成功（緑）と表示される
- [ ] エージェントの自己修正ループを少なくとも1回観察した

---

### タスク 3: バグ修正演習（Git不使用）

> **このタスクの目的**: テストが安全網として機能することを体験します。バグが混入したコードに対してエージェントが自律的に原因を特定・修正します。

#### ステップ 1: 資材フォルダをコピーして反映する

1. デスクトップにある講師配布の `_lab03_materials` フォルダを、`InventoryManagementApp/` 直下にコピーする
   - 既に同名フォルダがある場合は上書きしてよい
   - このフォルダには「バグ入り実装」と「安全網テスト」が含まれる

2. `_lab03_materials` から使うバグ入り実装を1つ選ぶ:
   - 初級は `_lab03_materials/StockService_buggy_beginner.cs`（バグ1件）
   - 中級は `_lab03_materials/StockService_buggy_intermediate.cs`（バグ2件）

3. 選んだファイルを開いて内容を全選択し、`src/InventoryManagement.Core/Services/StockService.cs` に**すべて貼り付けて保存**する
   - 既存内容は上書きしてよい
   - コピー元とコピー先のファイル名が異なっていても問題ない
   - 保存に失敗する場合は、いったんファイルを閉じて再度開いて貼り付け直す
   - 読み取り専用の場合はファイルのプロパティで読み取り専用を外す

4. `_lab03_materials/tests/UnitTests/Core/StockServiceTests/StockServiceBugSafetyNetTests.cs` を、`tests/UnitTests/Core/StockServiceTests/StockServiceBugSafetyNetTests.cs` にコピーして上書きする

5. **安全網テストを実行して失敗を確認する**
   - Test Explorer を開く（左サイドバーのビーカーアイコン）
   - `StockServiceBugSafetyNetTests` を実行する
   - 初級版は1件失敗、中級版は2件失敗になることを確認する

#### ステップ 2: Agentモードにバグ修正を依頼する

1. Agentモードで新しい会話を開始する
2. 以下のプロンプトを入力して送信する:
   ```plaintext
   StockService.cs にバグが混入しており、単体テストが失敗しています。
   StockServiceBugSafetyNetTests の失敗内容を起点に原因を特定してください。
   テストを実行して失敗の原因を特定し、修正してください。
   すべてのテストが成功するまで修正を繰り返してください。
   ```

#### ステップ 3: デバッグループを観察する

| 観察項目 | メモ欄 |
|---|---|
| エージェントが最初に実行したコマンドは何か | |
| 失敗したテストのエラーメッセージをどう解釈したか | |
| バグの原因をどのように特定したか | |
| 修正に何回かかったか | |

#### ステップ 4: 動作確認

1. Test Explorer で**全テストが成功**していることを確認する
2. アプリケーションを実行して基本動作を確認する

#### このタスクでまずかった場合の復元方法

`StockService.cs` を元に戻したい場合は、Git を使わずに以下の方法で復元してください:

1. **VS Code のローカル履歴（Timeline）**:
   - `StockService.cs` を右クリック → **「タイムライン」** を表示
   - バグ入りファイルをコピーする前の時点を選んで復元

2. **講師配布のバックアップフォルダー**:
   - 講師から案内されたバックアップフォルダーから `StockService.cs` をコピーして上書き

### 完了条件チェックリスト（タスク3）

- [ ] デスクトップの `_lab03_materials` フォルダを `InventoryManagementApp/` 直下にコピーした
- [ ] バグ入りファイルをコピーして、テストが失敗することを確認した
- [ ] `StockServiceBugSafetyNetTests` を実行し、初級1件/中級2件の失敗を確認した
- [ ] エージェントにバグ修正を依頼した
- [ ] エージェントが自律的にバグを特定・修正した
- [ ] Test Explorer で全テストが成功（緑）と表示される
- [ ] アプリケーションが正常に動作する

---

## 演習 7 まとめ

### このラボで達成したこと

1. **Askモードによるテスト分析**: 既存のテストパターンを AI と一緒に読み解いた
2. **Agentモードによるテスト生成**: エージェントが自律的にテストを書いて実行・修正するループを観察した
3. **テストが安全網として機能する体験**: バグ入りコードに対してテストが即座に検知し、エージェントが修正した

### Lab04 への接続

Lab04 では、大規模なリファクタリング（全 Data 層・Service 層の LINQ 変換・XML ドキュメント追加）を一括で Agent に任せます。このラボで作成したテストが「動作が壊れていないことを保証する安全網」として機能します。

---

## トラブルシューティング

### Q: テストが Test Explorer に表示されない
**A**: ビルドが成功しているか確認してください。成功している場合は VS Code を再起動してください。

### Q: NullReferenceException が発生する
**A**: モックの設定を確認してください。`_stockData.GetAll().Returns(stocks)` でテストデータが正しく設定されているか確認します。問題が解決しない場合はエラーメッセージをエージェントに貼り付けて修正を依頼してください。

### Q: Substitute.ForPartsOf が使えない
**A**: NSubstitute が UnitTests プロジェクトに参照されているか確認してください。`UnitTests.csproj` に `<PackageReference Include="NSubstitute"...>` が含まれているか確認します。

### Q: バグ入りファイルをコピーしたが上書きされなかった
**A**: ファイル名が `StockService.cs` であることを確認してください。正しい場所（`src/InventoryManagement.Core/Services/`）にコピーされているか確認します。

### Q: `_lab03_materials` 内のファイルを編集したのにテスト結果が変わらない
**A**: `_lab03_materials` は配布資材フォルダです。実行対象は `src/InventoryManagement.Core/Services/StockService.cs` と `tests/UnitTests/Core/StockServiceTests/StockServiceBugSafetyNetTests.cs` です。資材フォルダ内だけを編集してもテスト結果は変わりません。

---

## 応用課題（オプション）

Core 層（StockService / OrderService）の主要メソッドに対するテストを一括生成してください。

**プロンプトのヒント**:
- カバレッジの観点を明示する（正常系・境界値・エラー系）
- 使用すべきファクトリークラスを指定する
- 完了条件に「全テスト成功」を含める
