# Claw Cloud 部署 Sub-Store 完整教程

## 📋 目录
- [前置条件](#前置条件)
- [注册和登录](#注册和登录)
- [创建应用](#创建应用)
- [基本配置](#基本配置)
- [环境变量配置](#环境变量配置)
- [存储配置](#存储配置)
- [网络配置](#网络配置)
- [部署和访问](#部署和访问)
- [使用方法](#使用方法)
- [故障排除](#故障排除)
- [维护和备份](#维护和备份)

## 🔧 前置条件

### GitHub 账号要求
- 注册超过 **180 天** 的 GitHub 账号
- 可获得 **5 美元/月** 免费额度
- 资源限制：最多 4 vCPU、8GiB 内存、10GiB 硬盘、10GB 流量

### 推荐配置
- 地区选择：避免 Singapore 和 Japan（延迟和稳定性考虑）
- 资源配置：CPU 0.1-0.2 Core，内存 128-256MB 即可满足使用

## 🚀 注册和登录

### 步骤
1. 访问 https://run.claw.cloud
2. 点击 **Sign in with GitHub**
3. 授权 GitHub 账号
4. 选择地区（推荐 US East 或 Europe）
5. 创建容器空间名称
6. 点击 **Start Deploying**

## 📱 创建应用

### 操作步骤
1. 进入控制台后，点击 **App Launchpad**
2. 点击右上角 **Create App** 按钮
3. 开始配置应用参数

## ⚙️ 基本配置

### 必填项目
```yaml
应用名称: sub-store
镜像名称: xream/sub-store
镜像标签: latest (可选，默认为 latest)
```

### 资源配置
```yaml
Replicas: 1
CPU: 0.1 Core (可根据需要调整到 0.2)
Memory: 128M (推荐 256M 以获得更好性能)
Container Port: 3001
```

## 🔧 环境变量配置

### 完整环境变量列表

#### 必须配置的变量
```bash
# 系统路径
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# 时区设置
TIME_ZONE=Asia/Shanghai

# 后端访问路径（安全路径，可自定义）
SUB_STORE_FRONTEND_BACKEND_PATH=/mFCwYT4bu

# 数据存储路径
SUB_STORE_DATA_BASE_PATH=/opt/app/data

# 前端文件路径
SUB_STORE_FRONTEND_PATH=/opt/app/frontend
```

#### 可选配置变量
```bash
# 启用 Docker 模式
SUB_STORE_DOCKER=true

# 自定义端口（如果需要）
SUB_STORE_BACKEND_PORT=3000
SUB_STORE_FRONTEND_PORT=3001

# 日志级别
SUB_STORE_LOG_LEVEL=info

# 数据库类型（默认使用文件存储）
SUB_STORE_DATABASE=file
```

### 安全配置建议
```bash
# 自定义后端路径（提高安全性）
SUB_STORE_FRONTEND_BACKEND_PATH=/your-custom-path-here

# 可选：设置访问密码
SUB_STORE_API_PASSWORD=your-secure-password
```

## 💾 存储配置

### 持久化存储设置
为了防止容器重启后数据丢失，**强烈建议**配置持久化存储：

#### 存储卷配置
```yaml
挂载路径: /opt/app/data
存储大小: 1GB (通常足够)
存储类型: 持久化存储 (Persistent Volume)
访问模式: ReadWriteOnce
```

#### 存储目录结构
```
/opt/app/data/
├── subs/          # 订阅配置文件
├── collections/   # 合并订阅配置
├── artifacts/     # 生成的配置文件
├── scripts/       # 自定义脚本
└── cache/         # 缓存数据
```

## 🌐 网络配置

### 基本网络设置
```yaml
Enable Internet Access: ✅ (必须启用)
Container Port: 3001
Protocol: HTTP
```

### 域名和访问
- 系统会自动分配域名：`your-app-name.run.claw.cloud`
- 访问端口：443 (HTTPS) / 80 (HTTP)
- 内部端口映射：3001 (前端) → 3000 (后端)

## 🚀 部署和访问

### 部署步骤
1. 确认所有配置正确
2. 点击 **Deploy Application**
3. 等待 2-5 分钟直到状态变为 **Running**
4. 查看日志确认启动成功

### 成功部署的标志
日志中应该看到类似信息：
```
[sub-store] INFO: Migration complete!
[sub-store] INFO: [BACKEND] listening on :::3000
[sub-store] INFO: [FRONTEND] :::3001
[sub-store] INFO: [FRONTEND -> BACKEND] :::3001/mFCwYT4bu/api/ -> :::3000/api/
```

### 获取访问地址
1. 在 **My Apps** 中找到 sub-store 应用
2. 查看 **Network** → **Public Address**
3. 复制完整域名，如：`https://sub-store-abc123.run.claw.cloud`

## 🎯 使用方法

### 方式一：直接访问内置前端
```
直接访问: https://your-domain.run.claw.cloud
```

### 方式二：使用官方前端
```
1. 访问: https://sub-store.vercel.app
2. 设置后端: https://your-domain.run.claw.cloud/mFCwYT4bu
```

### 方式三：一键配置链接
```
https://sub-store.vercel.app?api=https://your-domain.run.claw.cloud/mFCwYT4bu
```

### 基本功能使用

#### 1. 添加订阅
- 点击 **订阅** → **+** 添加订阅源
- 输入订阅名称和 URL
- 选择订阅类型（Shadowsocks、V2Ray、Trojan 等）

#### 2. 创建过滤器
- 点击 **过滤器** → **+** 创建新过滤器
- 设置过滤条件（地区、关键词、正则表达式）
- 保存并应用到订阅

#### 3. 合并订阅
- 点击 **合集** → **+** 创建新合集
- 选择要合并的订阅源
- 配置合并规则和过滤器
- 生成新的订阅链接

## 🔧 故障排除

### 常见问题及解决方案

#### 1. 存储卷绑定失败
**现象**: `binding volumes: pod does not exist any more: pod "sub-store-0" not found`
**解决方案**:
- **临时解决**: 先不配置持久化存储，等应用正常运行后再添加
- **检查存储配置**: 确认存储大小在免费额度内（建议1GB以下）
- **重新部署**: 删除应用后重新创建
- **分步配置**: 先部署基础应用，再添加存储卷

#### 2. 部署失败
**现象**: 应用状态显示失败或一直在重启
**解决方案**:
- 检查镜像名称是否正确：`xream/sub-store`
- 确认端口配置：Container Port = 3001
- 检查资源配置是否在免费额度内
- 查看详细日志找出具体错误

#### 2. 无法访问前端
**现象**: 访问域名显示 404 或连接超时
**解决方案**:
- 确认应用状态为 Running
- 检查 Container Port 设置为 3001
- 确认 Enable Internet Access 已启用
- 尝试等待几分钟让服务完全启动

#### 3. 后端连接失败
**现象**: 前端界面显示但无法连接后端
**解决方案**:
- 检查后端路径是否正确：`/mFCwYT4bu`
- 确认 HTTPS 前端访问 HTTPS 后端
- 检查环境变量 `SUB_STORE_FRONTEND_BACKEND_PATH` 配置

#### 4. 数据丢失
**现象**: 重启后配置消失
**解决方案**:
- 配置持久化存储卷
- 挂载路径设置为 `/opt/app/data`
- 定期导出配置进行备份

#### 5. 订阅更新失败
**现象**: 无法获取或更新订阅内容
**解决方案**:
- 确认网络连接正常
- 检查订阅 URL 是否有效
- 查看应用日志中的错误信息
- 确认没有被防火墙或网络策略阻止

### 日志查看方法
1. 进入应用详情页面
2. 点击 **Logs** 查看实时日志
3. 查找 ERROR 或 WARN 级别的信息
4. 根据错误信息进行针对性修复

## 🛡️ 维护和备份

### 定期维护任务

#### 1. 配置备份
**频率**: 每周一次
**步骤**:
1. 访问 Sub-Store 前端界面
2. 进入 **设置** → **数据管理**
3. 点击 **导出配置** 下载备份文件
4. 将备份文件保存到安全位置

#### 2. 资源监控
**监控项目**:
- CPU 使用率（建议 < 80%）
- 内存使用率（建议 < 80%）
- 存储空间使用率（建议 < 70%）
- 网络流量使用情况

#### 3. 安全更新
**注意事项**:
- 定期检查 Sub-Store 版本更新
- 更新时先导出配置备份
- 测试新版本功能是否正常

### 性能优化建议

#### 1. 资源调整
根据实际使用情况调整资源配置：
- **轻度使用**：0.1 Core CPU + 128M 内存
- **中度使用**：0.2 Core CPU + 256M 内存  
- **重度使用**：0.5 Core CPU + 512M 内存

#### 2. 缓存优化
- 定期清理过期缓存
- 合理设置订阅更新频率
- 避免同时处理大量订阅

#### 3. 网络优化
- 使用地理位置较近的服务器
- 优化订阅源的选择
- 定期检查网络连接质量

## 📚 附录

### 有用的链接
- **Sub-Store 官方文档**: https://sub-store.vercel.app
- **Claw Cloud 文档**: https://docs.claw.cloud
- **GitHub 仓库**: https://github.com/sub-store-org/Sub-Store
- **Telegram 群组**: https://t.me/zhetengsha

### 版本信息
- **Sub-Store**: 2.20.16 (截至本教程编写时)
- **Node.js**: v22.19.0
- **支持的协议**: Shadowsocks, V2Ray, Trojan, Hysteria 等

### 常用配置模板

#### 最小化配置
适合轻度使用，节省资源：
```yaml
CPU: 0.1 Core
Memory: 128M
Storage: 1GB
环境变量: 仅必须项
```

#### 推荐配置
适合日常使用，性能均衡：
```yaml
CPU: 0.2 Core
Memory: 256M
Storage: 2GB
环境变量: 完整配置
持久化存储: 启用
```

#### 高性能配置
适合重度使用或多用户：
```yaml
CPU: 0.5 Core
Memory: 512M
Storage: 5GB
环境变量: 完整配置 + 优化项
持久化存储: 启用
监控和日志: 启用
```

---

**最后更新**: 2025年9月4日
**教程版本**: v1.0
**兼容性**: Sub-Store 2.20.16+, Claw Cloud 当前版本
