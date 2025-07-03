# Telegram 頻道 MCP 服務器部署指南

## 🎉 恭喜！您的Worker已經成功部署

您的Telegram頻道MCP服務器已經部署到：
**https://telegram-channel-mcp.joegodwanggod.workers.dev**

## 📋 接下來需要完成的步驟

### 1. 創建Telegram Bot

1. 在Telegram中找到 `@BotFather`
2. 發送 `/newbot` 創建新Bot
3. 按照指示設置Bot名稱和用戶名
4. 保存BotFather給您的Token（格式類似：`123456789:ABCdef123...`）

### 2. 獲取頻道ID

1. 創建或選擇您要發送消息的Telegram頻道
2. 將您的Bot添加為頻道管理員
3. 向頻道發送一條測試消息
4. 訪問以下URL獲取頻道ID：
   ```
   https://api.telegram.org/bot<BOT_TOKEN>/getUpdates
   ```
   將 `<BOT_TOKEN>` 替換為您的實際Token
5. 在返回的JSON中查找頻道ID（通常是負數，格式如：`-1001234567890`）

### 3. 設置Production環境變量

執行以下命令來設置production secrets：

```bash
# 設置Bot Token
npx wrangler secret put BOT_TOKEN
# 輸入您的實際Bot Token

# 設置頻道ID  
npx wrangler secret put CHANNEL_ID
# 輸入您的頻道ID（負數）

# 設置MCP訪問密鑰（可選，但建議設置以增加安全性）
npx wrangler secret put MCP_ACCESS_KEY
# 輸入您的自定義密鑰（例如：my-secure-password-123）
```

### 4. 測試部署

設置完成後，您可以通過以下方式測試：

1. **MCP端點**：`https://telegram-channel-mcp.joegodwanggod.workers.dev/sse`
2. **標準MCP協議端點**：`https://telegram-channel-mcp.joegodwanggod.workers.dev/mcp`

### 5. 與AI助手集成

#### Claude Desktop配置

在您的Claude Desktop配置文件中添加：

```json
{
  "mcpServers": {
    "telegram-channel": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse"
      ]
    }
  }
}
```

#### 其他MCP客戶端

使用MCP檢查器測試：
```bash
npx @modelcontextprotocol/inspector@latest
```

然後連接到：`https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=your-access-key`

## 🔐 安全配置

### MCP訪問密鑰

為了增加安全性，建議設置MCP_ACCESS_KEY環境變量。如果設置了此密鑰，所有MCP端點將需要驗證。

**支持的驗證方式：**

1. **Authorization Header（推薦）**：
   ```
   Authorization: Bearer your-access-key
   ```

2. **自定義Header**：
   ```
   X-MCP-Key: your-access-key
   ```

3. **URL參數**：
   ```
   https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=your-access-key
   ```

**未授權訪問響應：**
```json
{
  "error": "Unauthorized",
  "message": "Valid access key required. Provide key via Authorization header, X-MCP-Key header, or ?key= parameter.",
  "examples": {
    "Authorization header": "Bearer your-key-here",
    "Custom header": "X-MCP-Key: your-key-here",
    "URL parameter": "?key=your-key-here"
  }
}
```

## 🛠️ 可用工具

### `send-sms`

向配置的Telegram頻道發送消息

**參數：**
- `message` (string): 要發送的消息內容
- `parse_mode` (可選): 消息解析模式
  - `"none"` - 純文本（預設）
  - `"Markdown"` - Markdown格式
  - `"MarkdownV2"` - MarkdownV2格式（自動轉義特殊字符）
  - `"HTML"` - HTML格式

**示例：**
```javascript
// 發送純文本消息
{
  "message": "Hello from MCP server!"
}

// 發送Markdown格式消息
{
  "message": "**粗體文字** _斜體文字_",
  "parse_mode": "Markdown"
}
```

## 🔧 本地開發

如果您想在本地開發和測試：

```bash
# 編輯 .dev.vars 文件，設置您的Bot Token和頻道ID
# 然後啟動本地開發服務器
pnpm run dev

# 本地MCP端點將在以下地址可用：
# http://localhost:8787/sse
```

## 📝 環境變量說明

- `BOT_TOKEN`: 從@BotFather獲取的Bot令牌
- `CHANNEL_ID`: 目標Telegram頻道的ID（負數，以-100開頭）

## 🚀 部署更新

如果您修改了代碼，可以重新部署：

```bash
pnpm run deploy
```

## 📞 支持

如果遇到問題：
1. 檢查Bot是否已添加到頻道並具有管理員權限
2. 確認頻道ID格式正確（負數）
3. 驗證Bot Token有效性
4. 查看Worker日誌：`npx wrangler tail`

---

�� 您的MCP服務器現在已準備就緒！ 