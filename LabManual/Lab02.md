# ラボ02: GitHub Copilot を使用して新機能を開発する

## 概要

このラボでは、GitHub Copilotを活用して在庫管理システムに新しい「低在庫アラート機能」を追加します。AI支援開発の効果を実感しながら、実践的な機能開発を体験していただきます。

---

## 演習5: 新しい「低在庫アラート機能」を開発する

### 演習の目標
倉庫担当者が在庫不足の商品を素早く確認できる機能を追加し、発注漏れを防止します。

### 実装する機能
- **新機能名**: CheckLowStock（低在庫確認）
- **機能概要**: 最小在庫数を下回っている商品を検索し、アラートを表示
- **表示メッセージ例**:
  - 「低在庫商品が3件見つかりました」
  - 「P001 | ノートPC | 現在在庫: 3 / 最小在庫: 5 - 発注推奨」

---

## タスク1: Program.csの更新

### 作業対象ファイル
以下のファイルを更新していきます：
1. `Program.cs` - メニューとメイン処理の追加

### ステップ1: Program.csファイルを開く

1. **ソリューションエクスプローラービューを開く**
   - 左サイドバーの**フォルダーアイコン**をクリック
   - **注意**: 「エクスプローラー」ではなく「ソリューション エクスプローラー」を使用してください

2. **Program.csファイルを開く**
   - ソリューションエクスプローラーで以下の階層を辿ります：
     ```
     InventoryManagementApp
     └── src
         └── InventoryManagement.Console
             └── Program.cs
     ```
   - **Program.cs** ファイルをダブルクリックして開きます

### ステップ2: メニュー表示の更新

1. **ShowMenuメソッドを探す**
   - ファイルが開いたら **Ctrl + F** で検索ダイアログを開く
   - **「ShowMenu」** と入力して検索
   - **Enter** で該当箇所にジャンプ

2. **ShowMenuメソッド全体を選択**
   ```csharp
   static void ShowMenu()
   {
       Console.WriteLine("1. 商品一覧表示");
       Console.WriteLine("2. 商品登録");
       Console.WriteLine("3. 在庫確認");
       Console.WriteLine("4. 入庫処理");
       Console.WriteLine("5. 出庫処理");
       Console.WriteLine("6. 発注推奨リスト表示");
       Console.WriteLine("0. 終了");
   }
   ```

3. **GitHub Copilotでメニュー項目を追加**
   - 選択状態で **Ctrl + I** キーを押してインラインチャットを開く
   - **重要**: インラインチャットが開かない場合は、GitHub Copilot拡張機能が有効になっているか確認してください
   - 以下のプロンプトを日本語で入力：
   ```plaintext
   選択範囲を更新して、「7. 低在庫アラート表示」メニュー項目を追加してください。
   ```
   - **Enter** キーを押下

4. **生成されたコードを確認して承諾**
   - 以下のような結果が表示されることを確認：
   ```csharp
   static void ShowMenu()
   {
       Console.WriteLine("1. 商品一覧表示");
       Console.WriteLine("2. 商品登録");
       Console.WriteLine("3. 在庫確認");
       Console.WriteLine("4. 入庫処理");
       Console.WriteLine("5. 出庫処理");
       Console.WriteLine("6. 発注推奨リスト表示");
       Console.WriteLine("7. 低在庫アラート表示");
       Console.WriteLine("0. 終了");
   }
   ```
   - 内容が正しければ **「承諾」** または **「同意する」** をクリック

### ステップ3: Mainメソッドのswitch文を更新

1. **Mainメソッド内のswitch文を検索**
   - **Ctrl + F** で **「switch (input)」** を検索
   - Mainメソッド内のswitch文を特定

2. **switch文全体を選択**
   ```csharp
   switch (input)
   {
       case "1":
           ShowAllProducts(productService);
           break;
       case "2":
           AddNewProduct(productService);
           break;
       case "3":
           CheckStock(stockService);
           break;
       case "4":
           ProcessStockIn(stockService);
           break;
       case "5":
           ProcessStockOut(stockService);
           break;
       case "6":
           ShowReorderList(orderService);
           break;
       case "0":
           Console.WriteLine("終了します。");
           return;
       default:
           Console.WriteLine("無効な入力です。");
           break;
   }
   ```

3. **GitHub Copilotでケースを追加**
   - 選択状態で **Ctrl + I** でインラインチャットを開く
   - 以下のプロンプトを入力：
   ```plaintext
   選択範囲を更新して、case "7"を追加してください。ShowLowStockAlertメソッドを呼び出すようにしてください。productServiceとstockServiceを引数として渡してください。
   ```

