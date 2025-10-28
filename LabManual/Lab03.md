# ラボ03: GitHub Copilot ツールを使用して単体テストを開発する

## 概要

このラボでは、GitHub Copilotを活用して在庫管理システムの単体テストを開発します。既存のサービス層のテストに加えて、データアクセス層（InventoryManagement.Data）の包括的なテストを作成し、コード品質の向上を図ります。

### 学習目標
- GitHub Copilotを使用した効率的な単体テスト作成
- xUnitフレームワークとNSubstituteライブラリの実践的活用
- テスト駆動開発（TDD）の理解
- データアクセス層のテスト設計パターンの習得

---

## 開始する前に

### プロジェクトの現状確認

**UnitTestsプロジェクトの構造**:
```
UnitTests/
├── Core/
│   └── ProductServiceTests/
│       ├── GetProductByIdTest.cs
│       ├── SearchByNameTest.cs
│       ├── GetByCategoryTest.cs
│       └── AddProductTest.cs
├── ProductFactory.cs
├── StockFactory.cs
├── StockHistoryFactory.cs
└── UnitTests.csproj
```

**現在の対応状況**:
- **一部完了**: InventoryManagement.Core/Services層の一部テスト
- **今回実装**: InventoryManagement.Data層のテスト

**使用技術**:
- **xUnit**: .NET用テストフレームワーク
- **NSubstitute**: モックオブジェクト生成ライブラリ
- **GitHub Copilot**: AI支援によるテストコード生成

---

## 演習7: GitHub Copilot を使用して UnitTests プロジェクトを評価および拡張する

### タスク1: 既存の単体テストアプローチの分析

#### ステップ1: 既存テストパターンの理解

1. **Visual Studio Codeの準備**
   - InventoryManagementAppプロジェクトが開かれていることを確認
   - エディターで開いているすべてのファイルを閉じる（**Ctrl + K, Ctrl + W**）

2. **チャットビューを開く**
   - **Ctrl + Alt + I** キーでチャットビューを開く
   - 既存のチャット履歴をクリアして新しい会話を開始

3. **分析対象ファイルをチャットコンテキストに追加**
   以下のファイルをドラッグ&ドロップでチャットビューに追加：
   
   **テストファイル群**:
   - `tests/UnitTests/ProductFactory.cs`
   - `tests/UnitTests/StockFactory.cs`
   - `tests/UnitTests/StockHistoryFactory.cs`
   - `tests/UnitTests/Core/ProductServiceTests/GetProductByIdTest.cs`
   - `tests/UnitTests/Core/ProductServiceTests/SearchByNameTest.cs`
   - `tests/UnitTests/Core/ProductServiceTests/GetByCategoryTest.cs`
   
   **実装ファイル群**:
   - `src/InventoryManagement.Core/Services/ProductService.cs`
   - `src/InventoryManagement.Core/Services/StockService.cs`

4. **テストアプローチの分析**
   ```plaintext
   @workspace このワークスペースで実装されている単体テストのアプローチについて説明してください。以下の観点で分析してください：
   1. モックの使用方法
   2. テストデータの作成パターン（ファクトリークラス）
   3. テストケースの構成方法
   4. アサーションの書き方
   ```

#### ステップ2: テストアプローチの利点確認

1. **利点分析の実行**
   ```plaintext
   @workspace このテストアプローチにはどのような利点がありますか？コード品質、保守性、開発効率の観点から説明してください。
   ```

2. **GitHub Copilotの回答を確認**
   以下のような分析結果が提示されることを確認：
   
   **主要な利点**:
   - **依存関係の分離**: NSubstituteによるモック化で外部依存を排除
   - **データ一貫性**: ファクトリーメソッドによる統一されたテストデータ生成
   - **可読性**: 記述的なテストメソッド名とわかりやすい構造
   - **包括的カバレッジ**: 成功ケースとエラーケースの両方をテスト
   - **迅速なフィードバック**: 単体テストによる即座の品質確認

