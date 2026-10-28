# CA 公钥

本目录存放 **DSH 插件安全公告的根证书公钥**，用于校验签名内容，私钥不公开。

## 文件

| 文件 | 说明 |
|---|---|
| `dsh-plugins-root.pem` | PEM 格式公钥证书（文本） |
| `dsh-plugins-root.cer` | DER 格式公钥证书（二进制） |

## 证书信息

| 字段 | 值 |
|---|---|
| Subject | `CN=dsh-plugins-root, O=hanqi007, OU=DSH Plugin Security, C=CN` |
| Issuer | 自签（同 Subject） |
| 算法 | RSA 2048 / SHA-256 |
| 有效期 | 2026-10-28 → 2031-10-29 |
| SHA-1 指纹 | `663793BDC1BCA2F4B142045068D21261D70FFD2C` |
| 序列号 | `009DA7F6AC6BF9E816` |
| Key Usage | Digital Signature · Key Cert Sign · CRL Sign |
| Basic Constraints | CA = TRUE |

## 用途

- 校验 `security.json` 的安全公告是否被篡改（签名由私钥生成）
- 校验后续发布的安装包摘要

## 校验方法

```bash
openssl x509 -in dsh-plugins-root.pem -noout -fingerprint -sha1 -subject -dates
```

输出应包含 `SHA1 Fingerprint=66:37:93:BD:C1:BC:A2:F4:B1:42:04:50:68:D2:12:61:D7:0F:FF:D2:C`。

## 安全边界

- 私钥仅保存在离线机器上（`dsh-plugins-root.pfx`，口令不入库）
- 公钥证书可以公开分发，泄露不影响签名私钥
- 若私钥泄露，应立即在 `security.json` 发布 `critical` 公告并轮换本目录证书

## 轮换流程

1. 生成新证书并更新本目录
2. 在 `security.json` 加一条公告，说明旧证书作废
3. 用新私钥签名新的 `security.json`
