<p align="center">
  <img src="logo.png" alt="DroidVM" width="140"/>
</p>

<h1 align="center">DroidVM</h1>

<h3 align="center">Real virtual machines in Android</h3>

<p align="center">
  <a href="https://github.com/Droid-VM/DroidVM"><img src="https://img.shields.io/github/stars/Droid-VM/DroidVM?style=social&label=Star" alt="GitHub Stars"></a>
  <a href="https://github.com/Droid-VM/DroidVM/releases"><img src="https://img.shields.io/github/v/release/Droid-VM/DroidVM?include_prereleases&label=release&logo=github" alt="Release"></a>
  <a href="https://droid-vm.github.io/"><img src="https://img.shields.io/badge/docs-DroidVM%20%E6%96%87%E6%A1%A3-4D9BE8" alt="Docs"></a>
  <a href="https://github.com/Droid-VM/DroidVM/discussions"><img src="https://img.shields.io/badge/community-Discussions-blue" alt="Discussions"></a>
  <a href="https://github.com/Droid-VM/DroidVM/wiki"><img src="https://img.shields.io/badge/wiki-DroidVM%20Wiki-informational" alt="Wiki"></a>
</p>

<p align="center">
  <a href="https://t.me/Droid_VM"><img src="https://img.shields.io/badge/Telegram-Join_Community-blackviolet?logo=telegram" alt="Telegram discussion group"></a>
  <a href="https://qun.qq.com/universal-share/share?ac=1&svctype=5&tempid=h5_group_info&busi_data=eyJncm91cENvZGUiOiIxMDk4MzM1NjAxIn0%3D"><img src="https://img.shields.io/badge/QQ-%E4%BA%A4%E6%B5%81%E7%BE%A4-blackviolet?logo=QQ" alt="QQ discussion group"></a>
</p>

<p align="center">
  <b>English</b> ｜ <a href="#readme-简体中文">简体中文</a>
</p>

<p align="center">
  <a href="https://droid-vm.github.io/"><b>项目文档</b></a>
  ·
  <a href="https://github.com/Droid-VM/DroidVM/releases"><b>发布</b></a>
  ·
  <a href="https://github.com/Droid-VM/DroidVM/discussions"><b>讨论</b></a>
  ·
  <a href="https://github.com/Droid-VM/DroidVM/wiki"><b>Wiki</b></a>
</p>

---

> **What is this?** DroidVM is an Android virtual machine manager that runs on the
> phone's own hardware hypervisor — **Qualcomm Gunyah**, **MediaTek GenieZone** or
> **Linux KVM** — giving you *near-native* performance. No cloud, no emulation
> on top of a full OS; the phone itself is the VM host.

