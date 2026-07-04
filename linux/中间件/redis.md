# Redis

# 安装

1.

```bash
sudo apt update
sudo apt install redis-server -y
```

## 📦 第一步：安装 Redis

### 1. 安装 Redis

bash



复制

```
sudo apt update
sudo apt install redis-server -y
```



### 2. 检查 Redis 状态

bash

```
sudo systemctl status redis
```



如果看到 `active (running)` 说明安装成功。

### 3. 设置 Redis 开机自启

bash

```
sudo systemctl enable redis
```



### 4. 测试 Redis 是否正常工作

bash

```
# 连接 Redis
redis-cli

# 在 Redis 命令行中测试
ping
# 应该返回：PONG

# 设置一个键值
set test "hello"

# 获取值
get test
# 应该返回：hello

# 退出
exit
```



------

## 🔧 第二步：配置 Redis（可选但推荐）

### 1. 修改 Redis 配置文件

bash

```
sudo nano /etc/redis/redis.conf
```



### 2. 常见配置调整

| 配置项             | 默认值          | 推荐值        | 说明                         |
| :----------------- | :-------------- | :------------ | :--------------------------- |
| `bind`             | `127.0.0.1 ::1` | `127.0.0.1`   | 只允许本地访问（安全）       |
| `port`             | `6379`          | `6379`        | 默认端口，通常不改           |
| `requirepass`      | (注释状态)      | `你的强密码`  | 设置访问密码（生产环境必须） |
| `maxmemory`        | (无)            | `512mb`       | 最大内存限制                 |
| `maxmemory-policy` | `noeviction`    | `allkeys-lru` | 内存淘汰策略                 |

**如果需要设置密码**（推荐生产环境）：

bash

```
# 在配置文件中找到 # requirepass foobared
# 取消注释并修改为：
requirepass your_strong_password_here
```



### 3. 重启 Redis 使配置生效

bash

```
sudo systemctl restart redis
```



### 4. 如果设置了密码，测试连接

bash

```
redis-cli -a your_strong_password_here ping
```