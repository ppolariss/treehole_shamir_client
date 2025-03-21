# Treehole_shamir_client

解密用户邮箱的客户端，opentreehole密钥管理员使用。

## 使用步骤

必要：准备 pgp 私钥。

如果使用 gnupg 生成密钥，请从 gnupg 导出私钥文本文件，并放置到程序同一目录下。

```shell
gpg -a -o private.key --export-private-keys <your_uid>
```

如果使用其他工具生成密钥，请自行处理

### 解密 shamir 信息

首先在 authurl 中登录获取 access token，然后使用 token 解密用户信息。

```shell
# 解密全部用户信息，并且上传到后端等待解密。此命令会把处理好的全部用户信息文件存到当前目录下，文件名形如 share_data_xxx.json
shamir_client decrypt -t <your_token> -k <your_private_key_file> -p <your_password> [-a <server_url>]

# 解密单用户的信息
shamir_client decrypt -t <your_token> -k <your_private_key_file> -p <your_password> -u <user_id> [-a <server_url>]

# 如果有已经解密的全部用户信息文件 share_data_xxx.json，可以不用重新解密，直接上传
shamir_client decrypt -t <your_token> [-a <server_url>] -f <share_data_xxx.json>
```

默认 server_url 为 https://auth.fduhole.com

### 生成公私钥文件

仅用于管理员生成新的密钥，与解密的密钥无关

```shell
shamir_client generate <your_name> <your_email> <your_password>
```

### 解密用户邮箱

准备 `shares.json` 文件，放置到程序同一目录下，格式为

```json
[
  "123123123\n456456456",
  "345345345\n678678678"
]
```

执行

```shell
shamir_client email [-f <your_share_file>]
```

### 上传新管理员公钥

准备好7个分开的公钥文件

```shell
shamir_client upload -t <your_token> <file1> <file2> <file3> <file4> <file5> <file6> <file7> [-a <server_url>]
```

### 更新密钥管理员
1. 至少有半数以上个密钥管理员解密全部用户信息 shamir_client decrypt
2. 上传新的密钥管理员公钥 shamir_client upload
3. 更新密钥管理员 POST /shamir/update