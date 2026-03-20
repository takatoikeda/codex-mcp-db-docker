# codex-mcp-db-docker
# Codex + MCP + Docker + MySQL をローカルで動かした

## 概要
CodexからMCPサーバー経由でMySQLを操作する環境をDockerで構築した。

## 構成
Codex → MCP Server → MySQL(Docker)

## 環境
- Windows11 + WSL2
- Node.js
- Docker
- MySQL

## 手順

### 1. MySQLをDockerで起動
```bash
docker run -d \
  --name mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  -p 3306:3306 \
  mysql:8
