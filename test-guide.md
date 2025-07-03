# 🧪 MCP服務器測試指南

## 📋 當前配置信息

- **服務器地址**: `https://telegram-channel-mcp.joegodwanggod.workers.dev`
- **MCP SSE端點**: `https://telegram-channel-mcp.joegodwanggod.workers.dev/sse`
- **MCP JSON-RPC端點**: `https://telegram-channel-mcp.joegodwanggod.workers.dev/mcp`
- **訪問密鑰**: `YOUR_ACCESS_KEY_HERE`
- **Bot用戶名**: @tgmcppppppppbot
- **目標聊天**: 用戶Joe (@Comalot) - ID: 6828979094

## 🎯 Claude Desktop配置

### 方法1: 複製配置文件
直接複製 `claude-mcp-config.json` 的內容到您的Claude Desktop配置文件中。

### 方法2: 手動配置
在Claude Desktop的配置文件中添加：

```json
{
  "mcpServers": {
    "telegram-channel": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=YOUR_ACCESS_KEY_HERE"
      ]
    }
  }
}
```

## 🔍 MCP Inspector測試

### 啟動Inspector
```bash
npx @modelcontextprotocol/inspector@latest
```

### 連接地址
在Inspector中輸入以下任一地址：

1. **帶密鑰URL（推薦）**:
   ```
   https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=YOUR_ACCESS_KEY_HERE
   ```

2. **無密鑰URL**（需要在Inspector中手動添加認證header）:
   ```
   https://telegram-channel-mcp.joegodwanggod.workers.dev/sse
   ```
   然後添加header: `Authorization: Bearer YOUR_ACCESS_KEY_HERE`

## 🛠️ 可用工具測試

### `send-sms` 工具

**基本參數**:
```json
{
  "message": "Hello from MCP test!"
}
```

**帶格式的消息**:
```json
{
  "message": "**粗體文字** _斜體文字_ `代碼`",
  "parse_mode": "Markdown"
}
```

**自動轉義MarkdownV2**:
```json
{
  "message": "測試特殊字符: . ! - = + ( ) [ ]",
  "parse_mode": "MarkdownV2"
}
```

**HTML格式**:
```json
{
  "message": "<b>粗體</b> <i>斜體</i> <code>代碼</code>",
  "parse_mode": "HTML"
}
```

## 🌐 直接API測試

### 健康檢查（無需密鑰）
```bash
curl "https://telegram-channel-mcp.joegodwanggod.workers.dev/health"
```

### MCP初始化（需要密鑰）
```bash
curl -X POST "https://telegram-channel-mcp.joegodwanggod.workers.dev/mcp" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_ACCESS_KEY_HERE" \
  -d '{
    "jsonrpc": "2.0",
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "capabilities": {},
      "clientInfo": {
        "name": "test-client",
        "version": "1.0.0"
      }
    },
    "id": 1
  }'
```

### 獲取工具列表
```bash
curl -X POST "https://telegram-channel-mcp.joegodwanggod.workers.dev/mcp" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_ACCESS_KEY_HERE" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/list",
    "params": {},
    "id": 2
  }'
```

### 調用發送消息工具
```bash
curl -X POST "https://telegram-channel-mcp.joegodwanggod.workers.dev/mcp" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_ACCESS_KEY_HERE" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
      "name": "send-sms",
      "arguments": {
        "message": "🧪 這是來自MCP工具的測試消息！"
      }
    },
    "id": 3
  }'
```

## 🚫 錯誤測試

### 測試無密鑰訪問
```bash
curl "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse"
```
預期結果: HTTP 401 + 錯誤信息

### 測試錯誤密鑰
```bash
curl -H "Authorization: Bearer wrong-key" \
     "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse"
```
預期結果: HTTP 401

## 📱 Telegram驗證

測試成功後，您應該會在Telegram中收到消息（發送給用戶Joe）。

---

💡 **提示**: 如果遇到問題，請檢查密鑰是否正確，或查看Worker日誌：`npx wrangler tail` 