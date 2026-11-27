# Rodak

> **把智能家居、离线语音与 AI，装进你自己的电脑或 NAS。**  
> Local-first AIoT & Home Agent workbench — paired seamlessly with open-source [BigSmart-Open](https://github.com/rymcu/BigSmart-Open) hardware and [RodakOS](https://github.com/rymcu/rodakos) firmware.

本仓库是 **Rodak** 的官方对外发布与用户指引仓库，统一发布桌面端安装包、版本更新日志及自托管部署说明，同时用于收集使用反馈与 [Issues](https://github.com/rymcu/rodak-releases/issues)。

> **关于开源范围**：Rodak 桌面应用与 `rodak-server` 为闭源本地优先软件，无需注册 Rodak 云端账号、不设云端强制订阅；与之深度协同的硬件开发板 **[BigSmart-Open](https://github.com/rymcu/BigSmart-Open)** 与终端固件 **[RodakOS](https://github.com/rymcu/rodakos)** 均全面开源。

---

## 获取与运行形态

Rodak 提供两种共享同一套本地核心引擎的运行形态，数据与密钥完全保存在你自己的设备中：

| 形态 | 适用场景 | 获取方式 |
| --- | --- | --- |
| **Rodak 桌面应用** | Windows 个人电脑与硬件玩家（开箱带系统托盘、本机离线语音、mpv 放歌与 USB 串口烧录） | 前往 [GitHub Releases](https://github.com/rymcu/rodak-releases/releases) 下载最新 Windows `x64` NSIS 安装包（支持自定义安装路径） |
| **`rodak-server` 自托管** | 群晖 / 威联通 / Unraid / TrueNAS 或任意 Linux 主机 24 小时常驻，全家浏览器直连 | 使用官方 Docker 镜像 `ghcr.io/rymcu/rodak-server:latest`（提供 `amd64` / `arm64` 双架构） |

---

## 核心能力

| 能力 | 说明 |
| --- | --- |
| **我的家：跨品牌房间控家** | 统一管理 BigSmart 语音伙伴与米家灯具/开关/传感器、小佩智能猫砂盆、鸟语花香智能喂食器；按房间或品类排列，在线连接状态与电源开关状态严格分离。 |
| **本机离线实时语音** | 内置 Silero VAD、SenseVoice / Paraformer 本地识别（中/英/日/韩/粤语）与 Kokoro 103 音色本地合成；支持播报期间随时开口打断、连续追问、回声与杂音过滤，以及多房间同时唤醒仲裁。 |
| **自由接入本地或云端大模型** | 推荐直连本机或局域网 **Ollama** 零费用运行，也可按需直连 OpenAI 兼容、Anthropic、Google Gemini、阿里云百炼与 MiniMax，不被单一模型平台锁死。 |
| **懂房间的对话与家庭音乐** | 支持一对一、多角色群聊与长期记忆，输入 `@` 按唯一标识精准引用房间与设备；对话流内透明展示工具调用过程，内建网易云音乐与飞牛 NAS 私有曲库播放卡片。 |
| **BigSmart 硬件深度协同** | 内置 ESP32 可视化固件烧录与 USB 一键可信配网、320×240 开机字标/壁纸/主题编译预览与签名发布，以及低延迟摄像头预览与远程屏幕同传。 |
| **诚实的就绪自检与用量统计** | 模型探测坚持“真的回复了才算连上，端口通不算”；本地用量默认保留 90 天明细（界面按今天 / 近 7 天 / 近 30 天核对本机免费占比与云端估算费用）。 |

---

## 渐进式界面导航

Rodak 兼顾家人日常的清爽体验与开发者的工程深度：

- **日常固定三入口**：侧边栏默认仅显示 **我的家**、**对话**、**设置**，家人打开即可控灯、看宠物设备、聊天与听歌。
- **高级与开发者工具（默认收起，共 11 项）**：按需一键展开即可访问 **设备云**、**智能体**、**扩展与能力**、**建模**、**串口**、**AI 供应商**、**观测与用量**、**语音服务**、**远程沙箱**、**对外服务**、**工作区与认证**；任意界面按下 `Ctrl + K` 可呼出全局命令搜索快速直达。

---

## 桌面快速上手（Windows）

1. 前往 [Releases](https://github.com/rymcu/rodak-releases/releases) 下载最新 Windows 安装包并完成安装。
2. 启动后进入 **设置 → 助手与语音**，配置本机 / 局域网 `Ollama` 或自有云端模型 API Key，点击探测验证真实连通。
3. 如需本机离线语音，前往 **高级与开发者工具 → 语音服务** 下载 **SenseVoice**（ASR）与 **Kokoro**（TTS），模型下载均自动校验 SHA-256 完整性。
4. 前往 **设置 → 设备账号与音乐** 绑定米家、小佩、鸟语花香及网易云 / 飞牛音乐；如有 BigSmart 开发板，可在 **串口** 完成 USB 烧录与可信配网。

---

## NAS / Linux Docker 部署（`rodak-server`）

新建工作目录 `rodak` 并保存以下 `compose.yaml`：

```yaml
services:
  rodak:
    image: ghcr.io/rymcu/rodak-server:latest
    container_name: rodak
    restart: unless-stopped
    network_mode: host
    environment:
      # 替换为你的 NAS / 服务器局域网 IP（支持多个地址，逗号分隔）
      RODAK_ADMIN_HOSTS: ${RODAK_ADMIN_HOSTS:?请设置本机局域网 IP，例如 192.168.1.10}
      RODAK_DEVICE_ADVERTISE_HOST: ${RODAK_DEVICE_ADVERTISE_HOST:-${RODAK_ADMIN_HOSTS}}
      RODAK_ADMIN_PORT: ${RODAK_ADMIN_PORT:-9543}
      RODAK_DEVICE_HTTP_PORT: ${RODAK_DEVICE_HTTP_PORT:-9080}
      RODAK_DEVICE_MQTT_PORT: ${RODAK_DEVICE_MQTT_PORT:-1883}
      # 若部署在 Caddy / Nginx 等反向代理之后，声明可信代理地址；否则留空
      RODAK_TRUSTED_PROXIES: ${RODAK_TRUSTED_PROXIES:-}
      TZ: ${TZ:-Asia/Shanghai}
    volumes:
      - ${RODAK_DATA_DIR:-./rodak-data}:/data
    stop_grace_period: 30s
```

### 启动与首次配对

```sh
RODAK_ADMIN_HOSTS=192.168.1.10 docker compose up -d
docker compose logs rodak
```

首次启动日志会打印一条 **10 分钟内有效的一次性配对链接** 以及本机生成的根 CA 指纹；在浏览器打开并核对指纹一致即可进入管理界面。如需消除浏览器的自签名证书提示，可导出根 CA 并导入系统信任库：

```sh
docker compose exec rodak node /app/out/server/main.mjs admin-ca > rodak-root-ca.pem
```

### 常用运维命令

| 操作 | 命令 |
| --- | --- |
| 重新生成管理员配对链接 | `docker compose exec rodak node /app/out/server/main.mjs login-link` |
| 为家人生成登录链接（`member` 角色） | `docker compose exec rodak node /app/out/server/main.mjs login-link --role member` |
| 准备本机离线语音识别模型 | `docker compose exec rodak node /app/out/server/main.mjs voice-setup` |
| 执行本地数据在线备份 | `docker compose exec rodak node /app/out/server/main.mjs backup` |
| 从历史备份恢复（下次启动生效） | `docker compose exec rodak node /app/out/server/main.mjs restore <备份 ID>` |
| 升级到最新镜像 | `docker compose pull && docker compose up -d` |

### 网络、数据与硬件参考

- **网络模式**：默认使用 `network_mode: host`，确保 BigSmart / RodakOS 设备可通过局域网 mDNS 自动发现服务端；默认端口为管理面 `9543`（HTTPS）、设备 HTTP `9080`、设备 MQTT `1883`，冲突时通过对应环境变量修改。
- **备份范围**：持久化数据位于 `./rodak-data`，备份时请务必将 **`data/` 与 `config/`（含主密钥）一起备份**；`models/` 与 `bin/` 可随时重新下载。同一数据目录仅挂载给单个 `rodak-server` 容器。
- **硬件占用**：官方镜像支持 `amd64` 与 `arm64`（已在 x86 主机及 Apple M1 验证）；本地 SenseVoice 加载后约占 **800 MB 内存**，桌面级 CPU 单句识别约 **0.1 秒**，无 AVX 的老旧 NAS CPU 约 **1 秒**。

---

## 开源硬件与固件生态

没有 BigSmart 硬件也可以直接使用 Rodak 管理家庭设备、对话与放歌；搭配 RYMCU 社区开源的硬件与固件，则可获得完整的桌面语音伙伴与视觉定制闭环：

| 项目 | 定位 | 开源仓库 |
| --- | --- | --- |
| **BigSmart-Open** | 基于 ESP32-S3-WROOM-1-N16R8 的开源智能语音开发板，集成 2.4" ST7789 电容触摸屏（320×240）、音频编解码、摄像头、MicroSD 卡槽与电池供电 | [rymcu/BigSmart-Open](https://github.com/rymcu/BigSmart-Open) |
| **RodakOS** | 基于 ESP-IDF + LVGL 构建的开源终端固件，提供触控优先的微型 “Phone OS”、实时语音打断、WebRTC 屏幕/摄像头同传、外观主题与安全 OTA | [rymcu/rodakos](https://github.com/rymcu/rodakos) |

### 开箱内置支持的第三方设备与音乐源

- **智能家居与宠物设备**：米家（灯具、插座、开关、传感器、音箱、路由器）、小佩（智能猫砂盆）、鸟语花香（智能喂食器）
- **家庭音乐源**：网易云音乐、飞牛音乐（NAS 本地私有曲库，离线可播）

---

## 隐私、费用与安全边界

- **离线与云端边界清晰**：所有对话记录、长期记忆、声纹档案、设备绑定与用量明细均保存在你的本地 SQLite 数据库中。使用本机离线语音（SenseVoice / Kokoro）搭配本地 Ollama 与飞牛 NAS 曲库时，全程可在局域网完全离线运行；仅当你主动配置第三方云端模型、在线语音或云端品牌设备账号时，对应请求才会直连你所选的服务商。
- **局域网可信身份**：工作台在本地生成固定 ECDSA 服务器证书，BigSmart / RodakOS 设备必须校验信任根指纹后才建立连接；浏览器远程访问采用一次性配对链接与 `owner / admin / member` 分级权限。
- **诚实的设备回执与保守默认策略**：高风险后台自主行为默认关闭，不在无人确认时静默改写设备状态；RodakOS 原生灯光与音量控制绑定当前连接并核验关联软件回执，不跨重启自动重放，且明确区分“软件回执已确认”与“物理结果已验证”（第三方设备按各自厂商接口同步状态）。

---

## 问题反馈

- **下载最新版与查看变更记录**：[GitHub Releases](https://github.com/rymcu/rodak-releases/releases)
- **Bug 反馈与功能建议**：[提交 Issue](https://github.com/rymcu/rodak-releases/issues)
- **开源硬件与固件交流**：[BigSmart-Open](https://github.com/rymcu/BigSmart-Open) · [RodakOS](https://github.com/rymcu/rodakos)
