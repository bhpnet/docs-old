---
order: 2
---

# bhpcli keys

Keys模块用于管理BHP的Tendermint本地密钥库（钱包）。

## 可用命令

| 名称                               | 描述                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| [add](#bhpcli-keys-add)           | 创建新密钥、从助记词导入已有密钥，或从备份的keystore导入秘钥 |
| [list](#bhpcli-keys-list)         | 列出所有密钥                                                 |
| [show](#bhpcli-keys-show)         | 根据给定的名称显示密钥信息                                   |
| [export](#bhpcli-keys-export)     | 将密钥导出为json文件                                         |
| [delete](#bhpcli-keys-delete)     | 删除指定的密钥                                               |
| [update](#bhpcli-keys-update)     | 更改用于保护私钥的密码                                       |
| [mnemonic](#bhpcli-keys-mnemonic) | 通过读取系统熵来创建bip39助记词，也可以称为种子短语          |
| [new](#bhpcli-keys-new)           | 使用交互式命令派生新的私钥，该命令将对你的每个输入作出提示   |

## bhpcli keys add

创建一个新的密钥（钱包），或通过助记词/密钥库导入已有密钥。

```bash
bhpcli keys add <key-name> <flags>
```
## bhpcli keys list

返回此密钥管理器存储的所有密钥的名称、类型、地址和公钥列表。

## bhpcli keys show

查询本地密钥的详细信息。