Start with **[DroidVM](https://github.com/Droid-VM/DroidVM)** — the Android app
that manages everything.

```text
┌────────────────────────────────────────────────────────────────┐
│                          Your Android                          │
│                                                                │
│    DroidVM (app / manager)                                     │
│      ├─ crosvm ──────────────── the VMM  (this org's fork)     │
│      │     │                                                   │
│      │     ├─ virtio devices · gfxstream / virglrenderer · …   │
│      │     └─ EDK2 firmware (edk2-gunyah)  /  direct kernel    │
│      ├─ host kernel modules  (Gunyah GuestAccept, hugepages…)  │
│      └─ network: bridge · vswitch · pbridge                     │
│                    │                                            │
│   ┌────────────────┼──────────────────────────────────────────┐ │
│   │   Gunyah / GenieZone / KVM  hardware hypervisor           │ │
│   └────────────────┴──────────────────────────────────────────┘ │
│                    │                                            │
│   ┌────────────────┴──────────────────────────────────────────┐ │
│   │  Guest — Linux desktop, Windows on ARM, BSD …            │ │
│   │    guest additions · virtio-gpu (mesa) · turnip          │ │
│   └───────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────┘
```

## ✨ Highlights

- **Real hypervisor, not a container.** VMs run on the SoC's hardware
  virtualization layer (`/dev/gunyah`, `/dev/gzvm` or `/dev/kvm`).
- **Two VMM backends** — [crosvm] and QEMU — with **UEFI boot** for Linux and
  **Windows on ARM**.
- **GPU acceleration**: VirGL, GfxStream and 2D software rendering; built-in VNC
  client, native display, and external-screen casting (Presentation API).
- **Full storage & disk story**: raw / qcow2 / vhdx / vdi / vmdk, snapshots,
  incremental images, LXC import, `convert` / `resize` / `clone`; share folders
  with the host via VirtFS (9p / virtiofs).
- **Network**: virtual bridge with NAT / DHCP / STP / VLAN / IPv4+IPv6 and port
  forwarding; host-side pbridge shares one upstream MAC across many VMs.
- **`.vmpkg` migration** — export a whole VM (disk + boot files + network) to a
  single file and import it on another device.

> Device requirements: **Android 13+**, **root** (Magisk / KernelSU / APatch),
> ARM64 with firmware+EL2 virtualization enabled — Snapdragon 8 Gen 3 / 8 Elite
> (Gunyah), Dimensity 9000+ (GenieZone), or a device exposing `/dev/kvm`.

## 🗺️ Repository map

> Every component below is a separate repo in this organization. Unless noted,
> its **`droidvm`** branch is the stable line used by the end-to-end build.

### App · 文档 · 构建

| Repo | 是什么 |
| --- | --- |
| [DroidVM](https://github.com/Droid-VM/DroidVM) | 主 Android 应用（虚拟机管理器；默认分支 `master`），入口仓库 |
| [DroidVM-Prebuilt-Root](https://github.com/Droid-VM/DroidVM-Prebuilt-Root) | 原生 prebuilt 产物的构建流水线（daemon、运行时、打包） |
| [DroidVM-Prebuilts](https://github.com/Droid-VM/DroidVM-Prebuilts) | 发布 DroidVM 应用随附的原生二进制（由 Prebuilt-Root 构建并推送） |
| [droidvm-meta-repo](https://github.com/Droid-VM/droidvm-meta-repo) | 端到端构建工作区：1_~9_ 脚本把上面所有组件串起来，产出 APK 与各产物 |
| [Droid-VM.github.io](https://github.com/Droid-VM/Droid-VM.github.io) | 官方文档站（VitePress）→ [droid-vm.github.io](https://droid-vm.github.io/) |

### VMM 与固件

| Repo | 是什么 |
| --- | --- |
| [crosvm](https://github.com/Droid-VM/crosvm) | AOSP crosvm 的 DroidVM fork —— 主要的虚拟化后端 |
| [Virtualization](https://github.com/Droid-VM/Virtualization) | AOSP `packages/modules/Virtualization` 的 fork（Android 虚拟化框架 + 微droid） |
| [edk2-gunyah](https://github.com/Droid-VM/edk2-gunyah) | 面向 Qualcomm Gunyah 的 UEFI 固件：在 Gunyah 上跑受保护虚拟机 |
| [linux-boot-extractor](https://github.com/Droid-VM/linux-boot-extractor) | 纯用户态从磁盘镜像中提取 `vmlinuz` / `initramfs` / 引导配置（crosvm-on-gunyah 无 UEFI 路径时的直接内核引导） |
| [win11-arm64-image-builder](https://github.com/Droid-VM/win11-arm64-image-builder) | 一键把 Win11 ARM64 ISO + 自签 gunyah/virtio 驱动做成可直接在 DroidVM 开机的 qcow2 |

### 图形栈（GPU 加速）

| Repo | 是什么 |
| --- | --- |
| [gfxstream](https://github.com/Droid-VM/gfxstream) | 用于 DroidVM 的 gfxstream：Gunyah `GuestAccept` blob 路径 + host-visible arena |
| [virglrenderer](https://github.com/Droid-VM/virglrenderer) | 用于 DroidVM 的 virglrenderer：KGSL 原生上下文后端（guest 内 turnip over vdrm） |
| [mesa](https://github.com/Droid-VM/mesa) | guest 侧 Mesa 补丁（配合 virgl / gfxstream / venus 使用） |
| [mesa-cross](https://github.com/Droid-VM/mesa-cross) | DroidVM guest mesa `.deb` 的交叉构建配方 + GitHub Actions（gfxstream / drm2kgsl / venus） |
| [turnip](https://github.com/Droid-VM/turnip) | mesa-tu8（gen8 / A8xx KGSL Turnip）fork，携带 DroidVM 本地 kgsl 修复（已归档，由 Banners-Turnip 取代） |
| [Banners-Turnip](https://github.com/Droid-VM/Banners-Turnip) | 自动化的最新 Mesa Turnip Vulkan 驱动构建，为 AdrenoTools 打包（host 端 KGSL turnip 的构建来源） |
| [virtio-media](https://github.com/Droid-VM/virtio-media) | AOSP `platform/external/virtio-media` fork（virtio-media 设备 crate + guest 驱动；稳定分支 `droidvm`） |
| [v4l2r](https://github.com/Droid-VM/v4l2r) | 用于 guest 媒体/摄像头直通探索的 V4L2 Rust 绑定 |

### 内核与模块（host / guest）

| Repo | 是什么 |
| --- | --- |
| [gh-hugepage-reserve](https://github.com/Droid-VM/gh-hugepage-reserve) | Gunyah Hugepage Reserve 内核模块（向受保护虚拟机让出大页） |
| [gunyah_host_mod](https://github.com/Droid-VM/gunyah_host_mod) | DroidVM `GuestAccept` 内存共享所需的 host 内核模块（按 GKI KMI 版本组织） |
| [droidvm-guest-additions](https://github.com/Droid-VM/droidvm-guest-additions) | Guest 内核模块：`gunyah_guest` + 打过补丁的 `virtio_gpu` |
| [android-crates-io](https://github.com/Droid-VM/android-crates-io) | Android Rust 生态 crate 镜像/fork |
| [zstd](https://github.com/Droid-VM/zstd) | AOSP `platform/external/zstd` fork（DroidVM 的 libzstd 可见性修复） |

### Windows guest 支持

| Repo | 是什么 |
| --- | --- |
| [gunyah-guest-drivers-windows](https://github.com/Droid-VM/gunyah-guest-drivers-windows) | 面向 Gunyah 受保护虚拟机的 Windows paravirtualized 驱动（rdmapool、viostor/NetKVM 等的 restricted-DMA-pool 适配） |

### 网络

| Repo | 是什么 |
| --- | --- |
| [pseudo-bridge-rs](https://github.com/Droid-VM/pseudo-bridge-rs) | `pbridge`：把单一上游 MAC（如 Wi-Fi STA）在 L2 共享给多个 VM/guest（仿 macOS pseudo-bridge / MAC-NAT） |
| [gvisor-vswitch](https://github.com/Droid-VM/gvisor-vswitch) | 基于 gVisor 的虚拟交换机 |
| [bridgedhcp](https://github.com/Droid-VM/bridgedhcp) | 桥接网络的 DHCP 服务 |
| [netbox](https://github.com/Droid-VM/netbox) | Rust netlink 网络配置工具（创建 tap / bridge / 路由规则，替代 iproute2 子集） |
| [libvncserver](https://github.com/Droid-VM/libvncserver) | 面向 Android（crosvm）的 VNC server |

### 其它组件 / 测试

| Repo | 是什么 |
| --- | --- |
| [crosvm-minimal-manifest](https://github.com/Droid-VM/crosvm-minimal-manifest) | crosvm 最小 manifest（soong 构建用） |
| [crosvm-test](https://github.com/Droid-VM/crosvm-test) | crosvm 构建测试 |

## 🔗 Quick links

| 链接 | 地址 |
| --- | --- |
| 📖 使用文档 | <https://droid-vm.github.io/> |
| 🚀 主仓库 / Releases | <https://github.com/Droid-VM/DroidVM> |
| 🏷️ Wiki | <https://github.com/Droid-VM/DroidVM/wiki> |
| 💬 Discussions | <https://github.com/Droid-VM/DroidVM/discussions> |
| 🌍 主站（DroidVM 网站） | <https://droidvm.org/>（Droid-VM.github.io 的 301 指向） |

## 🛠️ 端到端构建（快速版）

最省心的方式是用 **droidvm-meta-repo** 的流水线（`1_`→`9_` 各阶段脚本把本组织所有组件串起来）。想单独构建，对每个组件：

```bash
git clone -b droidvm https://github.com/Droid-VM/<repo>.git   # 各组件走 droidvm 稳定分支
# 应用本体（唯一例外，稳定分支叫 master）：
git clone -b master https://github.com/Droid-VM/DroidVM.git
```

详细的构建与部署步骤见各仓库 README 与 [droidvm-meta-repo 的流程说明](https://github.com/Droid-VM/droidvm-meta-repo)。

## 🧱 技术栈一览

- **App**：Java / Kotlin，Gradle，Android 13+（API 33+），守护进程模型，Termux 终端库
- **VMM**：Rust（crosvm）、C（QEMU 路径）、C（EDK2 固件）
- **图形**：C / C++（gfxstream、virglrenderer、Mesa、Turnip）
- **内核**：C（Gunyah host/guest 模块）、Windows driver 工具链（rdmapool / virtio-win）
- **网络**：Rust（pbridge）、Go（vswitch / DHCP）
- **CI / 流水线**：GitHub Actions、Python（prebuilt 自动化）、Shell、PowerShell、.NET（Windows 镜像路线）

## 🙏 致谢

DroidVM 站在上游社区的肩膀上：ChromiumOS 的 [crosvm]、[virtio-media]、
Google 的 [gfxstream]、Freedesktop 的 [Mesa]（含 [turnip]）与
[virglrenderer]、TianoCore 的 EDK2、Red Hat 的 virtio-win —— 以及
Qualcomm Gunyah / MediaTek GenieZone 的硬件虚拟化能力。各 fork 均保留上游
许可证与归属。

---

## Roadmap（路线图摘要）

活跃工作包括：**3D 加速通路**（gfxstream + virglrenderer KGSL 后端 + guest
mesa/turnip）、**Gunyah 受保护虚拟机**内存模型（GuestAccept / hugepage / rdmapool）、
**Windows on ARM** 支持与 **网络栈**（pbridge / vswitch）。最新进展见各仓库
commit、Discussions 与 [DroidVM wiki](https://github.com/Droid-VM/DroidVM/wiki)。

---

<a id="readme-简体中文"></a>
<p align="center">— · — · —</p>

<h1 align="center">DroidVM</h1>
<h3 align="center">在 Android 里跑真正的虚拟机</h3>

**DroidVM** 是运行在 Android 设备上的虚拟机管理器，直接利用手机的
**硬件虚拟化**能力——高通 **Gunyah**、联发科 **GenieZone** 或 **Linux KVM**——
在手机上创建和管理轻量级虚拟机，获得**接近原生**的性能。它不只是 App：
Droid-VM 这个组织维护着把"在手机上跑虚拟机"这件事打通的一整条技术栈。

### 核心亮点

- 🖥️ **真正跑在 Hypervisor 上**，而非容器/模拟：设备存在 `/dev/gunyah`、
  `/dev/gzvm` 或 `/dev/kvm` 即可。
- 🚀 **双后端**：自维护的 [crosvm] fork + QEMU；**UEFI 引导** Linux 与 **Windows on ARM**。
- 🎮 **GPU 加速**：VirGL / GfxStream / 2D 软渲染；内置 VNC、原生显示、外接屏投屏。
- 💾 **磁盘管理**：raw / qcow2 / vhdx / vdi / vmdk、快照、增量、LXC 导入、
  convert / resize / clone，以及 VirtFS（9p / virtiofs）共享目录。
- 🌐 **网络**：虚拟桥接（NAT / DHCP / STP / VLAN / IPv4+IPv6 / 端口转发），
  host 侧 pbridge 可让多个 VM 共享一个上游 MAC（如手机 Wi-Fi）。
- 📦 **虚拟机包**：整个虚拟机（磁盘 + 引导 + 网络配置）导出为单个 `.vmpkg`，
  迁移到别的设备。

### 组织构成（主要仓库）

| 分类 | 仓库 |
| --- | --- |
| **App / 文档 / 构建** | [DroidVM]（主应用）· [DroidVM-Prebuilt-Root]（原生产物构建）· [DroidVM-Prebuilts]（二进制发布）· [droidvm-meta-repo]（1_~9_ 端到端构建）· [Droid-VM.github.io]（VitePress 文档站） |
| **VMM 与固件** | [crosvm] · [Virtualization]（AOSP 虚拟化框架）· [edk2-gunyah] · [linux-boot-extractor] · [win11-arm64-image-builder] |
| **图形栈** | [gfxstream] · [virglrenderer] · [mesa] · [mesa-cross] · [turnip]（已归档）· [Banners-Turnip] · [virtio-media] · [v4l2r] |
| **内核模块** | [gh-hugepage-reserve] · [gunyah_host_mod] · [droidvm-guest-additions] · [android-crates-io] · [zstd] |
| **Windows 支持** | [gunyah-guest-drivers-windows]（rdmapool / virtio-win 的 restricted-DMA-pool 适配） |
| **网络** | [pseudo-bridge-rs]（pbridge）· [gvisor-vswitch] · [bridgedhcp] · [netbox] · [libvncserver] |

**分支约定**：除应用本体 [DroidVM] 的稳定分支是 `master`（唯一例外）外，
本组织所有仓库的稳定分支统一为 **`droidvm`**。

### 支持平台与环境

- **系统**：Android 13（API 33）及以上
- **权限**：需要 root（Magisk / KernelSU / APatch）
- **设备**：ARM64 且固件/内核已开启硬件虚拟化——
  - 高通：骁龙 8 Gen 3（SM8650）及以上，推荐 8 Elite（SM8750），开启 Gunyah
  - 联发科：天玑 9000 及以上，开启 GenieZone
  - 其它：以 EL2 引导且内核开启 Linux KVM

### 组织动态（截至 2026-09-07）

- **应用本体已到 660+ ⭐**，release 通道活跃（`dev` tag 持续更新）。
- 组织正全力推进 **3D 加速**（`droidvm-meta-repo`、gfxstream `GuestAccept`
  blob + host-visible arena、virglrenderer KGSL 原生上下文后端、guest mesa .deb
  交叉构建、Banners-Turnip 提供 host 端最新 turnip）。
- **Windows on ARM 镜像**构建器 + **Gunyah Windows 驱动**已就绪，
  可直接产出能在 DroidVM 开机的 Win11 qcow2。
- 欢迎在 [Discussions] 交流、按各仓库 README 参与构建与贡献。

### 参与方式

```bash
# 先跑通应用本体
git clone -b master https://github.com/Droid-VM/DroidVM.git
cd DroidVM && git submodule update --init --recursive && ./gradlew assembleRelease

# 想编译整套原生栈 → 用 meta repo
git clone https://github.com/Droid-VM/droidvm-meta-repo.git && cd droidvm-meta-repo
# 按 1_~9_ 脚本阶段执行（README 有依赖图）
```

需要更多细节？请看：
[主仓库 DroidVM](https://github.com/Droid-VM/DroidVM) ·
[使用文档](https://droid-vm.github.io/) ·
[Wiki](https://github.com/Droid-VM/DroidVM/wiki) ·
[Releases](https://github.com/Droid-VM/DroidVM/releases)

---

*Droid-VM 组织简介 · 数据与仓库清单基于 GitHub 实际状态（2026-09-07）整理*

[DroidVM]: https://github.com/Droid-VM/DroidVM
[DroidVM-Prebuilt-Root]: https://github.com/Droid-VM/DroidVM-Prebuilt-Root
[DroidVM-Prebuilts]: https://github.com/Droid-VM/DroidVM-Prebuilts
[droidvm-meta-repo]: https://github.com/Droid-VM/droidvm-meta-repo
[Droid-VM.github.io]: https://github.com/Droid-VM/Droid-VM.github.io
[crosvm]: https://github.com/Droid-VM/crosvm
[Virtualization]: https://github.com/Droid-VM/Virtualization
[edk2-gunyah]: https://github.com/Droid-VM/edk2-gunyah
[linux-boot-extractor]: https://github.com/Droid-VM/linux-boot-extractor
[win11-arm64-image-builder]: https://github.com/Droid-VM/win11-arm64-image-builder
[gfxstream]: https://github.com/Droid-VM/gfxstream
[virglrenderer]: https://github.com/Droid-VM/virglrenderer
[mesa]: https://github.com/Droid-VM/mesa
[mesa-cross]: https://github.com/Droid-VM/mesa-cross
[turnip]: https://github.com/Droid-VM/turnip
[Banners-Turnip]: https://github.com/Droid-VM/Banners-Turnip
[virtio-media]: https://github.com/Droid-VM/virtio-media
[v4l2r]: https://github.com/Droid-VM/v4l2r
[gh-hugepage-reserve]: https://github.com/Droid-VM/gh-hugepage-reserve
[gunyah_host_mod]: https://github.com/Droid-VM/gunyah_host_mod
[droidvm-guest-additions]: https://github.com/Droid-VM/droidvm-guest-additions
[android-crates-io]: https://github.com/Droid-VM/android-crates-io
[zstd]: https://github.com/Droid-VM/zstd
[gunyah-guest-drivers-windows]: https://github.com/Droid-VM/gunyah-guest-drivers-windows
[pseudo-bridge-rs]: https://github.com/Droid-VM/pseudo-bridge-rs
[gvisor-vswitch]: https://github.com/Droid-VM/gvisor-vswitch
[bridgedhcp]: https://github.com/Droid-VM/bridgedhcp
[netbox]: https://github.com/Droid-VM/netbox
[libvncserver]: https://github.com/Droid-VM/libvncserver
[Discussions]: https://github.com/Droid-VM/DroidVM/discussions
