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