4. **生成されたコードを確認**
   - switch文に以下のようなケースが追加されることを確認：
   ```csharp
   case "7":
       ShowLowStockAlert(productService, stockService);
       break;
   ```

---

## タスク2: ShowLowStockAlertメソッドの実装

### ステップ1: メソッドの設計を理解

ShowLowStockAlertメソッドの処理フロー：
1. すべての商品を取得
2. 各商品の在庫数を確認
3. 在庫数が最小在庫数を下回っている商品を検出
4. 低在庫商品のリストを表示
5. 低在庫商品が見つからない場合はメッセージを表示

### ステップ2: 基本的なShowLowStockAlertメソッドを作成

1. **Program.csの末尾に移動**
   - ファイルの最後、`ShowReorderList`メソッドの後に新しいメソッドを追加します

2. **基本的なメソッドの骨組みを作成**
   ```csharp
   static void ShowLowStockAlert(ProductService productService, StockService stockService)
   {
       // この部分をGitHub Copilotで実装します
   }
   ```

### ステップ3: GitHub Copilotを使用した包括的な実装

1. **チャットビューを開く**
   - **Ctrl + Alt + I** でチャットビューを開く

2. **必要なファイルをチャットコンテキストに追加**
   以下のファイルをドラッグ&ドロップでチャットビューに追加：
   - `Program.cs`（現在編集中のファイル）
   - `ProductService.cs`（InventoryManagement.Core/Services/ フォルダー内）
   - `StockService.cs`（InventoryManagement.Core/Services/ フォルダー内）
   - `Product.cs`（InventoryManagement.Entities/ フォルダー内）
   - `Stock.cs`（InventoryManagement.Entities/ フォルダー内）

3. **包括的な実装指示を送信**
   ```plaintext
   @workspace ShowLowStockAlertメソッドを実装してください。以下の仕様で作成してください：

   1. ProductServiceを使用してすべての商品を取得
   2. 各商品について、StockServiceを使用して現在の在庫数を取得
   3. 在庫数が最小在庫数（MinimumStock）を下回っている商品をリストに追加
   4. 低在庫商品が見つかった場合：
      - 「低在庫商品が{件数}件見つかりました」と表示
      - 各商品について「{ProductId} | {ProductName} | 現在在庫: {現在数} / 最小在庫: {最小数} - 発注推奨」の形式で表示
   5. 低在庫商品が見つからない場合：
      - 「低在庫の商品はありません」と表示

   メソッドはstatic voidで、引数はProductServiceとStockServiceです。
   ```

### ステップ4: 生成されたコードの実装

GitHub Copilotからの提案に基づいて、以下のような実装を確認してください：

```csharp
static void ShowLowStockAlert(ProductService productService, StockService stockService)
{
    Console.WriteLine("=== 低在庫アラート ===");
    
    var allProducts = productService.GetAllProducts();
    var lowStockProducts = new List<(Product product, int currentStock)>();

    foreach (var product in allProducts)
    {
        int currentStock = stockService.GetStockQuantity(product.ProductId);
        if (currentStock < product.MinimumStock)
        {
            lowStockProducts.Add((product, currentStock));
        }
    }

    if (lowStockProducts.Count > 0)
    {
        Console.WriteLine($"低在庫商品が{lowStockProducts.Count}件見つかりました");
        Console.WriteLine();

        foreach (var item in lowStockProducts)
        {
            Console.WriteLine($"{item.product.ProductId} | {item.product.ProductName} | 現在在庫: {item.currentStock} / 最小在庫: {item.product.MinimumStock} - 発注推奨");
        }
    }
    else
    {
        Console.WriteLine("低在庫の商品はありません");
    }
}
```

**実装のポイント**:
- **タプル使用**: 商品と在庫数をペアで管理
- **foreach ループ**: すべての商品をチェック
- **条件判定**: 現在在庫 < 最小在庫で判定
- **分岐処理**: 低在庫商品の有無で表示を切り替え

### ステップ5: コードの確認と調整

1. **生成されたコードをProgram.csに追加**
   - ShowLowStockAlertメソッドをProgram.csファイルの適切な位置に追加

2. **必要なusing文の確認**
   - ファイル上部に必要なusing文が含まれているか確認
   - 必要に応じて以下を追加：
   ```csharp
   using InventoryManagement.Core.Services;
   using InventoryManagement.Entities;
   ```

---

## タスク3: 機能テストの実施

### ステップ1: アプリケーションの準備

1. **ソリューションをクリーン**
   - ソリューションエクスプローラーで **InventoryManagement.Console** を右クリック
   - **「クリーン」** を選択
   - ビルドキャッシュをクリアして確実に最新コードで実行

