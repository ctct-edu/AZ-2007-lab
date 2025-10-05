# パート3: GitHub Copilot ツールを使用して単体テストを開発する

## 概要

このパートでは、GitHub Copilotを活用して図書館管理システムの単体テストを開発します。既存のサービス層のテストに加えて、データアクセス層（Library.Infrastructure）の包括的なテストを作成し、コード品質の向上を図ります。

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
├── ApplicationCore/
│   ├── LoanService/
│   │   ├── ExtendLoan.cs
│   │   └── ReturnLoan.cs
│   └── PatronService/
│       └── RenewMembership.cs
├── LoanFactory.cs
├── PatronFactory.cs
└── UnitTests.csproj
```

**現在の対応状況**:
- **完了**: Library.ApplicationCore/Services層のテスト
- **今回実装**: Library.Infrastructure/Data層のテスト

**使用技術**:
- **xUnit**: .NET用テストフレームワーク
- **NSubstitute**: モックオブジェクト生成ライブラリ
- **GitHub Copilot**: AI支援によるテストコード生成

---

## 演習7: GitHub Copilot を使用して UnitTests プロジェクトを評価および拡張する

### タスク1: 既存の単体テストアプローチの分析

#### ステップ1: 既存テストパターンの理解

1. **Visual Studio Codeの準備**
   - AccelerateDevGitHubCopilotプロジェクトが開かれていることを確認
   - エディターで開いているすべてのファイルを閉じる（**Ctrl + K, Ctrl + W**）

2. **チャットビューを開く**
   - **Ctrl + Alt + I** キーでチャットビューを開く
   - 既存のチャット履歴をクリアして新しい会話を開始

3. **分析対象ファイルをチャットコンテキストに追加**
   以下のファイルをドラッグ&ドロップでチャットビューに追加：
   
   **テストファイル群**:
   - `tests/UnitTests/LoanFactory.cs`
   - `tests/UnitTests/PatronFactory.cs`
   - `tests/UnitTests/ApplicationCore/LoanService/ExtendLoan.cs`
   - `tests/UnitTests/ApplicationCore/LoanService/ReturnLoan.cs`
   - `tests/UnitTests/ApplicationCore/PatronService/RenewMembership.cs`
   
   **実装ファイル群**:
   - `src/Library.ApplicationCore/Services/LoanService.cs`
   - `src/Library.ApplicationCore/Services/PatronService.cs`

4. **テストアプローチの分析**
   ```plaintext
   @workspace このワークスペースで実装されている単体テストのアプローチについて説明してください。以下の観点で分析してください：
   1. モックの使用方法
   2. テストデータの作成パターン
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
   @workspace UnitTestsプロジェクトをLibrary.Infrastructureプロジェクトのメソッドテストに拡張する方法について、プロセスの概要を教えてください。
   ```

2. **拡張手順の理解**
   GitHub Copilotから以下のような手順が提示されることを確認：
   
   **基本的な拡張手順**:
   1. **プロジェクト参照の追加**: UnitTests.csprojにLibrary.Infrastructureへの参照を追加
   2. **テスト対象の特定**: データアクセス層のテスト対象メソッドを特定
   3. **テストクラスの作成**: Infrastructure専用のテストクラス構造を構築
   4. **単体テストの実装**: xUnitとNSubstituteを使用したテストコード作成
   5. **テスト実行の確認**: Visual Studio CodeのTest Explorerでテスト実行

---

### タスク2: データアクセス層テストの実装

#### ステップ1: プロジェクト参照の追加

1. **UnitTests.csprojファイルを開く**
   - ソリューションエクスプローラーで `tests/UnitTests/UnitTests.csproj` を開く

2. **現在のプロジェクト参照を確認**
   ```xml
   <ItemGroup>
     <ProjectReference Include="..\..\src\Library.ApplicationCore\Library.ApplicationCore.csproj" />
   </ItemGroup>
   ```

3. **Library.Infrastructure参照の追加方法を確認**
   - UnitTests.csprojファイルをチャットコンテキストに追加
   - 以下のプロンプトを入力：
   ```plaintext
   @workspace UnitTests.csprojファイル内にLibrary.Infrastructureプロジェクトへの参照を追加する方法を説明してください。
   ```

4. **プロジェクトファイルの更新**
   GitHub Copilotの指示に従って以下を追加：
   ```xml
   <ItemGroup>
     <ProjectReference Include="..\..\src\Library.ApplicationCore\Library.ApplicationCore.csproj" />
     <ProjectReference Include="..\..\src\Library.Infrastructure\Library.Infrastructure.csproj" />
   </ItemGroup>
   ```

#### ステップ2: JsonLoanRepositoryクラスの分析

1. **対象クラスの確認**
   - `src/Library.Infrastructure/Data/JsonLoanRepository.cs` を開く

2. **クラス構造の理解**
   ```csharp
   public class JsonLoanRepository : ILoanRepository
   {
       private readonly JsonData _jsonData;
       
       // GetLoan: ID指定で貸出情報を取得
       public async Task<Loan?> GetLoan(int id)
       
       // UpdateLoan: 貸出情報を更新
       public async Task UpdateLoan(Loan loan)
   }
   ```

   **重要なポイント**:
   - JsonDataクラスに依存してデータ操作を実行
   - GetLoanは指定IDの貸出を返す（見つからない場合はnull）
   - UpdateLoanは既存貸出の更新とデータ保存を実行

#### ステップ3: テスト用フォルダー構造の作成

1. **フォルダー構造の作成**
   - `tests/UnitTests` フォルダー内に以下の構造を作成：
   ```
   UnitTests/
   └── Infrastructure/
       └── JsonLoanRepository/
   ```

2. **GetLoanテストクラスの作成**
   - `JsonLoanRepository` フォルダー内に `GetLoan.cs` ファイルを作成

#### ステップ4: GetLoanテストクラスの実装

1. **基本構造の生成**
   - 以下のファイルをチャットコンテキストに追加：
     - `JsonLoanRepository.cs`
     - `ReturnLoan.cs`（参考として）
     - `LoanService.cs`（参考として）
     - `LoanFactory.cs`
     - `JsonData.cs`

2. **フィールドとコンストラクターの生成**
   ```plaintext
   @workspace GetLoan.csファイルのフィールドとクラスコンストラクターを作成してください。namespace はUnitTests.Infrastructure.JsonLoanRepositoryTestsとしてください。このクラスはJsonLoanRepository.csファイルのGetLoanメソッドの単体テストに使用されます。以下のprivate readonlyフィールドを作成してください：_mockLoanRepository、_jsonLoanRepository、_configuration、_jsonData。GetLoanTestコンストラクターでこれらのフィールドをインスタンス化してください。JsonDataオブジェクトのインスタンス化にはConfigurationBuilderを使用してください。
   ```

3. **生成されたコードの確認と修正**
   基本的な構造は以下のようになります：
   ```csharp
   using NSubstitute;
   using Library.ApplicationCore;
   using Library.ApplicationCore.Entities;
   using Library.Infrastructure.Data;
   using Microsoft.Extensions.Configuration;

   namespace UnitTests.Infrastructure.JsonLoanRepositoryTests;

   public class GetLoanTest
   {
       private readonly ILoanRepository _mockLoanRepository;
       private readonly JsonLoanRepository _jsonLoanRepository;
       private readonly IConfiguration _configuration;
       private readonly JsonData _jsonData;

       public GetLoanTest()
       {
           _mockLoanRepository = Substitute.For<ILoanRepository>();
           _configuration = new ConfigurationBuilder().Build();
           _jsonData = new JsonData(_configuration);
           _jsonLoanRepository = new JsonLoanRepository(_jsonData);
       }
   }
   ```

   **修正ポイント**:
   - 名前空間を `UnitTests.Infrastructure.JsonLoanRepositoryTests` に設定
   - 必要なusing文の追加
   - ConfigurationBuilderの簡略化

    > **GitHub Copilotが AddJsonFile を提案した場合の対処法**
    > 
    > GitHub Copilotが以下のようなコードを提案することがあります：
    > ```csharp
    > _configuration = new ConfigurationBuilder()
    >     .AddJsonFile("appSettings.json", optional: true)
    >     .Build();
    > ```
    > 
    > **この場合は以下のエラーが発生します：**
    > ```
    > 'ConfigurationBuilder' に 'AddJsonFile' の定義が含まれておらず...
    > ```
    > 
    > **解決方法（推奨）**: テスト用に簡略化してください
    > ```csharp
    > _configuration = new ConfigurationBuilder().Build();
    > ```

#### ステップ5: テストメソッドの実装

1. **成功ケースのテスト作成**
   - 以下のファイルを追加でチャットコンテキストに追加：
     - `Loans.json`（参照データとして）
   - GetLoan.cs内のプログラムを全選択状態にする

2. **GetLoan成功ケースの実装**
   ```plaintext
   @workspace 選択範囲を更新して、JsonLoanRepository.GetLoanメソッドの単体テストを含めてください。この単体テストは、データ内で貸出IDが見つかったケースをテストする必要があります。_mockLoanRepositoryを使用して期待される返却貸出を配置し、_jsonLoanRepositoryを使用して実際の貸出を返却してください。アサートでは、返却された貸出IDが期待される貸出IDと一致することを確認してください。Loans.jsonファイルに存在する貸出IDを使用してください。
   ```

3. **生成されたテストメソッドの確認**
   ```csharp
   [Fact(DisplayName = "JsonLoanRepository.GetLoan: Returns loan when loan ID exists in data")]
   public async Task GetLoan_ReturnsLoanWhenLoanIdIsFound()
   {
       // Arrange
       var loanId = 1; // Loans.jsonファイルに存在する貸出IDを使用
       var expectedLoan = new Loan { 
           Id = loanId, 
           BookItemId = 101, 
           PatronId = 202, 
           LoanDate = DateTime.Now, 
           DueDate = DateTime.Now.AddDays(14) 
       };
       _mockLoanRepository.GetLoan(loanId).Returns(expectedLoan);
       
       // Act
       var actualLoan = await _jsonLoanRepository.GetLoan(loanId);
       
       // Assert
       Assert.NotNull(actualLoan);
       Assert.Equal(expectedLoan.Id, actualLoan?.Id);
   }
   ```

4. **失敗ケースのテスト作成**
   GitHub Copilotのオートコンプリート機能を使用：
   - GetLoan_ReturnsLoanWhenLoanIdIsFoundメソッドの後に空行を作成
   - GitHub Copilotの提案を確認して以下のようなテストを作成：

   ```csharp
   [Fact(DisplayName = "JsonLoanRepository.GetLoan: 貸出IDが見つからない場合にnullを返す")]
   public async Task GetLoan_ReturnsNullWhenLoanIdIsNotFound()
   {
       // Arrange
       var loanId = 999; // Loans.jsonファイルに存在しない貸出IDを使用
       
       // Act
       var actualLoan = await _jsonLoanRepository.GetLoan(loanId);
       
       // Assert
       Assert.Null(actualLoan);
   }
   ```

#### ステップ6: JSONデータファイルの参照設定

1. **UnitTests.csprojの更新**
   テストでJSONファイルにアクセスするため、以下を追加：
   ```xml
   <ItemGroup>
       <None Include="..\..\src\Library.Console\Json\**\*">
           <Link>Json\%(RecursiveDir)%(FileName)%(Extension)</Link>
           <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
   </ItemGroup>
   ```

   **重要**: この設定により、テスト実行時にJSONファイルが出力ディレクトリにコピーされます。

---

### タスク3: テストの実行と確認

#### ステップ1: ビルドとエラーチェック

1. **ソリューションのビルド**
   - ソリューションエクスプローラーで **GuidedProjectApp** を右クリック
   - **「ビルド」** を選択
   - エラーがないことを確認（警告は無視）

#### ステップ2: テスト実行

1. **Test Explorerの使用**
   - 左サイドバーの**ビーカーアイコン**（テスト）をクリック
   - **「UnitTests」** ノードを展開
   - **「Infrastructure」** → **「JsonLoanRepositoryTests」** → **「GetLoanTest」** を展開

2. **個別テストの実行**
   - **「JsonLoanRepository.GetLoan: データに貸出IDが存在する場合に貸出を返す」** テストを実行
   - テスト結果で緑色のチェックマークが表示されることを確認

3. **エディターからの実行**
   - GetLoan.csファイルを開く
   - テストメソッドの左側にある**緑色の再生ボタン**をクリック
   - テスト結果がエディター内に表示されることを確認

#### ステップ3: 両方のテストケースの確認

1. **成功ケースの確認**
   - 存在する貸出IDでのテストが成功することを確認

2. **失敗ケースの確認**
   - 存在しない貸出IDでのテストが成功（nullが正しく返される）することを確認

3. **テスト結果の分析**
   - 両方のテストに緑色のチェックマークが表示されることを確認
   - Test Explorerでテスト実行時間や詳細結果を確認

---

## 演習7まとめ

### 達成した内容

1. **既存テストアプローチの理解**
   - モック化パターンの分析
   - ファクトリーメソッドの利用方法
   - テストデータ作成の一貫性確保

2. **Infrastructure層テストの実装**
   - プロジェクト参照の正しい設定
   - JsonLoanRepositoryクラスの包括的テスト
   - 成功・失敗両ケースの適切なテストカバレッジ

3. **テスト実行環境の構築**
   - JSONデータファイルの正しい参照設定
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

**データアクセステストの重要性**:
- インフラストラクチャ層の品質保証
- JSONデータ操作の正確性確認
- エラーハンドリングの検証

---

## 次のステップ

演習7では、JsonLoanRepositoryクラスの基本的なテストを実装しました。次の段階では、以下の拡張を検討してください：

1. **JsonPatronRepositoryクラスのテスト実装**
2. **JsonDataクラスの包括的テスト**
3. **UpdateLoanメソッドの詳細テスト**
4. **エラーケースの追加テスト**

これらのテストを通じて、図書館管理システム全体の品質と信頼性を大幅に向上させることができます。