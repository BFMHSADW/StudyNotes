# Rclone 国区 iCloud 支持 — 源码修改与编译指南

## 背景

Rclone 的 iCloud Drive 后端硬编码了全球版域名 `icloud.com`，国区 Apple ID 使用的是 `iCloud.com.cn`（由云上贵州运营），导致认证时收到 HTTP 302 重定向报错：

```
Error: HTTP error 302 (302 Found) returned body: "{\"domainToUse\":\"iCloud.com.cn\"}"
```

**解决方案**：将源码中的 3 个 URL 从 `.com` 改为 `.com.cn`，重新编译。

---

## 环境准备

```bash
# 安装编译依赖
sudo dnf install golang git make unzip

# 验证
go version
git --version
```

---

## 步骤一：克隆源码

```bash
cd ~
git clone https://github.com/rclone/rclone.git
cd rclone
```

## 步骤二：查看需要修改的文件

```bash
grep -rn "icloud\.com" backend/iclouddrive/api/client.go
```

预期输出类似：

```go
backend/iclouddrive/api/client.go:12:	baseEndpoint  = "https://www.icloud.com"
backend/iclouddrive/api/client.go:13:	homeEndpoint  = "https://www.icloud.com"
backend/iclouddrive/api/client.go:14:	setupEndpoint = "https://setup.icloud.com/setup/ws/1"
backend/iclouddrive/api/client.go:15:	authEndpoint  = "https://idmsa.apple.com/appleauth/auth"
```

## 步骤三：修改源码

用 `sed` 一键替换：

```bash
sed -i \
  -e 's|"https://www.icloud.com"|"https://www.icloud.com.cn"|g' \
  -e 's|"https://setup.icloud.com/setup/ws/1"|"https://setup.icloud.com.cn/setup/ws/1"|g' \
  -e 's|"https://idmsa.apple.com/appleauth/auth"|"https://idmsa.apple.com.cn/appleauth/auth"|g' \
  backend/iclouddrive/api/client.go
```

**注意**：`authEndpoint`（idmsa.apple.com）是否需要改取决于实际测试。如果认证阶段报错，可尝试只改前 3 个 URL，保留 `idmsa.apple.com` 不变。

验证修改结果：

```bash
grep -n "icloud\.\|idmsa\." backend/iclouddrive/api/client.go
```

预期输出：

```go
	baseEndpoint  = "https://www.icloud.com.cn"
	homeEndpoint  = "https://www.icloud.com.cn"
	setupEndpoint = "https://setup.icloud.com.cn/setup/ws/1"
	authEndpoint  = "https://idmsa.apple.com.cn/appleauth/auth"
```

## 步骤四：编译

```bash
# 查看当前系统 rclone 版本，编译时保持一致的版本号方便识别
rclone version

# 编译（约 1-3 分钟）
go build -o rclone-cn
```

编译成功后当前目录会生成 `rclone-cn` 二进制文件。

验证：

```bash
./rclone-cn version
```

## 步骤五：配置 iCloud

```bash
./rclone-cn config
```

交互流程：

1. 输入 `n` — 新建 remote
2. 输入名称，如 `icloud`
3. 选择存储类型 — 输入 `iclouddrive`（或在列表中找到对应编号）
4. 输入 Apple ID（你的国区邮箱）
5. 输入密码
6. 输入 2FA 验证码（在 iPhone/Mac 上弹出的验证码，或输入 `sms` 获取短信）
7. 确认其他选项，按 Enter 使用默认值
8. 完成

## 步骤六：测试使用

```bash
# 列出 iCloud 根目录
./rclone-cn ls icloud:

# 列出目录结构
./rclone-cn lsd icloud:

# 下载文件到本地
./rclone-cn copy icloud:Documents ~/iCloud/Documents -P

# 上传文件到 iCloud
./rclone-cn copy ~/local-file.txt icloud:Documents -P

# 挂载为本地目录（需要 fuse）
./rclone-cn mount icloud: ~/mnt/icloud --allow-other --daemon
```

---

## 安装到系统（可选）

如果不想要两个 rclone，可以把编译好的替换系统的：

```bash
# 备份原版
sudo cp /usr/bin/rclone /usr/bin/rclone-official

# 替换
sudo cp ~/rclone/rclone-cn /usr/bin/rclone

# 验证
rclone version
```

---

## 注意事项

1. **Trust Token 有效期 30 天**，过期后需要重新认证：
   ```bash
   ./rclone-cn config
   # 选 edit → 选 icloud → 重新走 2FA 流程
   ```

2. **单文件上传限制**：国区 iCloud 单文件约 25MB，超过会报 413 错误

3. **配置文件位置**：`~/.config/rclone/rclone.conf`，原版和编译版共享同一个配置

4. **后续更新**：如果 rclone 官方合并了国区支持（PR #9399），可以直接用回官方版本

---

## 参考

- GitHub Issue: https://github.com/rclone/rclone/issues/8257
- PR #9399: https://github.com/rclone/rclone/pull/9399
- 源码文件: `backend/iclouddrive/api/client.go`