2. **ソリューションをビルド**
   - 再度 **InventoryManagement.Console** を右クリック
   - **「ビルド」** を選択
   - エラーがないことを確認

### ステップ2: アプリケーションの実行

1. **デバッグ実行を開始**
   - **InventoryManagement.Console** プロジェクトを右クリック
   - **「デバッグ」** → **「新しいインスタンスを開始」** を選択

2. **実行環境の確認**
   - コンソールウィンドウが開くことを確認
   - メニューに「7. 低在庫アラート表示」が表示されることを確認

### ステップ3: 新機能のテスト

1. **低在庫アラート機能のテスト**
   - メニュー選択: **「7」** と入力
   - **Enter** キーを押下

2. **結果の確認**
   - 低在庫商品が表示されることを確認
   - 表示形式が正しいことを確認：
     ```
     === 低在庫アラート ===
     低在庫商品が2件見つかりました

     P004 | モニター | 現在在庫: 3 / 最小在庫: 8 - 発注推奨
     P006 | プリンター | 現在在庫: 5 / 最小在庫: 3 - 発注推奨
     ```

3. **在庫を増やしてテスト**
   - メニューから **「4」** を選択（入庫処理）
   - 商品ID: **「P004」** と入力
   - 数量: **「10」** と入力
   - 保管場所: **「倉庫A」** と入力
   - 備考: **「補充」** と入力

4. **再度低在庫アラートを確認**
   - メニューから **「7」** を選択
   - P004が低在庫リストから消えていることを確認

5. **アプリケーション終了**
   - **「0」** を入力してアプリケーションを終了

**重要な注意点**:
- CSVデータファイルの場所により、更新内容の反映場所が異なります
- デバッガー実行: `InventoryManagement.Console\bin\Debug\net8.0\Data\`
- dotnet run実行: `InventoryManagement.Console\Data\`

---

## 演習5まとめ

### 達成した内容

1. **メニュー拡張**
   - Program.csのShowMenuメソッドに新しいメニュー項目を追加
   - Mainメソッドのswitch文に新しいケースを追加

2. **低在庫アラート機能の実装**
   - ShowLowStockAlertメソッドの完全な実装
   - 商品サービスと在庫サービスの連携
   - 低在庫商品の検出とフォーマット表示

3. **機能テストの完了**
   - 低在庫商品の正常な検出
   - 表示フォーマットの確認
   - データ更新後の動作確認

### 学習ポイント

**GitHub Copilotの効果的活用**:
- メソッド実装の高速化
- 適切なロジックの提案
- コードの一貫性維持

**コーディングのベストプラクティス**:
- サービス層の適切な利用
- データ構造の効率的な管理
- ユーザーフレンドリーな出力

**在庫管理システムの機能拡張**:
- 既存機能との統合
- ビジネスロジックの実装
- エラーハンドリングの考慮

---

## トラブルシューティング

### Q: ビルドエラーが発生する
**A**: 以下の点を確認してください
- using文の追加：`using InventoryManagement.Core.Services;`、`using InventoryManagement.Entities;`
- プロジェクト参照の確認
- ProductServiceとStockServiceの依存関係注入がProgram.csに含まれているか

### Q: 低在庫アラートが正しく動作しない
**A**: 以下を確認してください
- CSVファイルの読み込み場所とデータの整合性
- 商品の最小在庫数（MinimumStock）の設定値
- StockServiceのGetStockQuantityメソッドが正しく動作しているか

### Q: 低在庫商品が表示されない
**A**: 以下を確認してください
- Products.csvの最小在庫数の設定
- Stocks.csvの現在在庫数
- 条件判定ロジック（currentStock < product.MinimumStock）が正しいか

### Q: メニューに「7. 低在庫アラート表示」が表示されない
**A**: 以下を確認してください
- ShowMenuメソッドが正しく更新されているか
- ファイルが保存されているか
- ビルドが成功しているか

---

## 次のステップ

このラボでは、在庫管理システムに低在庫アラート機能を追加しました。次のラボでは、作成した機能の単体テストを開発し、品質保証の観点から重要な作業を学習します。

---

## 応用課題（オプション）

より高度な学習を希望する場合は、以下の拡張機能にチャレンジしてください：

1. **優先度表示**
   - 在庫不足の深刻度（最小在庫数との差）に応じて優先度を表示

2. **カテゴリーフィルター**
   - 特定のカテゴリーのみの低在庫商品を表示

3. **自動発注提案**
   - 低在庫商品に対して推奨発注数を自動計算して表示

4. **CSV出力**
   - 低在庫商品リストをCSVファイルとして出力

これらの機能を実装することで、GitHub Copilotの活用スキルをさらに向上させることができます。