# OpenBMC 實作筆記

本專案主要記錄在 Ubuntu + QEMU 環境下學習與實作 OpenBMC 的過程。

主要分成：

1. BitBake / QEMU
2. D-Bus
3. Redfish
4. Redfish ↔ D-Bus

---

# 📂 檔案結構與內容

* 01_bitbake
  * **使用 Yocto / BitBake 建置 OpenBMC。**
  * **安裝 OpenBMC 編譯所需的 Ubuntu 套件與開發環境。**
  * **下載 OpenBMC source code。**
  * **使用 setup evb-ast2600 初始化 AST2600 開發環境。**
  * **使用 bitbake obmc-phosphor-image 編譯 OpenBMC Image。**
  * **使用 runqemu 啟動 QEMU 模擬 BMC。**
  * **設定 QEMU Port Forward：**
8443 → 443：HTTPS / Redfish
2222 → 22：SSH
2323 → 23：Telnet
確認 QEMU 成功啟動 evb-ast2600 OpenBMC。

* 02_D-bus
  * **透過 SSH 進入 QEMU 模擬的 OpenBMC。**
  * **使用 cat /etc/os-release 確認目前執行環境為  OpenBMC。**
  * **使用 busctl list 查看目前系統上的 D-Bus Services。**
  * **使用 busctl tree 查看 D-Bus Object Tree。**
  * **使用 busctl introspect 查看指定 Object 的：**
Interface
Method
Property
Signal
  * **以 `xyz.openbmc_project.State.Host` 為例，探索：**
`/xyz/openbmc_project/state/host0`
`xyz.openbmc_project.State.Host`
`CurrentHostState`
`BootProgress`
`OperatingSystemState`
  * **使用 `busctl status` 找出 D-Bus Service 對應的 Process 與 systemd Service。**
  * **使用 `busctl monitor` 觀察 D-Bus 即時事件。**
  * **使用 `busctl get-property` 讀取指定 Object 的 Property。**
  * **使用 `obmcutil` 快速查看 OpenBMC 系統狀態。**
 
* 03_redfish
  * **使用 `curl` 測試 OpenBMC Redfish REST API。**
  * **查詢 `/redfish/v1` Service Root。**
  * **探索 Redfish API 的主要資源：**
`AccountService`
`Chassis`
`Systems`
`UpdateService`
  * **查詢 `/redfish/v1/Systems/system` 取得 Server System 資訊。**
  * **觀察 Redfish JSON 回應中的：**
`PowerState`
`Boot`
`Bios`
`BootProgress`
`GraphicalConsole`
`SerialConsole`
`LogServices`
`Memory`
`Processors`
`Storage`
  * **使用 `ComputerSystem.Reset` API 了解 Server 的開機、關機與重啟操作。**
  * **查詢 System Event Log 與 Log Service。**

* 04_redfish & D-bus
  * **驗證 DBus`CurrentPowerState`↔redfish`.PowerState`**
  * **驗證 redfish`AccountService/Accounts`↔DBus`User.Manager`**
  * **展示兩種介面查詢一致性的測試。**

* OpenBMC concepts - HackMD
  * 來源： [HackMD 筆記](https://hackmd.io/@Jimmy121538/Sym6TEtDMg)
  * 整理 OpenBMC 核心概念與系統架構，包含：
    * **Yocto / BitBake 建置架構**：Layer → Recipe → BitBake → Kernel / RootFS → BMC Image
    * **Linux / 硬體底層架構**：U-Boot → Linux Kernel → RootFS
    * **D-Bus IPC 架構**：Daemon ↔ D-Bus
    * **FRU / 硬體資訊管理**： EEPROM → FRU-Device → D-Bus 
    * **Entity Manager**：JSON Configuration → Hardware Probe → D-Bus Object
    * **Redfish / bmcweb 管理架構**：Redfish → HTTP/HTTPS → bmcweb → D-Bus → OpenBMC Daemon
    * **OpenBMC Debugging**：Kernel → Systemd / journalctl → D-Bus / busctl → Redfish
