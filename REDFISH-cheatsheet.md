# Redfish cheat sheet

This document is intended to provide a set of example [Redfish][1] client
commands for OpenBMC usage. This document uses cURL. This document assumes
several ids, such as ManagerId, "bmc", and ComputerSystemId, "system". Assuming
an id is not correct and any software written to use the Redfish API should not.
From the Redfish Specification, DSP0266, "Clients shall not make assumptions
about the URIs for the members of a resource collection."

## Query Redfish Service Root

```bash
export bmc=xx.xx.xx.xx
curl -k https://${bmc}/redfish/v1
```

---

## Establish Redfish connection session

### Method 1

```bash
export bmc=xx.xx.xx.xx
curl --insecure -H "Content-Type: application/json" -X POST -D headers.txt https://${bmc}/redfish/v1/SessionService/Sessions -d    '{"UserName":"root", "Password":"0penBmc"}'
```

A file, headers.txt, will be created. Find the `"X-Auth-Token"` in that file.
Save it away in an env variable like so:

```bash
export bmc_token=<token>
```

### Method 2

```bash
export bmc=xx.xx.xx.xx
export token=`curl -k -H "Content-Type: application/json" -X POST https://${bmc}/login -d '{"username" :  "root", "password" :  "0penBmc"}' | grep token | awk '{print $2;}' | tr -d '"'`
curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/...
```

Note: Method 2 is used in this document.

---

## View Redfish Objects

```bash
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Chassis
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Managers
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Systems
```

---

## View sessions

```bash
curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/SessionService/Sessions
```

---

## Host power

Host soft power off:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "GracefulShutdown"}'
```

Host hard power off:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "ForceOff"}'
```

Host power on:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "On"}'
```

Reboot Host:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "GracefulRestart"}'
```

---

## BMC reboot

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Managers/bmc/Actions/Manager.Reset -d '{"ResetType": "GracefulRestart"}'
```

---

## BMC factory reset

Proceed with caution:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Managers/bmc/Actions/Manager.ResetToDefaults -d '{"ResetType": "ResetAll"}'
```

---

## Log entry

Display logging entries:

```bash
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Systems/system/LogServices/EventLog/Entries
```

Delete logging entries:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/LogServices/EventLog/Actions/LogService.Reset
```

---

## Firmware update

Firmware update: Note the `<image file path>` must be a tarball.

```bash
uri=$(curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/UpdateService | jq -r ' .HttpPushUri')

curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/octet-stream" -X POST -T <image file path> https://${bmc}${uri}
```

Firmware update using multi-part form data: Note the `<apply time>` can be
`OnReset` or `Immediate`.

```bash
uri=$(curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/UpdateService | jq -r ' .MultipartHttpPushUri')

curl -k -H "X-Auth-Token: $token" -H "Content-Type:multipart/form-data" -X POST -F UpdateParameters="{\"Targets\":[\"/redfish/v1/Managers/bmc\"],\"@Redfish.OperationApplyTime\":<apply time>};type=application/json" -F "UpdateFile=@<image file path>;type=application/octet-stream" https://${bmc}${uri}
```

TFTP Firmware update using TransferProtocol: Note: The `<image file path>`
contains the address of the TFTP service:
`xx.xx.xx.xx/obmc-phosphor-xxxxx-xxxxxxxxx.static.mtd.tar`

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/UpdateService/Actions/UpdateService.SimpleUpdate -d '{"TransferProtocol":"TFTP","ImageURI":"<image file path>"}'
```

TFTP Firmware update with protocol in ImageURI:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/UpdateService/Actions/UpdateService.SimpleUpdate -d '{"ImageURI":"tftp://<image file path>"}'
```

---

## Update "root" password

Change password to "0penBmc1":

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH -d '{"Password": "0penBmc1"}' https://${bmc}/redfish/v1/AccountService/Accounts/root
```

---

## BIOS firmware boot control

Enter into BIOS setup on boot

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Continuous","BootSourceOverrideTarget": "BiosSetup"}}'
```

Fully boot

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Disabled","BootSourceOverrideTarget": "None"}}'
```

Change Legacy/EFI selector (valid only if host is based on the x86 CPU)

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Once","BootSourceOverrideTarget": "None","BootSourceOverrideMode": "UEFI"}}'
```

---

## Enable NTP

Add a NTP Server

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"NTP":{"NTPServers":["time.nist.gov"]}}'
```

Now enable NTP

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"NTP":{"ProtocolEnabled": true}}'
```

---

## Disable IPMI

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"IPMI":{"ProtocolEnabled": false}}'
```

## Manage subscriptions

List configured subscriptions:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/
```

