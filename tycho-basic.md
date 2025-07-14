  ## Tycho 整體架構

  Tycho 是一個綜合性的區塊鏈交易基礎設施，專為 solver 和去中心化交易所 (DEX) 設計，用於優化鏈上交易。

  For Solver 的三大核心服務

  1. Indexer 服務

  用途：
  - 串流即時鏈上流動性資料
  - 提供結構化資料來鏡像鏈上狀態

  兩種索引方式：
  - Native Simulation Indexing： 提供結構化資料，允許在 VM 外模擬協議邏輯
  - VM Compatibility Indexing： 追蹤協議合約狀態，支援本地模擬

  如何使用：
  - 通過 CLI、Rust 和 Python client 存取
  - 可以按 TVL (Total Value Locked) 篩選協議狀態
  - 支援多個區塊鏈協議的即時資料串流

  2. Simulation 服務

  用途：
  - 模擬與區塊鏈協議狀態的互動
  - 計算現貨價格
  - 跨不同協議模擬代幣交換

  主要功能：
  - spot_price：回傳資金池當前邊際價格
  - get_amount_out：模擬代幣交換
  - fee：回傳協議手續費比率
  - get_limits：回傳代幣間最大可交易金額

  如何使用：
  - 使用 Rust crate
  - 創建協議流，篩選代幣
  - 在多個流動性池間模擬交換

  3. Execution 服務

  用途：
  - 執行跨不同協議的交換
  - 提供編碼和執行區塊鏈交換的工具

  主要組件：
  - Encoding： Rust crate，產生交換執行的 calldata
  - Executing： Solidity 合約，執行鏈上交易

  三種代幣轉移方式：
  - Permit2： 使用 Uniswap 的 Permit2 合約的現代批准方法
  - Standard ERC20 Approvals： 傳統代幣批准機制
  - Direct Transfers： 在同一交易中直接轉移代幣

  整體使用場景

  適合的使用者：
  - 需要準確即時區塊鏈資料的 solver
  - 想要整合和追蹤協議組件的 DEX
  - 需要全面鏈上流動性資訊的開發者

  主要價值：
  - 即時市場資料獲取
  - 跨協議交易路徑優化
  - 模擬交換機會比較
  - 最佳交易策略執行

---

### REVM 是什麼？為什麼需要它？

  REVM 核心概念

  REVM (Rust Ethereum Virtual Machine) 是用 Rust 實現的高效能 Ethereum Virtual Machine，專注於速度和簡潔性。

  為什麼 Solver 需要 REVM？

  1. 速度優勢
  - 比標準 EVM 快數倍
  - 本地模擬無需網路延遲
  - 毫秒級交易模擬

  2. 離線模擬能力
  傳統方式: Solver → RPC Node → 網路延遲 → 結果
  REVM 方式: Solver → 本地模擬 → 即時結果

  3. 狀態管理
  - 可以任意修改合約狀態進行測試
  - 模擬大額交易而不影響實際餘額
  - 測試極端市場條件

  實際應用場景

  1. MEV 策略測試
  // 模擬 arbitrage 機會
  let result = revm.simulate_transaction(
      swap_transaction,
      modified_state  // 注入測試餘額
  );

  2. 協議風險評估
  - 模擬大額提取對流動性池的影響
  - 測試協議在極端條件下的表現
  - 評估清算風險

  3. 最佳路由計算
  - 並行模擬多個交易路徑
  - 計算最佳執行順序
  - 預測 gas 成本

### Native vs VM Indexing 詳細對比

⏺ Native Simulation Indexing

  特點：
  - 提供結構化、解析過的協議資料
  - 直接存儲交易曲線參數 (如 AMM 的 reserves、fee rates)
  - 無需虛擬機器執行，計算速度極快

  適用場景：
  - 需要大量分析和比較不同協議
  - 即時價格計算和路由優化
  - 歷史資料分析和趨勢預測

  資料結構範例：
  {
    "pool": "0x...",
    "token0": "WETH",
    "token1": "USDC",
    "reserves": [1000, 3000000],
    "fee": 0.003,
    "timestamp": "2024-01-01T00:00:00Z"
  }

  VM Compatibility Indexing

  特點：
  - 追蹤完整合約狀態
  - 可以執行任意合約函數
  - 完全模擬鏈上環境

  適用場景：
  - 需要精確模擬複雜協議邏輯
  - 測試新交易策略
  - 處理非標準或創新協議

  優勢：
  - 100% 精確度，與鏈上結果完全一致
  - 支援任意複雜度的合約互動
  - 無網路延遲的本地模擬
