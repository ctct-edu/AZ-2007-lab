# ラボ04: GitHub Copilot を使用してコードセクションをリファクタリングする

## 概要

このラボでは、Lab03 で作成したテストを安全網として使いながら、コード全体を大規模にリファクタリングします。foreach ループの LINQ 変換と XML ドキュメンテーションの追加を、**単一の Plan→Agent タスク**で一括実行します。

---

## 開始する前に

### プロジェクトの現状

コードベースには以下の改善点があります:

| 対象 | 現状 | 目標 |
|---|---|---|
| ProductService | foreach ループを使用 | LINQ に変換 |
| OrderService | foreach ループを使用 | LINQ に変換 |
| StockService | foreach ループを使用 | LINQ + null 安全演算子 |
| ProductData | foreach ループを使用 | LINQ に変換 |
| StockData | foreach ループを使用 | LINQ に変換 |
| StockHistoryData | foreach ループを使用 | LINQ に変換 |
| 全 public メソッド | XML ドキュメントなし | XML ドキュメント追加 |

### Lab03 テストを安全網として確認する

**このラボを始める前に、Lab03 で作成したテストが全て成功していることを確認してください。**

1. Test Explorer を開く（左サイドバーのビーカーアイコン）
2. 全テストを実行する
3. **全て緑（成功）であることを確認してから次に進む**

> **なぜテストを確認するのか**: 大規模なリファクタリングでは意図しない動作変更が起こりうるため、「変更前の状態が正しく動作する」ことを先に確認しておきます。

---

## 演習 8: LINQ と XML ドキュメントの概念

### LINQ（統合言語クエリ）とは

コレクションに対する操作を、ループではなく**「何を取得したいか」を直接記述する**スタイルで書けます。

| LINQ メソッド | 用途 | foreach での対応 |
|---|---|---|
| `FirstOrDefault` | 最初の要素を取得（なければ null） | ループで条件一致の先頭を返す |
| `Where` | 条件に一致する要素をフィルタリング | ループで条件一致を result に追加 |
| `Any` | 条件に一致する要素が存在するか確認 | ループで found フラグを立てる |
| `FindIndex` | 条件に一致する要素のインデックスを取得 | ループで i をインクリメント |
| `RemoveAll` | 条件に一致する要素を全て削除 | ループで DeleteAt を呼ぶ |
| `Select` | 要素を変換して新しいリストを作る | ループで new List に Add |
| `ToList` | IEnumerable を List に変換 | - |

**変換例**:
```csharp
// Before（foreach）
List<Product> result = new List<Product>();
foreach (var product in products)
{
    if (product.ProductId == productId)
        return product;
}
return null;

// After（LINQ）
return products.FirstOrDefault(p => p.ProductId == productId);
```

### XML ドキュメンテーションコメントとは

`///` で始まる特殊なコメントで、IntelliSense でメソッドにカーソルを合わせると表示されます。

```csharp
/// <summary>指定された商品IDに一致する商品を取得します</summary>
/// <param name="productId">検索する商品のID</param>
/// <returns>一致する商品。見つからない場合は null</returns>
/// <example>
/// <code>
/// var product = productData.GetProductById("P001");
/// </code>
/// </example>
public Product GetProductById(string productId)
```

**主要タグ**: `<summary>` `<param>` `<returns>` `<remarks>` `<example>`

---

## 演習 8-10 → 統合タスク: Data 層・Service 層の一括リファクタリング

演習 8（ProductService の LINQ 変換）・演習 9（Data 層一括リファクタリング）・演習 10（OrderService/StockService 包括改善）を、**単一の Plan→Agent タスク**として実施します。

---

### タスク 1: 【Planモード】で改善計画を立てる <!-- TODO:実機確認 -->

#### ステップ 1: Planモードに切り替える

1. チャット欄のモードを **Plan** に変更する <!-- TODO:実機確認 -->
2. 新しい会話を開始する

#### ステップ 2: 要件プロンプトを入力する

以下のプロンプトをそのまま入力して送信する:

```plaintext
Data 層・Service 層全体の未変換ループを LINQ に変換し、全 public メソッドに XML ドキュメンテーションコメントを追加してください。

対象ファイル:
- src/InventoryManagement.Core/Services/ProductService.cs
- src/InventoryManagement.Core/Services/StockService.cs
- src/InventoryManagement.Core/Services/OrderService.cs
- src/InventoryManagement.Data/ProductData.cs
- src/InventoryManagement.Data/StockData.cs
- src/InventoryManagement.Data/StockHistoryData.cs

品質・信頼性・パフォーマンス・セキュリティの観点から改善点があれば併せて提案してください。

完了条件:
- 動作が変わらないこと（既存の単体テストが全て通ること）
- 全テストを実行して成功することを確認すること
- ビルドが成功すること
```

#### ステップ 3: 計画をレビューして修正指示を出す

1. 生成された計画を読む
2. 少なくとも1つの修正指示を出す
   - 例: 「StockService の GetStockQuantity には null 条件演算子（?.）と null 合体演算子（??）も使用してください」
   - 例: 「XML ドキュメントは日本語で記述してください」

### 完了条件チェックリスト（タスク1）

- [ ] Planモードで計画を生成した
- [ ] 対象ファイルが全て計画に含まれていることを確認した
- [ ] 少なくとも1つの修正指示を出した

---

### タスク 2: 【Agentモード】で一括実装

#### ステップ 1: Agentモードに切り替えて実行する <!-- TODO:実機確認 -->

1. チャット欄のモードを **Agent** に変更する
2. タスク1で確認した要件をそのまま（または若干調整して）Agentモードに送信する

#### ステップ 2: 実行中の観察

