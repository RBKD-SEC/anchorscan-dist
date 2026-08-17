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

## 首次使用配置

release 归档内的 `fathom`（闭源扫描引擎）已随包附带（包内 `tools/fathom/fathom`，Windows 为 `tools/fathom/fathom.exe`），`anchorscan doctor` 会自动检测，**无需单独下载**；fathom 不提供源码、不能自行编译，二进制受 `NOTICE` 中 proprietary 条款约束。以下两项需自行配置：

### 1. 外部工具（nmap / nuclei / httpx / nuclei-templates）

`nmap`、`nuclei`、`httpx` 为外部开源工具，需自行安装并放入系统 `PATH`（系统包管理器或官方 Releases 任选其一）：

- `nmap`（NSE 引擎）：
  - macOS：`brew install nmap`
  - Debian/Ubuntu：`sudo apt install nmap`
  - Windows / 其它平台：https://nmap.org/download.html
- `nuclei`（漏洞模板探测引擎）：
  - 已安装 Go 环境：`go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest`
  - 或下载预编译二进制：https://github.com/projectdiscovery/nuclei/releases
- `httpx`（Web 指纹识别）：
  - 已安装 Go 环境：`go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest`
  - 或下载预编译二进制：https://github.com/projectdiscovery/httpx/releases
- `nuclei-templates`（官方社区模板，nuclei 扫描必需）：
  - `git clone https://github.com/projectdiscovery/nuclei-templates ~/nuclei-templates`

安装后用 `which nmap nuclei httpx` 确认命令在 PATH 中；不在 PATH 时，编辑自动生成的 `config/default.yaml` 的 `tools` 段填入绝对路径即可。

### 2. 漏洞知识库（catalog 单源，可选）

发行归档**不带** catalog 副本（catalog 只在知识库仓库更新）。首次使用前自行克隆知识库仓库（RBKD-SEC/Pentest-Playbook，**私有仓库，需先申请访问权限**），并在配置中把 `knowledge_base.path` 指向其 catalog 产物（catalog 协议 **version 2**、`source: handbook-v2`）：

```bash
# 1. 克隆知识库仓库，得到 handbook-v2/dist/catalog.json
#    git clone git@github.com:RBKD-SEC/Pentest-Playbook.git
# 2. 编辑 config/default.yaml（首次运行 anchorscan 命令自动生成）：
#      knowledge_base:
#        path: ~/Pentest-Playbook/handbook-v2/dist/catalog.json
# 3. 重启 AnchorScan
```

未配置知识库**不影响扫描功能**（`/kb` 显示 disabled 与明确诊断），仅报告 enrich 与验证工作台无 KB 条目可用。更新方式：在克隆仓库内 `git pull` 拉取新 catalog 后重启 AnchorScan。

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
