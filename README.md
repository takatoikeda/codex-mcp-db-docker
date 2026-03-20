# Codexから自然言語でMySQLを操作する

## 概要
WSL上のUbuntuでDocker、MySQLを起動し、MCPサーバーを構築、Windows11上のCodexから自然言語でDB操作を行う。

## 構成
Codex → MCP Server → MySQL(Docker)

## 環境
- Windows11 + WSL2
- Node.js
- Docker
- MySQL

## 手順

### Dockerのインストール

### 必要なパッケージの追加
```bash
sudo apt install -y ca-certificates curl gnupg
```

### GPGキー追加
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### リポジトリ追加
```bash
echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### インストール
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### ユーザー権限でDockerを起動させるため以下設定を実施
```bash
sudo usermod -aG docker $USER
```

### MySQLをDockerで起動
```bash
docker run -d \
  --name mysql-test \
  -e MYSQL_ROOT_PASSWORD=(rootパスワード) \
  -p 3306:3306 \
  mysql:8
Unable to find image 'mysql:8' locally
8: Pulling from library/mysql
91c980086743: Pull complete
68420c358694: Pull complete
eba8eed90d75: Pull complete
43105b2d4e4a: Pull complete
d4c7048d1cf1: Pull complete
03f8ae82fc56: Pull complete
3cd28adefbd9: Pull complete
50200b0c89a3: Pull complete
1a7d4892b7d2: Pull complete
4d14d7bf02a4: Pull complete
09ed159fbec4: Download complete
0ad5e3bd9c41: Download complete
Digest: sha256:da906917ca4ace3ba55538b7c2ee97a9bc865ef14a4b6920b021f0249d603f3d
Status: Downloaded newer image for mysql:8
0011e079bb87a693e0226e31c5127d6e539aa0b73fa4c845cc2a7fad39f64a88
```

### 起動確認
```bash
docker logs -f mysql-test
```

### 接続テスト
```bash
docker exec -it mysql-test mysql -uroot -p
```

### WindowsからMySQLに接続
```bash
PS C:\Users\user1\Downloads\mysql-9.6.0-winx64\bin> .\mysql.exe -h 127.0.0.1 -uroot -p
Enter password: ********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 23
Server version: 8.4.8 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> showdatabases;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'showdatabases' at line 1
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.016 sec)
```

### Dockerのプロセスを確認
```bash
docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                    NAMES
0011e079bb87   mysql:8   "docker-entrypoint.s…"   34 minutes ago   Up 34 minutes   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp, 33060/tcp   mysql-test
```

### MySQLの起動ログを確認
```bash
docker logs mysql-test
2026-03-20 10:18:14+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.8-1.el9 started.
2026-03-20 10:18:14+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2026-03-20 10:18:14+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.8-1.el9 started.
2026-03-20 10:18:15+00:00 [Note] [Entrypoint]: Initializing database files
2026-03-20T10:18:15.053869Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2026-03-20T10:18:15.055100Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.4.8) initializing of server in progress as process 80
2026-03-20T10:18:15.064717Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-03-20T10:18:15.367925Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-03-20T10:18:16.441024Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2026-03-20T10:18:18.202153Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.
2026-03-20 10:18:18+00:00 [Note] [Entrypoint]: Database files initialized
2026-03-20 10:18:18+00:00 [Note] [Entrypoint]: Starting temporary server
2026-03-20T10:18:18.276393Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-03-20T10:18:18.626690Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.8) starting as process 125
2026-03-20T10:18:18.647321Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-03-20T10:18:18.974919Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-03-20T10:18:19.279232Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-03-20T10:18:19.279276Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-03-20T10:18:19.282142Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-03-20T10:18:19.307782Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2026-03-20T10:18:19.307929Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.8'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
2026-03-20 10:18:19+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leap-seconds.list' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
2026-03-20 10:18:21+00:00 [Note] [Entrypoint]: Creating database testdb

