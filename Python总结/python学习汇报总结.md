# Python 学习进度汇报 —— 正则表达式 / CGI / MySQL / Socket

## 一、四个模块的知识框架总结

### 1. 正则表达式（re 模块）

**核心内容：**
- `re.match()`：从字符串**起始位置**匹配，不匹配则返回 None
- `re.search()`：扫描**整个字符串**，找到第一个匹配位置
- `re.findall()`：返回字符串中**所有**匹配的子串列表
- `re.sub()`：替换字符串中匹配项
- `re.split()`：按匹配项分割字符串
- `group() / groups()`：提取分组捕获内容

**关键概念：**
- 元字符：`. ^ $ * + ? { } [ ] \ | ( )`
- 贪婪匹配 `.*` vs 非贪婪匹配 `.*?`
- 修饰符：`re.I`（忽略大小写）、`re.M`（多行）、`re.S`（点匹配所有字符）

---

### 2. CGI 编程（Common Gateway Interface）

**核心内容：**
- CGI 是 Web 服务器与外部程序交互的**通用网关接口**
- 浏览器 -> HTTP 请求 -> Web 服务器 -> CGI 程序 -> 返回 HTML
- Python CGI 脚本需放在服务器的 `cgi-bin` 目录下
- HTTP 头部格式：`Content-type:text/html` 后必须跟一个**空行**
- 通过 `cgi` 模块获取表单数据：`cgi.FieldStorage()`
- 通过 `os.environ` 获取环境变量（REQUEST_METHOD、QUERY_STRING 等）

---

### 3. MySQL（mysql-connector 驱动）

**核心内容：**
- 官方驱动安装：`pip install mysql-connector`
- 连接数据库：`mysql.connector.connect(host, user, passwd, database)`
- 操作范式：获取 `cursor` -> `execute()` 执行 SQL -> `fetchone()` / `fetchall()` 获取结果
- 数据库操作：CREATE DATABASE、CREATE TABLE、INSERT、SELECT、WHERE、ORDER BY、LIMIT
- **事务处理**：`commit()` 提交 / `rollback()` 回滚
- MySQL 8.0 密码插件变更问题：`caching_sha2_password` -> 需改为 `mysql_native_password`

---

### 4. Socket 网络编程

**核心内容：**
- Socket（套接字）是进程间/主机间通信的端点
- `socket.socket(family, type)`：创建套接字
  - `AF_INET`：IPv4；`AF_UNIX`：本地进程通信
  - `SOCK_STREAM`：TCP（面向连接、可靠）；`SOCK_DGRAM`：UDP（无连接、快速）
- **TCP 服务器端流程**：`socket()` -> `bind()` -> `listen()` -> `accept()` -> `recv()/send()` -> `close()`
- **TCP 客户端流程**：`socket()` -> `connect()` -> `send()/recv()` -> `close()`
- UDP 使用 `sendto()` / `recvfrom()`，无需连接

---

## 二、重难点提炼

| 模块 | 重点 | 难点 |
|------|------|------|
| **正则表达式** | 常用函数的使用场景区分 | 复杂模式的编写与调试；贪婪/非贪婪的理解；分组嵌套与回溯引用 |
| **CGI** | HTTP 头部格式与请求响应流程 | 服务器环境配置（Apache 的 httpd.conf）；环境变量的获取与解析；中文乱码处理 |
| **MySQL** | CRUD 操作与 cursor 范式 | 事务的边界控制；SQL 注入防范；MySQL 8.0 认证插件兼容问题 |
| **Socket** | TCP 三次握手的代码映射（bind/listen/accept/connect） | 阻塞式 I/O 的理解；粘包/拆包问题；多客户端并发处理 |

---

## 三、思考攻克过程（汇报参考）

### 正则表达式
- **初期困惑**：`match()` 和 `search()` 的区别容易混淆，误以为 `match()` 也能在任意位置匹配。
- **攻克方法**：通过对比实验理解——`match` 检查"开头是否符合"，`search` 检查"是否包含"。
- **深入理解**：通过 `.*?` 非贪婪匹配解决"匹配范围过大"问题，体会到量词后加 `?` 的修正作用。

### CGI
- **初期困惑**：脚本在终端能运行，放到浏览器就报错或显示源码。
- **攻克方法**：理解 CGI 的本质——脚本由 Web 服务器调用，而非直接执行；必须输出正确的 HTTP 头部；文件需要有可执行权限；Apache 的 `AddHandler` 要配置 `.py`。

### MySQL
- **初期困惑**：代码执行不报错但数据没写入数据库；或者 MySQL 8.0 连接时提示认证失败。
- **攻克方法**：理解了 `commit()` 的必要性（默认不自动提交）；查文档了解到 8.0 版本密码插件变更，需要修改 `my.ini` 配置或更新驱动。

### Socket
- **初期困惑**：客户端发一次消息程序就结束；服务器只能服务一个客户端。
- **攻克方法**：理解 TCP 是**面向连接**的流式协议，`accept()` 会阻塞等待连接；要实现多客户端并发，需要引入多线程或多进程。

---

## 四、尚未解决的疑虑（可与导师讨论）

### 正则表达式
1. **性能问题**：复杂正则在大文本上的效率如何评估？是否有工具可以可视化正则匹配过程（如 regex101）？
2. **实际应用场景**：在数据清洗/爬虫中提取信息时，正则和 BeautifulSoup/xpath 的取舍标准是什么？

### CGI
1. **现代替代方案**：CGI 每个请求都启动新进程，性能较低。在实际生产环境中，Python Web 开发是否已完全由 WSGI/Flask/Django/FastAPI 取代？CGI 还有学习价值吗？
2. **安全问题**：CGI 程序直接暴露在 Web 上，如何防范命令注入和路径遍历攻击？

### MySQL
1. **ORM 的选择**：直接使用 `mysql-connector` 写原生 SQL 与使用 SQLAlchemy 等 ORM 的利弊权衡？
2. **连接池**：实际项目中是否需要自己管理连接池，还是框架已经封装好了？
3. **SQL 注入**：使用字符串拼接 SQL 的风险已了解，但参数化查询的内部机制还想深入理解。

### Socket
1. **并发模型**：多线程、多进程、`select`/`poll`/`epoll`、asyncio 这几种并发处理方式的演进关系和适用场景？
2. **粘包问题**：TCP 是流协议， send 两次的数据可能被 recv 一次收到，如何设计消息边界（固定长度 / 分隔符 / 长度头）？
3. **实际应用**：Socket 编程与使用 `requests` / `urllib` 等高层库的关系——什么时候需要"造轮子"，什么时候用现成库？

---

## 五、一句话总结

> 这四个模块从**文本处理**（正则）到**Web 交互**（CGI）到**数据持久化**（MySQL）再到**网络底层通信**（Socket），覆盖了 Python 应用开发的多个关键层面。目前已掌握基础 API 的使用和基本流程，但在**性能优化、安全防护、工程化实践**方面还有较大的深入空间，希望能和导师讨论后续的学习重点和实际项目练习方向。
