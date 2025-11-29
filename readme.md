# Ansible Vault 示例

一个简单的 Ansible Vault 使用示例，展示如何加密敏感数据。

## 项目结构

```
.
├── ansible.cfg
├── group_vars/
│   ├── all.yml           # 非敏感变量
│   └── secrets.yml       # 加密的敏感变量
├── inventory
├── site.yml              # 主 Playbook
└── templates/
    └── database.conf.j2  # 配置模板
```

## 快速开始

1. **创建加密文件**
   ```bash
   ansible-vault create group_vars/secrets.yml
   ```

2. **输入敏感数据**
   ```yaml
   ---
   postgres_password: "你的密码"
   harbor_db_password: "你的密码" 
   api_key: "你的API密钥"
   ```

3. **运行 Playbook**
   ```bash
   ansible-playbook site.yml --ask-vault-pass
   ```

## 基本用法

### 常用命令

- 创建加密文件：`ansible-vault create file.yml`
- 编辑加密文件：`ansible-vault edit file.yml`  
- 查看加密文件：`ansible-vault view file.yml`
- 运行 Playbook：`ansible-playbook site.yml --ask-vault-pass`

### 运行方式

```bash
# 交互式输入密码
ansible-playbook site.yml --ask-vault-pass

# 使用密码文件
echo "your_password" > vault-pass-file
ansible-playbook site.yml --vault-password-file vault-pass-file
```

## 安全建议

1. 使用强密码加密文件
2. 不要提交加密密码到 Git
3. 为不同环境使用不同加密文件
4. 定期轮换密码

## 文件说明

- `group_vars/all.yml` - 非敏感配置（可提交到 Git）
- `group_vars/secrets.yml` - 敏感配置（加密，不提交到 Git）
- `site.yml` - 演示如何使用加密变量

---
**保护你的敏感数据，从使用 Vault 开始** 🔐
