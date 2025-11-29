# Ansible Vault 示例项目

一个完整的 Ansible Vault 使用示例，展示如何安全地管理和使用敏感数据。

## 📖 项目简介

这个项目演示了如何使用 Ansible Vault 来加密和保护敏感信息，如密码、API 密钥等。通过实际的 Playbook 示例，展示最佳实践和常见使用场景。

## 🗂️ 项目结构

```
ansible-vault-example/
├── ansible.cfg                    # Ansible 配置文件
├── inventory                      # 主机清单文件
├── site.yml                       # 主 Playbook
├── test-vars.yml                  # 变量测试 Playbook
├── templates/                     # 模板文件目录
│   └── database.conf.j2          # 数据库配置模板
├── group_vars/                    # 组变量目录
│   ├── all.yml                   # 非敏感变量（明文）
│   └── secrets.yml               # 敏感变量（加密）
├── roles/                         # 角色目录
│   └── database/
│       └── tasks/
│           └── main.yml          # 数据库角色任务
└── vault-password-file           # Vault 密码文件（不提交到 Git）
```

## 🚀 快速开始

### 前提条件

- Ansible 2.9+
- Python 3.6+

### 初始设置

1. **创建加密的敏感变量文件**
   ```bash
   ansible-vault create group_vars/secrets.yml
   ```
   
   在编辑器中输入：
   ```yaml
   ---
   postgres_password: "Pg!8xL2#9mQ$vR1@"
   harbor_db_password: "Hb#5yT9@2nM$wR7!"
   api_key: "sk_1234567890abcdef"
   secret_token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
   ```

2. **运行示例 Playbook**
   ```bash
   ansible-playbook site.yml --ask-vault-pass
   ```

## 🔐 Ansible Vault 使用指南

### 基本命令

| 命令 | 用途 | 示例 |
|------|------|------|
| `ansible-vault create` | 创建加密文件 | `ansible-vault create secrets.yml` |
| `ansible-vault edit` | 编辑加密文件 | `ansible-vault edit secrets.yml` |
| `ansible-vault view` | 查看加密文件 | `ansible-vault view secrets.yml` |
| `ansible-vault encrypt` | 加密现有文件 | `ansible-vault encrypt file.yml` |
| `ansible-vault decrypt` | 解密文件 | `ansible-vault decrypt secrets.yml` |
| `ansible-vault rekey` | 修改加密密码 | `ansible-vault rekey secrets.yml` |

### 运行 Playbook 的方式

#### 方法 1：交互式输入密码
```bash
ansible-playbook site.yml --ask-vault-pass
```

#### 方法 2：使用密码文件
```bash
ansible-playbook site.yml --vault-password-file vault-password-file
```

#### 方法 3：设置环境变量
```bash
export ANSIBLE_VAULT_PASSWORD_FILE=./vault-password-file
ansible-playbook site.yml
```

## 📋 文件说明

### 配置文件

**`ansible.cfg`**
```ini
[defaults]
inventory = inventory
host_key_checking = False
roles_path = roles

[privilege_escalation]
become = True
```

**`inventory`**
```ini
[webservers]
localhost ansible_connection=local
```

### 变量文件

**`group_vars/all.yml`** (非敏感配置)
```yaml
---
# 非敏感配置 - 可以提交到版本控制
postgres_image: "postgres:13-alpine"
postgres_container_name: "postgres-cicd"
postgres_data_dir: "/opt/cicd/postgres_data"
postgres_port: 5432
postgres_db: "sonarqube"
postgres_user: "sonar"
harbor_db: "harbor"
harbor_db_user: "harbor"
```

**`group_vars/secrets.yml`** (加密的敏感配置)
```yaml
---
# 敏感数据 - 加密存储
postgres_password: "Pg!8xL2#9mQ$vR1@"
harbor_db_password: "Hb#5yT9@2nM$wR7!"
api_key: "sk_1234567890abcdef"
secret_token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
```

### Playbook 文件

**`site.yml`** - 主 Playbook，展示如何使用加密变量

**`test-vars.yml`** - 测试变量加载的 Playbook

### 模板文件

**`templates/database.conf.j2`** - 数据库配置文件模板，使用加密变量

### 角色

**`roles/database/tasks/main.yml`** - 数据库角色的任务文件

## 🧪 测试和验证

### 测试变量加载
```bash
ansible-playbook test-vars.yml --ask-vault-pass
```

### 验证完整功能
```bash
ansible-playbook site.yml --ask-vault-pass
```

## 🛡️ 安全最佳实践

### 1. 密码管理
- 使用强密码（16位以上，包含特殊字符）
- 定期轮换密码
- 不同环境使用不同密码

### 2. 文件安全
```bash
# 设置严格的文件权限
chmod 600 vault-password-file
chmod 600 group_vars/secrets.yml
```

### 3. Git 忽略配置
创建 `.gitignore` 文件：
```
# Vault 相关文件
vault-password-file
*.vault
vault_pass.txt

# 敏感文件
secrets/
.env

# 临时文件
*.retry
*.swp
```

### 4. 环境分离
为不同环境创建不同的加密文件：
```bash
inventories/
├── production/
│   └── group_vars/
│       └── secrets.yml    # 生产环境加密变量
└── staging/
    └── group_vars/
        └── secrets.yml    # 测试环境加密变量
```

## 🔧 故障排除

### 常见问题

**问题 1：变量未定义错误**
```
"msg": "The task includes an option with an undefined variable"
```
**解决方案**：确保加密文件正确加载，检查文件路径和 Vault 密码

**问题 2：模板文件未找到**
```
"msg": "Could not find or access 'template.j2'"
```
**解决方案**：确保模板文件存在于 `templates/` 目录中

**问题 3：Vault 密码错误**
```
"msg": "Decryption failed"
```
**解决方案**：检查 Vault 密码是否正确，或使用 `ansible-vault rekey` 重置密码

### 调试技巧

1. **查看变量加载情况**
   ```bash
   ansible-playbook test-vars.yml --ask-vault-pass -v
   ```

2. **检查加密文件内容**
   ```bash
   ansible-vault view group_vars/secrets.yml --ask-vault-pass
   ```

3. **验证文件格式**
   ```bash
   ansible-vault view group_vars/secrets.yml --ask-vault-pass | head -5
   ```

## 📝 使用场景

### 场景 1：保护数据库密码
```yaml
# group_vars/secrets.yml (加密)
postgres_password: "StrongPassword123!"
mysql_password: "AnotherStrongPassword456!"
```

### 场景 2：保护 API 密钥
```yaml
# group_vars/secrets.yml (加密)
github_token: "ghp_xxxxxxxxxxxxxxxxxxxx"
aws_access_key: "AKIAxxxxxxxxxxxxxxxx"
aws_secret_key: "wJalrXUtxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

### 场景 3：保护 SSL 证书
```yaml
# group_vars/secrets.yml (加密)
ssl_cert: |
  -----BEGIN CERTIFICATE-----
  MIIE... (证书内容)
  -----END CERTIFICATE-----
ssl_key: |
  -----BEGIN PRIVATE KEY-----
  MIIE... (私钥内容)
  -----END PRIVATE KEY-----
```

## 🤝 贡献指南

1. Fork 本项目
2. 创建功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📄 许可证

本项目采用 MIT 许可证。

## 🙏 致谢

- 感谢 Ansible 社区提供的优秀文档
- 感谢所有贡献者和用户的支持

---

**安全自动化，从保护敏感数据开始！** 🔒
