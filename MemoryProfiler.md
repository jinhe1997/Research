# Unity Memory Profiler 研究文件

## 目錄
1. [檢查記憶體使用情況](#檢查記憶體使用情況)
2. [檢查使用最多記憶體的資產](#檢查使用最多記憶體的資產)
3. [總記憶體使用量](#總記憶體使用量)
4. [查找記憶體洩漏](#查找記憶體洩漏)
5. [Managed Shell Objects](#managed-shell-objects)
6. [設備上的記憶體](#設備上的記憶體)

---

## 檢查記憶體使用情況

### 簡介
使用 Memory Profiler 可以獲取 Unity 專案中記憶體使用情況的概覽。目標是通過 Memory Profiler 捕捉記憶體快照，並使用 Tree Map 來探索專案的記憶體分佈。

### 操作步驟
1. 在 Memory Profiler 視窗中，打開要檢查的記憶體快照。預設情況下，快照會在 Tree Map 視圖中打開。
2. 查看 Tree Map 中的不同物件類別。Tree Map 會以視覺化的方式顯示哪些類別佔用最多記憶體，並按大小排序。
3. 點擊 Tree Map 中的類別框，查看該類別中哪些具體物件佔用了記憶體。
4. 點擊類別中的物件以選擇它。選擇物件後，Tree Map 下方會顯示一個表格，其中包含該物件的詳細數據，包括其引用。
5. 從最大的物件開始，逐一檢查物件表，找出可以優化的目標。通常，紋理物件、Shader 變體和預分配緩衝區是記憶體優化的好候選。

**提示**：即使在生產的最早期階段，也應定期檢查記憶體使用情況，以最小化無法在目標設備上運行產品的風險。

---

## 檢查使用最多記憶體的資產

### 簡介
首先識別哪些物件是最佳優化候選，通常這些物件佔用了過多的記憶體。

### 操作步驟
1. 打開快照 - 按照「打開快照」中的說明操作。
2. 打開 Unity Objects 標籤頁。
3. Memory Profiler 視窗預設按降序排序。如果排序順序已更改，請選擇 **Total Size** 列標頭以將排序順序更改回降序，確保佔用最多記憶體的物件位於表格頂部。
4. 可以通過兩種方式搜索結果：
   - 展開群組以顯示群組內的單個物件。
   - 啟用 **Flatten hierarchy** 屬性以僅顯示表格中的單個物件。
5. 如果不確定哪些物件可能佔用過多記憶體，請保持 **Flatten hierarchy** 屬性禁用，並查看群組以找到最大的物件。如果對大多數資產有信心，但懷疑有少量異常值佔用過多記憶體，請啟用該屬性。
6. 啟用 **Show Potential Duplicates Only** 屬性以查看 Memory Profiler 識別為可能重複的物件。可以使用 References 組件和 Selection Details 組件查看這些物件的詳細信息，以確定這些物件是預期的重複（例如場景中應存在的多個 Prefab 實例）還是有問題的重複（例如無意中創建的物件或 Unity 未正確處理的物件）。

---

## 總記憶體使用量

### 簡介
定期檢查應用程式的總記憶體使用量，以盡早識別記憶體使用問題，例如應用程式是否為目標平台使用了過多記憶體。經常捕捉快照，並在多種情況下進行捕捉 - 不要完全依賴於在編輯器中運行應用程式時捕捉的快照。

### 操作步驟
1. 捕捉並打開快照後，可以使用不同的標籤頁以多種方式檢查快照。
2. 使用 **All Of Memory** 標籤頁查看應用程式的總記憶體使用量。如果應用程式需要滿足記憶體使用量的目標大小，這非常有用，因為可以看到應用程式使用的確切總量。

---

## 查找記憶體洩漏

### 簡介
開發人員經常在應用程式中遇到記憶體洩漏。記憶體洩漏會減慢應用程式的速度，並最終導致崩潰。

記憶體洩漏通常由以下兩個問題之一引起：
- 物件未通過代碼手動從記憶體中釋放。
- 物件由於無意的引用而保留在記憶體中。

Memory Profiler 可用於追蹤這些洩漏，無論是在託管記憶體還是原生記憶體中。

### 操作步驟
1. 在特定時間段內捕捉多個記憶體快照。
2. 在 Memory Profiler 視窗中使用 **Diff** 模式比較這些快照。
3. 通過比較快照，找出記憶體洩漏的來源。

---

## Managed Shell Objects

### 簡介
Managed Shell Objects 是 Unity 中託管記憶體的一部分，通常由垃圾回收器管理。這些物件可能包括未釋放的資源或無意中保留的引用。

### 操作步驟
1. 使用 Memory Profiler 捕捉快照。
2. 在 Unity Objects 標籤頁中查看託管物件。
3. 檢查是否有未釋放的物件或無意中保留的引用。

---

## 設備上的記憶體

### 簡介
在分析設備上的記憶體使用情況時，請記住：
- **託管記憶體** 主要是常駐記憶體，因為託管堆和垃圾回收器會定期訪問物件。
- **圖形記憶體（估計）** 值是一個估計值，因為對於大多數平台，Unity 無法獲取圖形資源的確切使用信息。Unity 根據可用信息（如寬度、高度、深度和像素格式）估計大小。
- **未追蹤的記憶體** 是操作系統報告為應用程式分配的所有記憶體，但缺乏有關分配來源的可靠信息。這些記憶體可能歸因於原生插件、操作系統庫或線程堆棧等區域。

### 操作步驟
1. 使用 Memory Profiler 捕捉設備上的記憶體快照。
2. 在 **Summary** 視圖中查看 **Total Resident on Device** 指標，以了解應用程式對物理記憶體的影響。
3. 使用 **All of Memory** 和 **Unity Objects** 視圖進行詳細分析，選擇 **Resident on Device** 或 **Allocated and Resident on Device** 從下拉菜單中，並按 **Resident size** 排序，以獲取對總物理記憶體使用貢獻最大的物件列表。

---

**提示**：定期檢查記憶體使用情況，即使在生產的最早期階段，以最小化無法在目標設備上運行產品的風險。