#### ステップ3: 拡張戦略の検討

1. **拡張方針の確認**
   ```plaintext
   @workspace UnitTestsプロジェクトをInventoryManagement.Dataプロジェクトのメソッドテストに拡張する方法について、プロセスの概要を教えてください。
   ```

2. **拡張手順の理解**
   GitHub Copilotから以下のような手順が提示されることを確認：
   
   **基本的な拡張手順**:
   1. **テスト対象の特定**: データアクセス層のテスト対象メソッドを特定
   2. **テストクラスの作成**: Data専用のテストクラス構造を構築
   3. **単体テストの実装**: xUnitとNSubstituteを使用したテストコード作成
   4. **テスト実行の確認**: Visual Studio CodeのTest Explorerでテスト実行

---

### タスク2: データアクセス層テストの実装

#### ステップ1: StockDataクラスの分析

1. **対象クラスの確認**
   - `src/InventoryManagement.Data/StockData.cs` を開く

2. **クラス構造の理解**
   ```csharp
   public class StockData : CsvDataAccess<Stock>
   {
       // GetStockByProductId: 商品IDで在庫情報を取得
       public Stock GetStockByProductId(string productId)
       
       // UpdateStock: 在庫情報を更新
       public bool UpdateStock(Stock updatedStock)
       
       // GetAllStocks: すべての在庫情報を取得
       public List<Stock> GetAllStocks()
       
       // AddStock: 新規在庫を追加
       public bool AddStock(Stock stock)
   }
   ```

   **重要なポイント**:
   - CsvDataAccessクラスを継承してCSV操作を実装
   - GetStockByProductIdは指定商品IDの在庫を返す（見つからない場合はnull）
   - UpdateStockは既存在庫の更新とデータ保存を実行
   - AddStockは新規在庫の追加（重複チェック含む）

#### ステップ2: テスト用フォルダー構造の作成

1. **フォルダー構造の作成**
   - `tests/UnitTests` フォルダー内に以下の構造を作成：
   ```
   UnitTests/
   └── Data/
       └── StockDataTests/
   ```

2. **GetStockByProductIdテストクラスの作成**
   - `StockDataTests` フォルダー内に `GetStockByProductIdTest.cs` ファイルを作成

#### ステップ3: GetStockByProductIdテストクラスの実装

1. **基本構造の生成**
   - 以下のファイルをチャットコンテキストに追加：
     - `StockData.cs`（テスト対象）
     - `GetProductByIdTest.cs`（参考として）
     - `ProductService.cs`（参考として）
     - `StockFactory.cs`（テストデータ生成用）
     - `Stock.cs`（エンティティ）

2. **フィールドとコンストラクターの生成**
   ```plaintext
   @workspace GetStockByProductIdTest.csファイルのフィールドとクラスコンストラクターを作成してください。namespace はInventoryManagement.UnitTests.Data.StockDataTestsとしてください。このクラスはStockData.csファイルのGetStockByProductIdメソッドの単体テストに使用されます。

   以下の要件に従ってください：
   1. private readonlyフィールド: _stockData（StockData型）
   2. コンストラクターで_stockDataを部分モック（Substitute.ForPartsOf）でインスタンス化してください
   3. 部分モックを使用することで、GetAll()メソッドはモック化し、GetStockByProductIdメソッドは実装を実行します
   ```

3. **生成されたコードの確認と修正**
   基本的な構造は以下のようになります：
   ```csharp
   using NSubstitute;
   using InventoryManagement.Data;
   using InventoryManagement.Entities;

   namespace InventoryManagement.UnitTests.Data.StockDataTests
   {
      public class GetStockByProductIdTest
      {
         private readonly StockData _stockData;

         public GetStockByProductIdTest()
         {
               _stockData = Substitute.ForPartsOf<StockData>("test.csv");
         }
      }
   }
   ```

   **修正ポイント**:
   - 名前空間を `InventoryManagement.UnitTests.Data.StockDataTests` に設定
   - 必要なusing文の追加

