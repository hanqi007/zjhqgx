# zjhqgx — DSH 插件紧急安全公告

**只在出现共同漏洞、需要紧急重大安全补丁时更新这个仓库。**

三个插件（`hdd-console-boot` / `dsh-cjgl` / `dsh-gd`）的自动检查会额外读本仓库根目录的
[`security.json`](./security.json)；命中的公告会被标记为「安全更新」，并优先提示/弹窗。

## 两条通道

| 通道 | 来源 | 用途 |
|---|---|---|
| 常规更新 | 各插件自己仓库的 `package.json` | 新功能、普通版本 |
| **安全补丁** | **本仓库 `security.json`** | 共同漏洞、紧急修复 |

普通版本更新不会写这里——只有需要立刻让所有人升级时才加一条 advisory。

## 格式

```jsonc
{
  "schema": 2,
  "updated": "2026-10-28",
  "advisories": [
    {
      "id": "ZJHQGX-2026-001",
      "plugin": "dsh-cjgl",        // 包名
      "severity": "high",          // low | medium | high | critical
      "summary": "…",
      "affected": "<3.0.2",        // 受影响的版本区间
      "fixed": "3.0.2",            // 修复版本
      "url": "…"                   // 详情地址
    }
  ]
}
```

插件侧判定：

```
命中 = advisories 里某条
        && advisory.plugin === 自己的包名
        && fixed > 当前版本
        && 当前版本落在 affected 区间内
```

命中后 `/update` 返回 `hasSecurityFix: true` 与 `advisory`，面板显示「安全更新」并用更醒目的样式，
自动检查时直接弹窗（常规更新只在结果行提示）。

## 维护流程

1. 发现共同漏洞 → 修在某个插件的新版本里
2. 在这里加一条 advisory（`affected` 写受影响区间，`fixed` 写修复版本）
3. 提交。三个插件在下一次检查（最多 24 小时一次）就会读到
4. 修复版本发布并稳定后，可以保留这条记录作为历史，也可以删掉——插件只关心 `fixed > 当前版本`

## License

MIT
