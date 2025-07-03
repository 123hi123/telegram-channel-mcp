# 🔐 MCP服務器安全功能指南

## 概述

您的Telegram頻道MCP服務器現在包含密鑰驗證功能，提供額外的安全保護。

## 當前配置

- **服務器地址**: `https://telegram-channel-mcp.joegodwanggod.workers.dev`
- **訪問密鑰**: `YOUR_ACCESS_KEY_HERE`

## 認證方式

### 1. Authorization Header（推薦）

```bash
curl -H "Authorization: Bearer YOUR_ACCESS_KEY_HERE" \
     "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse"
```

### 2. 自定義Header

```bash
curl -H "X-MCP-Key: YOUR_ACCESS_KEY_HERE" \
     "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse"
```

### 3. URL參數

```bash
curl "https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=YOUR_ACCESS_KEY_HERE"
```

## Claude Desktop集成

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

## MCP Inspector測試

1. 啟動Inspector：
   ```bash
   npx @modelcontextprotocol/inspector@latest
   ```

2. 連接地址：
   ```
   https://telegram-channel-mcp.joegodwanggod.workers.dev/sse?key=YOUR_ACCESS_KEY_HERE
   ```

## 端點權限

| 端點 | 需要密鑰 | 說明 |
|------|----------|------|
| `/health` | ❌ | 健康檢查，公開訪問 |
| `/sse` | ✅ | MCP SSE端點 |
| `/mcp` | ✅ | MCP JSON-RPC端點 |

## 錯誤處理

### 未提供密鑰
```json
{
  "error": "Unauthorized",
  "message": "Valid access key required...",
  "examples": { ... }
}
```

### 密鑰錯誤
返回HTTP 401狀態碼

## 更改密鑰

如需更改訂問密鑰：

```bash
echo "new-password-456" | npx wrangler secret put MCP_ACCESS_KEY
npx wrangler deploy
```

## 移除密鑰保護

如需完全移除密鑰保護：

```bash
npx wrangler secret delete MCP_ACCESS_KEY
npx wrangler deploy
```

---

🔒 **保持您的密鑰安全，不要在公開場所分享！** 