2026-03-20 10:18:21+00:00 [Note] [Entrypoint]: Stopping temporary server
2026-03-20T10:18:21.344812Z 11 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.4.8).
2026-03-20T10:18:22.632557Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.4.8)  MySQL Community Server - GPL.
2026-03-20T10:18:22.632586Z 0 [System] [MY-015016] [Server] MySQL Server - end.
2026-03-20 10:18:23+00:00 [Note] [Entrypoint]: Temporary server stopped

2026-03-20 10:18:23+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2026-03-20T10:18:23.367024Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-03-20T10:18:23.700602Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.8) starting as process 1
2026-03-20T10:18:23.710490Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-03-20T10:18:24.047924Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-03-20T10:18:24.294938Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-03-20T10:18:24.294983Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-03-20T10:18:24.298426Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-03-20T10:18:24.322864Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2026-03-20T10:18:24.322981Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.8'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
```

### DockerからMySQLに接続
```bash
docker exec -it mysql-test mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.4.8 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.01 sec)
```

### node.js、npmをインストール
```bash
sudo apt install -y nodejs npm
```

### 確認
```bash
node -v
npm -v
```

### パッケージインストール
```bash
npm install mysql2 @modelcontextprotocol/sdk
```

### MCPファイルの作成
```bash
vi mysql-mcp.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";

import mysql from "mysql2/promise";

// MySQL接続
const connection = await mysql.createConnection({
  host: "127.0.0.1",
  port: 3306,
  user: "root",
  password: "rootpass",
  database: "testdb",
});

// サーバー
const server = new Server(
  {
    name: "mysql-mcp",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// ツール一覧
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "query",
        description: "Execute SQL query",
        inputSchema: {
          type: "object",
          properties: {
            sql: { type: "string" },
          },
          required: ["sql"],
        },
      },
    ],
  };
});

// ツール実行
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "query") {
    const sql = request.params.arguments.sql;

    const [rows] = await connection.execute(sql);

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(rows, null, 2),
        },
      ],
    };
  }

  throw new Error("Unknown tool");
});

// 起動
const transport = new StdioServerTransport();
await server.connect(transport);
```

### MCP Serverの実行
```bash
node mysql-mcp.js
```

## Codexの設定

### 左下「設定」をクリックしポップアップの「設定」をクリック
<img width="1315" height="948" alt="image" src="https://github.com/user-attachments/assets/7e5559cb-ad44-4c85-837d-7cf72dff7b63" />

### MCPサーバーをクリックし「+サーバーを追加する」をクリック
<img width="1315" height="948" alt="image" src="https://github.com/user-attachments/assets/9565c2bf-85a2-4bfa-a9e9-8884b6510916" />

### 名前、起動用コマンド、引数を入力し「保存する」をクリック
<img width="1315" height="959" alt="image" src="https://github.com/user-attachments/assets/a574c829-94e6-4d73-ab86-78ab6fcf772b" />

## 自然言語でのMySQL操作

### スレッドを作成し入力欄に自然言語で指示を入力
<img width="1315" height="959" alt="image" src="https://github.com/user-attachments/assets/1bd62f77-eb89-4c9a-bd5e-d2053b7810b8" />

### レコードの追加
<img width="1315" height="959" alt="image" src="https://github.com/user-attachments/assets/f19d6758-ecec-4b75-a0fb-aaa825ac284f" />

### レコードが全件取得できたか確認
<img width="1315" height="959" alt="image" src="https://github.com/user-attachments/assets/f80d4787-1207-462a-b71e-dfab43ea1111" />

### MySQLクライアントでデータを確認
```bash
PS C:\Users\user1\mysql-9.6.0-winx64\bin> .\mysql.exe -h 127.0.0.1 -uroot -p
Enter password: ********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 23
Server version: 8.4.8 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.016 sec)

mysql> use testdb
Database changed
mysql> show tables;
+------------------+
| Tables_in_testdb |
+------------------+
| users            |
+------------------+
1 row in set (0.017 sec)

mysql> select * from users;
+----+--------+
| id | name   |
+----+--------+
|  1 | taro   |
|  2 | yamada |
+----+--------+
2 rows in set (0.009 sec)
```


