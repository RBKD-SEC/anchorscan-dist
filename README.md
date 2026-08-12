# anchorscan-dist（temporary binary-only distribution seam）

> **迁移通知（60 天）**：本入口是 anchorscan 公开分发的**临时 binary-only** 入口。
> 现有 canonical 源码仓（`P0m32Kun/anchorscan`）的 URL **不立即失效**，历史 Release
> **不删除**。60 天迁移窗口内，请将安装脚本/文档切换到本入口的下载 URL；窗口结束后
> canonical 源码仓将改名为私有并停止继续公开源码。

## 安装

```bash
# 1. 下载对应平台的 release 归档 + 校验和
VERSION=2026.08.10.1   # 示例，以实际 release 为准
curl -LO https://github.com/RBKD-SEC/anchorscan-dist/releases/download/${VERSION}/anchorscan-${VERSION}-$(uname -s | tr A-Z a-z)-$(uname -m).tar.gz
shasum -a 256 -c anchorscan-*.tar.gz.sha256

# 2. 解包并安装
tar -xzf anchorscan-*.tar.gz
sudo cp anchorscan-*/anchorscan /usr/local/bin/

# 3. 校验
anchorscan doctor          # 依赖健康（fathom/nmap/nuclei/知识库/bundle）
```

## 升级与回滚

- **升级**：下载新 release，校验后替换二进制；历史 release 全部保留。
- **回滚**：任何验证失败 → 使用上一 release 覆盖；本入口停止更新时消费者仍可回滚旧 release。

## 内容

- 仅含授权二进制（anchorscan + 附带 Fathom 闭源引擎）、安装说明、LICENSE/NOTICE、
  checksums、provenance、release metadata。
- **无源码**：不含 Go/前端/Rust 源码、`.env`、内部路径、token 或客户数据。
- Fathom 为闭源组件，受 `NOTICE` 中 proprietary 条款约束，禁止逆向。

## 验证

每个 release 由 new-Anchor 已验收 artifact（package integration 绿）promotion 而来，
保持原 digest，**不在本仓重建**。校验见 `checksums.txt`、`provenance.json`。
