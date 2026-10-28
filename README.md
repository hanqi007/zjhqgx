# zjhqgx — DSH 插件安全公告

本仓库是三个 DSH 插件的**安全公告与最低安全版本**的唯一来源：

| 插件 | 说明 | 当前安全版本 |
|---|---|---|
| `hdd-console-boot` | HDD 控制台开机动画 | 1.2.0 |
| `dsh-cjgl` | 启动与插件管理器 | 3.1.0 |
| `dsh-gd` | 星布谷地启动动画 | 2.1.0 |

## 谁在读它

三个插件的「自动检查更新」都只读本仓库根目录的 [`security.json`](./security.json)，
不再读各自仓库的 `package.json`。这样安全公告只有一处来源，改一次三个插件都生效。

## 格式

```jsonc
{
  "schema": 1,
  "updated": "2026-10-28",
  "plugins": {
    "<包名>": {
      "latest": "1.2.0",      // 最新版本
      "minSafe": "1.2.0",     // 低于它就属于安全风险
      "dist": "…",            // 安装包所在仓库
      "note": "…"
    }
  },
  "advisories": [
    {
      "id": "ZJHQGX-2026-001",
      "plugin": "dsh-cjgl",
      "severity": "high",     // low | medium | high | critical
      "summary": "…",
      "affected": "<3.0.2",   // 受影响区间
      "fixed": "3.0.2"        // 修复版本
    }
  ]
}
```

插件侧逻辑：

- `hasUpdate` = `latest > current`
- `hasSecurityFix` = `minSafe > current`
- `advisory` = `advisories` 里该插件、`fixed > current` 且 `affected` 命中当前版本的第一条

## 维护

发新版 → 改 `plugins.<包名>.latest` 与 `minSafe`，必要时补一条 `advisories`，提交即可。
所有插件在下一次检查时（最多 24 小时一次）读到新值。

## License

MIT
