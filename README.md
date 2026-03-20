# Codex + MCP + Docker + MySQLをローカルで動かした

## 概要
WSL上のUbuntuでDockerとMySQLを起動し、MCPサーバーを構築、Windows11上のCodexから自然言語でDB操作を行った。

## 構成
Codex → MCP Server → MySQL(Docker)

## 環境
- Windows11 + WSL2
- Node.js
- Docker
- MySQL

## 手順

### Dockerのインストール
必要なパッケージの追加
sudo apt install -y ca-certificates curl gnupg

GPGキー追加
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

リポジトリ追加
echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

インストール
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

ユーザー権限でDockerを起動させるため以下設定を実施
sudo usermod -aG docker $USER

MySQLをDockerで起動
docker run -d \
  --name mysql-test \
  -e MYSQL_ROOT_PASSWORD=(rootパスワード) \
  -p 3306:3306 \
  mysql:8