#### ステップ4: テストメソッドの実装

1. **成功ケースのテスト作成**
   - GetStockByProductIdTest.cs内のクラスにカーソルを置く

2. **GetStockByProductId成功ケースの実装**
   ```plaintext
   @workspace 選択範囲を更新して、StockData.GetStockByProductIdメソッドの単体テストを含めてください。この単体テストは、Data層のテストとして、GetStockByProductIdメソッドの内部ロジック（ループ処理と条件分岐）をテストする必要があります。

   以下の要件に従ってください：
   1. _stockDataは部分モック（ForPartsOf）として作成済みです
   2. _stockData.GetAll()をモック化して、テスト用の在庫リストを返すように設定してください
   3. GetStockByProductIdメソッド自体は実装を実行します（モック化しません）
   4. 商品IDで在庫が見つかったケースをテストしてください
   5. StockFactoryを使用してテストデータを作成してください
   6. アサートでは、返却された在庫のProductIdとQuantityが期待される値と一致することを確認してください
   ```

3. **生成されたテストメソッドの確認**
   ```csharp
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

4. **失敗ケースのテスト作成**
   GitHub Copilotのオートコンプリート機能を使用：
   - GetStockByProductId_ReturnsStock_WhenProductIdIsFoundメソッドの後に空行を作成
   - GitHub Copilotの提案を確認して以下のようなテストを作成：

   ```csharp
   [Fact(DisplayName = "StockData.GetStockByProductId: 商品IDで在庫が見つからない場合、nullを返す")]
   public void GetStockByProductId_ReturnsNull_WhenProductDoesNotExist()
   {
      // Arrange
      var stocks = new List<Stock>
      {
            StockFactory.CreateTestStock("P002"),
            StockFactory.CreateTestStock("P003")
      };
      _stockData.GetAll().Returns(stocks);

      // Act
      var actualStock = _stockData.GetStockByProductId("P001");

      // Assert
      Assert.Null(actualStock);
   }
   ```

---

## タスク3: サービス層のAddProductTestの修正

### ステップ1: AddProductTestクラスの確認

1. **AddProductTest.csファイルを開く**
   - `tests/UnitTests/Core/ProductServiceTests/AddProductTest.cs` を開く

2. **現在の状態を確認**
   ```csharp
   using NSubstitute;
   using InventoryManagement.Core.Services;
   using InventoryManagement.Data;
   using InventoryManagement.Entities;

   namespace InventoryManagement.UnitTests.Core.ProductServiceTests;

   public class AddProductTest
   {
       
   }
   ```

   **現状**: クラスは作成されているが、テストメソッドが未実装

### ステップ2: フィールドとコンストラクターの追加

1. **GitHub Copilotで基本構造を生成**
   - AddProductTestクラス内にカーソルを置く
   - 以下のプロンプトを入力：
   ```plaintext
   @workspace AddProductTestクラスにフィールドとコンストラクターを追加してください。他のProductServiceTestsクラスと同じパターンで、_mockProductDataと_productServiceフィールドを作成してください。
   ```

2. **生成されたコードの確認**
   ```csharp
   public class AddProductTest
   {
       private readonly ProductData _mockProductData;
       private readonly ProductService _productService;

       public AddProductTest()
       {
           _mockProductData = Substitute.For<ProductData>("test.csv");
           _productService = new ProductService(_mockProductData);
       }
   }
   ```

### ステップ3: テストメソッドの実装

1. **成功ケースのテスト作成**
   ```plaintext
   @workspace AddProductTestクラスに、ProductService.AddProductメソッドの単体テストを追加してください。有効な商品を追加して成功するケースをテストしてください。ProductFactoryを使用してテストデータを作成し、_mockProductDataのAddProductメソッドがtrueを返すように設定してください。
   ```

2. **生成されたテストメソッドの確認**
   ```csharp
   [Fact(DisplayName = "ProductService.AddProduct: Returns true when product is valid")]
   public void AddProduct_ReturnsTrue_WhenProductIsValid()
   {
       // Arrange
       var product = ProductFactory.CreateTestProduct();
       _mockProductData.AddProduct(product).Returns(true);
       
       // Act
       var result = _productService.AddProduct(product);
       
       // Assert
       Assert.True(result);
   }
   ```

3. **追加の失敗ケーステストを作成**
   - 成功ケースのテストメソッドの後に空行を作成
   - GitHub Copilotの提案を確認：

   ```csharp
   [Fact(DisplayName = "ProductService.AddProduct: Returns false when product is null")]
   public void AddProduct_ReturnsFalse_WhenProductIsNull()
   {
       // Arrange
       Product product = null;
       
       // Act
       var result = _productService.AddProduct(product);
       
       // Assert
       Assert.False(result);
   }

   [Fact(DisplayName = "ProductService.AddProduct: Returns false when product ID is empty")]
   public void AddProduct_ReturnsFalse_WhenProductIdIsEmpty()
   {
       // Arrange
       var product = ProductFactory.CreateTestProduct();
       product.ProductId = string.Empty;
       
       // Act
       var result = _productService.AddProduct(product);
       
       // Assert
       Assert.False(result);
   }

   [Fact(DisplayName = "ProductService.AddProduct: Returns false when unit price is invalid")]
   public void AddProduct_ReturnsFalse_WhenUnitPriceIsInvalid()
   {
       // Arrange
       var product = ProductFactory.CreateProductWithInvalidPrice();
       
       // Act
       var result = _productService.AddProduct(product);
       
       // Assert
       Assert.False(result);
   }
   ```

   **ProductFactoryのメソッドが提案されない場合は**:
   - コメントで明示的に指示する（ // Arrange: ProductFactory.CreateProductWithInvalidPrice()を使用）
   - メソッド名を途中まで入力する（例: ProductFactory.CreateProductWith）
   - 直前のテストでFactoryを使った例を書く（最初のテストメソッドでFactoryパターンを示すと、以降のテストで自動提案されやすくなる）
   - ファイル冒頭にFactoryメソッドの一覧をコメントで記載する
      ```
      // 使用可能なFactoryメソッド:
      // - ProductFactory.CreateTestProduct()
      // - ProductFactory.CreateInvalidProduct()
      // - ProductFactory.CreateProductWithInvalidPrice()
      // - ProductFactory.CreateProductWithNegativeMinStock()      
      ```
---

## タスク4: テストの実行と確認

### ステップ1: ビルドとエラーチェック

1. **ソリューションのビルド**
   - ソリューションエクスプローラーで **UnitTests** プロジェクトを右クリック
   - **「ビルド」** を選択
   - エラーがないことを確認（警告は無視）

### ステップ2: テスト実行

1. **Test Explorerの使用**
   - 左サイドバーの**ビーカーアイコン**（テスト）をクリック
   - **「UnitTests」** ノードを展開
   - **「Data」** → **「StockDataTests」** → **「GetStockByProductIdTest」** を展開

2. **個別テストの実行**
   - **「StockData.GetStockByProductId: Returns stock when product ID exists」** テストを実行
   - テスト結果で緑色のチェックマークが表示されることを確認

3. **エディターからの実行**
   - GetStockByProductIdTest.csファイルを開く
   - テストメソッドの左側にある**緑色の再生ボタン**をクリック
   - テスト結果がエディター内に表示されることを確認

### ステップ3: すべてのテストケースの確認

1. **Data層のテスト確認**
   - GetStockByProductIdの成功ケースが成功することを確認
   - GetStockByProductIdの失敗ケースが成功（nullが正しく返される）することを確認

2. **Core層のテスト確認**
   - AddProductTestのすべてのテストケースを実行
   - すべてのテストに緑色のチェックマークが表示されることを確認

3. **テスト結果の分析**
   - Test Explorerでテスト実行時間や詳細結果を確認
   - 失敗したテストがある場合は、エラーメッセージを確認して修正

---

## 演習7まとめ

### 達成した内容

1. **既存テストアプローチの理解**
   - モック化パターンの分析
   - ファクトリーメソッドの利用方法
   - テストデータ作成の一貫性確保

2. **Data層テストの実装**
   - StockDataクラスの包括的テスト
   - 成功・失敗両ケースの適切なテストカバレッジ
   - NSubstituteを使用したモック化

3. **Core層テストの完成**
   - AddProductTestクラスの完全実装
   - 複数の失敗ケースのテスト
   - バリデーションロジックの検証

4. **テスト実行環境の活用**
   - Visual Studio Code Test Explorerの活用
   - 継続的なテスト実行フローの確立

### 学習ポイント

**GitHub Copilotの効果的活用**:
- テストコード生成の高速化
- 適切なアサーション手法の提案
- テストケースの網羅的な生成支援

**単体テストのベストプラクティス**:
- 外部依存の適切なモック化
- テストデータの一貫した管理
- 可読性の高いテストメソッド命名
- AAA（Arrange-Act-Assert）パターンの遵守

**データアクセステストの重要性**:
- インフラストラクチャ層の品質保証
- CSVデータ操作の正確性確認
- エラーハンドリングの検証

---

## 次のステップ

演習7では、StockDataクラスとAddProductメソッドの基本的なテストを実装しました。次の段階では、以下の拡張を検討してください：

1. **ProductDataクラスの包括的テスト**
2. **StockServiceクラスのテスト実装**
3. **OrderServiceクラスのテスト実装**
4. **UpdateStockメソッドの詳細テスト**

これらのテストを通じて、在庫管理システム全体の品質と信頼性を大幅に向上させることができます。

---

## トラブルシューティング

### Q: ビルドエラー「型または名前空間名が見つかりません」
**A**: 以下の手順で解決してください：
1. ファイル上部にusing文を追加：`using InventoryManagement.Data;`、`using InventoryManagement.Entities;`
2. プロジェクトのターゲットフレームワークがnet8.0であることを確認
3. ソリューションをクリーンビルド

### Q: テスト実行時に「NullReferenceException」が発生
**A**: モックの設定を確認してください：
1. _mockStockData.GetAll().Returns()でテストデータを正しく設定しているか確認
2. テストデータが正しく初期化されているか確認
3. StockFactoryのメソッドが正しく呼ばれているか確認

### Q: GitHub Copilotが期待したテストコードを生成しない
**A**: 以下で最新のパターンを誘導してください：
1. プロンプトで具体的にテストメソッド名やアサーションを指定
2. 参考となる既存のテストファイルをコンテキストに追加
3. 複数の提案から最適なものを選択

### Q: Test Explorerにテストが表示されない
**A**: 以下を確認してください：
1. ビルドが成功しているか確認
2. テストメソッドに[Fact]属性が付いているか確認
3. Visual Studio Codeを再起動

### Q: モックが正しく動作しない
**A**: 以下を確認してください：
1. NSubstituteパッケージが正しくインストールされているか
2. Substitute.For<T>の構文が正しいか
3. Returns()メソッドで期待値を正しく設定しているか

---

## 応用課題（オプション）

より高度な学習を希望する場合は、以下のテスト実装にチャレンジしてください：

1. **UpdateProductTestクラスの完成**
   - UpdateProductTestのテストケース実装
   - テストケース作成後、ProductServiceクラスの修正

2. **StockServiceクラスのテスト**
   - AddStockメソッドのテスト（履歴記録を含む）
   - RemoveStockメソッドのテスト（在庫不足ケース含む）

3. **OrderServiceクラスのテスト**
   - GetLowStockProductsメソッドのテスト
   - GenerateOrderRecommendationsメソッドのテスト


これらのテストを実装することで、GitHub Copilotの活用スキルとテスト設計能力をさらに向上させることができます。