Setup subscription

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/EventService/Subscriptions -d '{"Context": "My context", "DeliveryRetryPolicy": "RetryForever", "Destination": "http://yy.yy.yy.yy:8888/events", "EventFormatType": "Event", "Protocol": "Redfish", "SubscriptionType": "RedfishEvent"}"
```

Store the ID of the first subscription in an environment variable:

```bash
export subscription_id=$(curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/ |jq '.Members[0]."@odata.id"' | cut -d '/' -f 6| cut -d '"' -f 1)
```

Display information for the subscription associated with the stored ID:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id}
```

Update the subscription corresponding to the stored ID:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id} -X PATCH -d '{"VerifyCertificate": false}'
```

Remove the subscription identified by the stored ID:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id} -X DELETE
```

### Test subscriptions

Send a test event using embedded into Redfish Event Service facility:

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Actions/EventService.SubmitTestEvent -X POST -d '{"EventTimestamp":"2023-02-13T14:49:20Z","Severity":"Warning","Message":"This is a test event message 2","MessageId":"iLOResourceEvents.1.3.DrvArrLogDrvErasing","MessageArgs":["1","slot 3"],"OriginOfCondition":"/redfish/v1/Systems/1/Storage"}'
```

Send a test DBus event, it should be picked up by the bmcweb and send event to
the subscriber (the command should be executed on the BMC):

```bash
busctl call xyz.openbmc_project.Logging \
/xyz/openbmc_project/logging \
xyz.openbmc_project.Logging.Create \
Create 'ssa{ss}' \
OpenBMC.0.1.PowerButtonPressed \
xyz.openbmc_project.Logging.Entry.Level.Error 0
```

[1]: https://www.dmtf.org/standards/redfish


# Redfish 速查手册
本文档提供 OpenBMC 环境下 Redfish 客户端命令示例，全部使用 cURL。文档中直接使用若干 ID，例如管理器 ID `bmc`、计算机系统 ID `system`。**正式开发 Redfish API 的软件，不应当硬编码假定这些 ID 值**。依据 Redfish 规范 DSP0266：**客户端不得对资源集合成员的 URI 做硬性假设**。

## 查询 Redfish 服务根节点【Query Redfish Service Root】
```bash
export bmc=xx.xx.xx.xx
curl -k https://${bmc}/redfish/v1
```

---

## 建立 Redfish 会话连接【Establish Redfish connection session】
### 方式一【Method 1】
```bash
export bmc=xx.xx.xx.xx
curl --insecure -H "Content-Type: application/json" -X POST -D headers.txt https://${bmc}/redfish/v1/SessionService/Sessions -d    '{"UserName":"root", "Password":"0penBmc"}'
```

执行后生成文件 `headers.txt`，从中提取响应头内的 `"X‑Auth‑Token"`，存入环境变量：
```bash
export bmc_token=<token>
```

### 方式二【Method 2】
```bash
export bmc=xx.xx.xx.xx
export token=`curl -k -H "Content-Type: application/json" -X POST https://${bmc}/login -d '{"username" :  "root", "password" :  "0penBmc"}' | grep token | awk '{print $2;}' | tr -d '"'`
curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/...
```

> 注：本文后续示例均采用方式二获取的 `$token`。

---

## 查看 Redfish 资源对象【View Redfish Objects】
```bash
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Chassis
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Managers
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Systems
```

---

## 查询已建立会话【View sessions】
```bash
curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/SessionService/Sessions
```

---

## 主机电源控制【Host power】
主机优雅软关机：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "GracefulShutdown"}'
```

主机强制硬关机：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "ForceOff"}'
```

主机开机：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "On"}'
```

主机重启（优雅重启）：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/Actions/ComputerSystem.Reset -d '{"ResetType": "GracefulRestart"}'
```

---

## BMC 重启【BMC reboot】
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Managers/bmc/Actions/Manager.Reset -d '{"ResetType": "GracefulRestart"}'
```

---

## BMC 恢复出厂设置【BMC factory reset】
> ⚠️谨慎执行，会清空配置

```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Managers/bmc/Actions/Manager.ResetToDefaults -d '{"ResetType": "ResetAll"}'
```

---

## 系统日志【Log entry】
查看日志条目：
```bash
curl -k -H "X-Auth-Token: $token" -X GET https://${bmc}/redfish/v1/Systems/system/LogServices/EventLog/Entries
```

清空全部日志：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/Systems/system/LogServices/EventLog/Actions/LogService.Reset
```

---

## 固件升级【Firmware update】
### HTTP 推送固件
> 注意：`<image file path>` 必须为 tar 压缩包

```bash
uri=$(curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/UpdateService | jq -r ' .HttpPushUri')

curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/octet-stream" -X POST -T <image file path> https://${bmc}${uri}
```

### Multipart 表单方式固件升级
`<apply time>` 取值可为 `OnReset`（重启生效）或 `Immediate`（立即生效）。
```bash
uri=$(curl -k -H "X-Auth-Token: $token" https://${bmc}/redfish/v1/UpdateService | jq -r ' .MultipartHttpPushUri')

curl -k -H "X-Auth-Token: $token" -H "Content-Type:multipart/form-data" -X POST -F UpdateParameters="{\"Targets\":[\"/redfish/v1/Managers/bmc\"],\"@Redfish.OperationApplyTime\":<apply time>};type=application/json" -F "UpdateFile=@<image file path>;type=application/octet-stream" https://${bmc}${uri}
```

### TFTP 协议固件升级（指定 TransferProtocol）
`<image file path>` 填写 TFTP 服务地址+文件名，示例格式：
`xx.xx.xx.xx/obmc-phosphor-xxxxx-xxxxxxxxx.static.mtd.tar`
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/UpdateService/Actions/UpdateService.SimpleUpdate -d '{"TransferProtocol":"TFTP","ImageURI":"<image file path>"}'
```

### TFTP 固件升级（URI 内携带协议头）
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/UpdateService/Actions/UpdateService.SimpleUpdate -d '{"ImageURI":"tftp://<image file path>"}'
```

---

## 修改 root 用户密码【Update "root" password】
示例将密码修改为 `0penBmc1`：
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH -d '{"Password": "0penBmc1"}' https://${bmc}/redfish/v1/AccountService/Accounts/root
```

---

## BIOS 启动项控制【BIOS firmware boot control】
开机直接进入 BIOS 设置界面
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Continuous","BootSourceOverrideTarget": "BiosSetup"}}'
```

关闭启动项强制覆盖，正常启动主机
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Disabled","BootSourceOverrideTarget": "None"}}'
```

切换 Legacy / UEFI 启动模式（仅 x86 主机有效）
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Systems/system -d '{"Boot":{"BootSourceOverrideEnabled": "Once","BootSourceOverrideTarget": "None","BootSourceOverrideMode": "UEFI"}}'
```

---

## NTP 时间同步配置【Enable NTP】
添加 NTP 服务器地址
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"NTP":{"NTPServers":["time.nist.gov"]}}'
```

启用 NTP 协议
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"NTP":{"ProtocolEnabled": true}}'
```

---

## 关闭 IPMI 协议【Disable IPMI】
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X PATCH https://${bmc}/redfish/v1/Managers/bmc/NetworkProtocol -d '{"IPMI":{"ProtocolEnabled": false}}'
```

---

## 事件订阅管理【Manage subscriptions】
查询已配置事件订阅列表
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/
```

创建事件订阅
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" -X POST https://${bmc}/redfish/v1/EventService/Subscriptions -d '{"Context": "My context", "DeliveryRetryPolicy": "RetryForever", "Destination": "http://yy.yy.yy.yy:8888/events", "EventFormatType": "Event", "Protocol": "Redfish", "SubscriptionType": "RedfishEvent"}'
```

提取第一条订阅的 ID，存入环境变量
```bash
export subscription_id=$(curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/ |jq '.Members[0]."@odata.id"' | cut -d '/' -f 6| cut -d '"' -f 1)
```

查看该条订阅详情
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id}
```

更新订阅配置
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id} -X PATCH -d '{"VerifyCertificate": false}'
```

删除指定订阅
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Subscriptions/${subscription_id} -X DELETE
```

### 事件订阅测试【Test subscriptions】
调用 Redfish 事件服务接口发送测试事件
```bash
curl -k -H "X-Auth-Token: $token" -H "Content-Type: application/json" https://${bmc}/redfish/v1/EventService/Actions/EventService.SubmitTestEvent -X POST -d '{"EventTimestamp":"2023-02-13T14:49:20Z","Severity":"Warning","Message":"This is a test event message 2","MessageId":"iLOResourceEvents.1.3.DrvArrLogDrvErasing","MessageArgs":["1","slot 3"],"OriginOfCondition":"/redfish/v1/Systems/1/Storage"}'
```

在 BMC 本地执行，发送 D‑Bus 测试事件，bmcweb 会捕获并转发给事件订阅方：
```bash
busctl call xyz.openbmc_project.Logging \
/xyz/openbmc_project/logging \
xyz.openbmc_project.Logging.Create \
Create 'ssa{ss}' \
OpenBMC.0.1.PowerButtonPressed \
xyz.openbmc_project.Logging.Entry.Level.Error 0
```

[1]: https://www.dmtf.org/standards/redfish