| 観察項目 | メモ欄 |
|---|---|
| 最初に変更されたファイルは何か | |
| どの順でファイルが処理されたか | |
| ターミナルコマンドの承認を求めてきたか | |
| テスト実行の結果はどうだったか | |

#### ステップ 3: 差分レビュー

エージェントが完了したら、以下の観点で差分を確認する:

**レビューチェックリスト**:
- [ ] 動作が変更されていないか（ロジックは同じか）
- [ ] 過度な変更が含まれていないか（不要なリファクタリングをしていないか）
- [ ] 命名・規約に沿っているか（日本語コメント・既存の命名パターン）
- [ ] Lab01 で作成した `.github/copilot-instructions.md` に沿っているか

> 以下は生成結果の一例です。意図や実装が異なっていても、完了条件を満たしていれば問題ありません。

```csharp
// 生成例: ProductService.GetProductById
public Product GetProductById(string productId)
{
    List<Product> products = _productData.GetAll();
    return products.FirstOrDefault(p => p.ProductId == productId);
}

// 生成例: StockService.GetStockQuantity（null 安全演算子使用）
public int GetStockQuantity(string productId)
{
    if (string.IsNullOrWhiteSpace(productId))
        return 0;
    List<Stock> stocks = _stockData.GetAllStocks();
    var stock = stocks.FirstOrDefault(s => s.ProductId == productId);
    return stock?.Quantity ?? 0;
}

// 生成例: OrderService.GenerateOrderRecommendations（Select 使用）
public List<ReorderRecommendation> GenerateOrderRecommendations()
{
    List<Product> lowStockProducts = GetLowStockProducts();
    return lowStockProducts.Select(product =>
    {
        int currentStock = _stockService.GetStockQuantity(product.ProductId);
        return CreateRecommendation(product, currentStock);
    }).ToList();
}
```

> **定量的な効果について**: コード行数の削減や開発時間の短縮は、対象コードや実装によって大きく異なります。今回のリファクタリングでも行数削減が見込まれますが、具体的な数字は実装結果で確認してください。

### 完了条件チェックリスト（タスク2）

- [ ] 全対象ファイルの foreach ループが LINQ に変換された
- [ ] 全 public メソッドに XML ドキュメンテーションコメントが追加された
- [ ] ビルドが成功した
- [ ] IntelliSense でメソッドにカーソルを合わせるとドキュメントが表示される

---

### タスク 3: テストで動作変更なしを確認する

#### ステップ 1: 全テストを実行する

1. Test Explorer を開く（左サイドバーのビーカーアイコン）
2. **「UnitTests」** ノードを右クリック → **「テストの実行」** を選択
3. **全テストが成功（緑）** であることを確認する

#### ステップ 2: アプリケーションの統合確認

1. **InventoryManagement.Console** を右クリック → **「デバッグ」** → **「新しいインスタンスを開始」**
2. 以下の機能が正常に動作することを確認する:
   - 商品一覧表示（「1」）
   - 在庫確認（「3」、商品ID: P001）
   - 発注推奨リスト表示（「6」）
   - 低在庫アラート表示（「7」、Lab02 で実装済み）
3. **「0」** でアプリケーションを終了する

> **テストが安全網として機能した**: Lab03 で作成したテストのおかげで、大規模な変更後も「動作が壊れていない」ことを即座に確認できます。これがテストを先に書く理由のひとつです。

### 完了条件チェックリスト（タスク3）

- [ ] 全単体テストが成功（緑）
- [ ] アプリケーションの基本機能が正常に動作する
- [ ] 「Lab03 のテストが安全網として機能した」ことを体感した

---

## 演習 8-10 まとめ

### このラボで達成したこと

1. **LINQ 変換**: Data 層・Service 層の全 foreach ループを LINQ に変換した
2. **XML ドキュメント追加**: 全 public メソッドにドキュメントコメントを追加した
3. **安全網の実感**: Lab03 で作成したテストが大規模変更後の品質保証として機能した
4. **引き渡し工程の実践**: 差分レビュー観点リストに沿ってエージェントの変更を検証した

### ラボ全体のストーリー完結

> Lab00 でモードの違いを理解した  
> → Lab01 で AI に文脈を与えた（copilot-instructions.md）  
> → Lab02 で計画駆動の機能開発を体験した  
> → Lab03 でテストという安全網を作った  
> → **Lab04 で安全網を頼りに大規模な改善を任せた** ← 今ここ

---

## トラブルシューティング

### Q: LINQ 変換後に単体テストが失敗する
**A**: 失敗しているテストのエラーメッセージをエージェントに貼り付けて「修正してください」と依頼してください。テストが安全網として機能しています。

### Q: using System.Linq がないとビルドエラーになる
**A**: ファイル上部に `using System.Linq;` を追加してください。.NET 6 以降では多くの場合は不要ですが、対象プロジェクトの設定によっては必要な場合があります。

### Q: XML ドキュメントが IntelliSense に表示されない
**A**: ファイルが保存されているか確認し、ソリューションをビルドしてから再確認してください。それでも表示されない場合は VS Code を再起動してください。

### Q: 一部のメソッドが LINQ に変換されていない
**A**: そのメソッドだけを選択して Agent モードで「このメソッドを LINQ でリファクタリングしてください」と依頼してください。

---

## 応用課題（オプション）

Copilot のコードレビュー機能を使って、今回加えた変更をレビューしてください。

**手順のヒント** <!-- TODO:実機確認 -->:
1. 変更したファイルをコンテキストに追加する
2. 「このリファクタリングに問題点や改善点はありますか？品質・保守性・パフォーマンスの観点で確認してください」と Ask モードで確認する
3. 指摘されたフィードバックに対応する修正指示を出す
