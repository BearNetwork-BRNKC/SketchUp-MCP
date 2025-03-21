# SketchupMCP - Sketchup 模型上下文協議（MCP）整合

SketchupMCP 透過模型上下文協議（MCP）將 Sketchup 連接到 Claude AI，使 Claude 能夠直接與 Sketchup 互動和控制。這項整合允許使用提示輔助 3D 建模、場景創建和操作 Sketchup。

特別感謝 [Blender MCP](https://github.com/ahujasid/blender-mcp) 提供的靈感與架構。

## 功能

* **雙向通信**：透過 TCP 套接字連接 Claude AI 與 Sketchup
* **組件操作**：在 Sketchup 中創建、修改、刪除和變換組件
* **材質控制**：應用和修改材質與顏色
* **場景檢查**：獲取當前 Sketchup 場景的詳細資訊
* **選取處理**：獲取並操作已選取的組件
* **Ruby 代碼執行**：在 Sketchup 中直接執行任意 Ruby 代碼，以進行高級操作

## 組件

該系統由兩個主要組件組成：

1. **Sketchup 擴展**：在 Sketchup 內部創建 TCP 伺服器來接收並執行命令的擴展
2. **MCP 伺服器（`sketchup_mcp/server.py`）**：實作模型上下文協議並連接到 Sketchup 擴展的 Python 伺服器

## 安裝

### Python 套件安裝

我們使用 `uv` 來管理 Python 環境，因此需要先安裝 `uv`：

```sh
brew install uv
```

### 安裝 Sketchup 擴展

1. 下載或自行構建最新的 `.rbz` 檔案
2. 在 Sketchup 中，前往 **Window > Extension Manager**
3. 點擊 **Install Extension**，然後選擇下載的 `.rbz` 檔案
4. 重新啟動 Sketchup

## 使用方式

### 啟動連線

1. 在 Sketchup 中，前往 **Extensions > SketchupMCP > Start Server**
2. 伺服器將預設啟動在 **9876** 端口
3. 確保 MCP 伺服器已在終端執行

### 與 Claude 配合使用

在 Claude 配置中加入以下內容，以使用 MCP 伺服器：

```json
"mcpServers": {
    "sketchup": {
        "command": "uvx",
        "args": [
            "sketchup-mcp"
        ]
    }
}
```

這將自動從 [PyPI](https://pypi.org/project/sketchup-mcp/) 下載最新版本。

成功連接後，Claude 將能夠透過以下功能與 Sketchup 互動：

#### 工具

* `get_scene_info` - 獲取當前 Sketchup 場景資訊
* `get_selected_components` - 獲取當前選取的組件資訊
* `create_component` - 創建新組件並指定參數
* `delete_component` - 從場景中刪除組件
* `transform_component` - 移動、旋轉或縮放組件
* `set_material` - 為組件應用材質
* `export_scene` - 將當前場景匯出為多種格式
* `eval_ruby` - 在 Sketchup 中執行任意 Ruby 代碼以進行高級操作

### 指令示例

以下是一些可以要求 Claude 執行的操作示例：

* "創建一個帶有屋頂和窗戶的簡單房屋模型"
* "選取所有組件並獲取它們的資訊"
* "將選取的組件變成紅色"
* "將選取的組件向上移動 10 個單位"
* "將當前場景匯出為 3D 模型"
* "使用 Ruby 代碼創建一個複雜的藝術與工藝櫃"

## 疑難排解

* **連線問題**：確保 Sketchup 擴展伺服器和 MCP 伺服器都在運行
* **命令執行失敗**：檢查 Sketchup 的 Ruby 控制台以查看錯誤訊息
* **超時錯誤**：嘗試簡化請求或將操作拆分為較小的步驟

## 技術細節

### 通信協議

該系統使用基於 TCP 套接字的簡單 JSON 協議：

* **命令** 以 JSON 物件的形式發送，包含 `type` 和可選的 `params`
* **回應** 以 JSON 物件的形式返回，包含 `status` 及 `result` 或 `message`

## 貢獻方式

歡迎貢獻！請隨時提交 Pull Request。

## 授權

MIT 授權許可證

