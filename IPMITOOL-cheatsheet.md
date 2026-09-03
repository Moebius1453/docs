# IPMITOOL-cheatsheet.md

This document is intended to provide a set of ipmitool commands for OpenBMC
usage.

## Power

### 1. View server status

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power status
```

### 2. Server power on

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power on
```

### 3. Server power off

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power off
```

### 4. Server power reset

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power reset
```

### 5. Server power cycle

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power cycle
```

### 6. Server power soft

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power soft
```

### 7. Send a diagnostic interrupt directly to the processor(not support)

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power diag
```

## Users

### 1. View information for all users

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user list
```

### 2. Display a brief summary of BMC users

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user summary
```

### 3. Create a BMC user with a given name

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user set name <userid> <username>
```

### 4. Set a given user with a given password

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user set password <userid>[<password>]
```

### 5. Disable designated users from accessing BMC

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user disable <userid>
```

### 6. Enable the specified user to access BMC

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user enable <userid>
```

## Field-replaceable Unit (FRU)

### 1. View FRU information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" fru list
```

## Sensor Data Record (SDR)

### 1. View SDR information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr
```

### 2. Query related SDR information in BMC

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr info
```

### 3. View sensor date records

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr list [all|full|compact|event|mcloc|fru|generic]

##
all     : All SDR records (sensors and positioners)
full    : Complete sensor recording
compact : Simple sensor recording
event   : Event information recorded by the sensor
mcloc   : Manage controller locator records
fru     : FRU (Field Replaceable Unit) locator record
generic : General SDR records
```

## Sensors

### 1. View sensor information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sensor list
```

## Management Controller (MC)

### 1. Instruct the BMC to perform a cold reset

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc reset cold
```

### 2. Instruct the BMC to perform a warm reset(not supported)

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc reset warm
```

### 3. View BMC information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc info
```

### 4. View the currently available operation options of BMC

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc getenables
```

## Channels

### 1. Display the authentication function about the selected information channel

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel authcap <channel number> <max privilege>
```

### 2. Display the information for the selected channel

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel info [<channel number>]
```

### 3. View channel information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel info
```

## Chassis

### 1. Display information about the high-level status of the system rack and power subsystem

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis status
```

### 2. The command will return the power on time

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis poh
```

### 3. Query the reason for the last system restart.(not supported)

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis restart_cause
```

### 4. Set rack power strategy in case of power failure

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis policy <state>

##
list        : return supported policies
always-on   : turn on when power is restored
previous    : return to previous state when power is restored
always-off  : stay off after power is restored
```

### 5. View and change power status

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis power

##
status : show current status
on     : power on
off    : power off
reset  : power reset
soft   : power soft
cycle  : power cycle
```

### 6. Set boot device for next system restart

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device>
```

##

Currently supported devices:

| device | function                                                    |
| :----: | ----------------------------------------------------------- |
|  none  | do not change boot device                                   |
|  pxe   | boot from pxe                                               |
|  disk  | boot from BIOS default boot device                          |
|  safe  | boot from BIOS default boot device,but requires a safe mode |
|  diag  | boot from the diagnostic partition                          |
| cdrom  | boot from the CD/DVD                                        |
|  bios  | enter bios settings                                         |

If you want to make your override persistent over reboots use the `persistent`
option:

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=persistent
```

If the main host machine is based on the x86 CPU you need also pay attention to
the legacy/EFI mode selector. By default IPMI overrides boot source with the
legacy mode enabled. To set EFI mode use `efiboot` option:

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=efiboot
```

You can combine options with a help of `,`:

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=persistent,efiboot
```

To read current boot source override setting:

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootparam get 5
```

### 7. Control panel logo light

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis identify <interval>
```

## Local Area Network(LAN)

### 1. Output the current configuration information of a given channel

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" lan print [<channel number>]
```

### 2. Set the given parameters for the given channel

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" lan set <channel> <command> <parameter>
##
Valid parameter
ipaddr <x.x.x.x>                 : Set ip for this channel
netmask <x.x.x.x>                : Set netmask for this channel
macaddr<xx:xx:xx:xx:xx:xx>       : Set the mac address for this channel
defgw ipaddr <x.x.x.x>           : Set the default gateway IP address
defgw macaddr<xx:xx:xx:xx:xx:xx> : Set the mac address of the default gateway
bakgw ipaddr <x.x.x.x>           : Set the IP address of the backup gateway
bakgw macaddr<xx:xx:xx:xx:xx:xx> : Set the IP address of the backup gateway
password <pass>                  : Set no user password
access <on|off>                  : Set the LAN channel access mode
```

## System Event Log (SEL)

### 1. Query the relevant information about SEL and its content in BMC

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel info
```

### 2. Clear the information in SEL,but it cannot be undone

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel clear
```

### 3. Delete a single event

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel delete <number>
```

### 4. Display the current time of the SEL clock

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel time get
```

## Session

### 1. Display session information

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" session info all
```

## Serial Over Lan (SOL)

### 1. Retrieve Serial-Over-LAN configuration information for the specified channel

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol info [<channel number>]
```

### 2. Put ipmitool into Serial Over LAN mode

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol activate
```

### 3. Disable serial LAN in BMC mode

```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol deactivate
```


# IPMITOOL‑速查手册（IPMITOOL‑cheatsheet.md）
> 本文档提供面向 OpenBMC 使用场景的一组 ipmitool 命令（This document is intended to provide a set of ipmitool commands for OpenBMC usage.）

## 电源控制（Power）
### 1. 查看服务器电源状态（View server status）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power status
```

### 2. 服务器开机（Server power on）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power on
```

### 3. 服务器关机（Server power off）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power off
```

### 4. 服务器硬件复位（Server power reset）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power reset
```

### 5. 服务器电源循环上下电（Server power cycle）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power cycle
```

### 6. 服务器软关机（Server power soft）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power soft
```

### 7. 向处理器直接发送诊断中断（不支持）（Send a diagnostic interrupt directly to the processor(not support)）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" power diag
```

## 用户管理（Users）
### 1. 查看全部用户信息（View information for all users）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user list
```

### 2. 输出BMC用户简要汇总信息（Display a brief summary of BMC users）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user summary
```

### 3. 为指定用户ID设置用户名（Create a BMC user with a given name）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user set name <userid> <username>
```

### 4. 为指定用户ID设置密码（Set a given user with a given password）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user set password <userid>[<password>]
```

### 5. 禁用指定用户访问BMC（Disable designated users from accessing BMC）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user disable <userid>
```

### 6. 启用指定用户访问BMC（Enable the specified user to access BMC）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" user enable <userid>
```

## 现场可更换单元 FRU（Field‑replaceable Unit (FRU)）
### 1. 查看FRU信息（View FRU information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" fru list
```

## 传感器数据记录 SDR（Sensor Data Record (SDR)）
### 1. 查看SDR信息（View SDR information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr
```

### 2. 查询BMC内SDR相关信息（Query related SDR information in BMC）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr info
```

### 3. 查看传感器数据记录（View sensor date records）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sdr list [all|full|compact|event|mcloc|fru|generic]

##
all     : 全部SDR记录（传感器与定位器）(All SDR records (sensors and positioners))
full    : 完整传感器记录(Complete sensor recording)
compact : 精简传感器记录(Simple sensor recording)
event   : 传感器记录的事件信息(Event information recorded by the sensor)
mcloc   : 管理控制器定位器记录(Manage controller locator records)
fru     : FRU现场可更换单元定位器记录(FRU (Field Replaceable Unit) locator record)
generic : 通用SDR记录(General SDR records)
```

## 传感器（Sensors）
### 1. 查看传感器信息（View sensor information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sensor list
```

## 管理控制器 MC（Management Controller (MC)）
### 1. 执行BMC冷复位（Instruct the BMC to perform a cold reset）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc reset cold
```

### 2. 执行BMC热复位（不支持）（Instruct the BMC to perform a warm reset(not supported)）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc reset warm
```

### 3. 查看BMC控制器信息（View BMC information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc info
```

### 4. 查看BMC当前可用功能选项（View the currently available operation options of BMC）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" mc getenables
```

## 通道配置（Channels）
### 1. 查看指定通道的认证能力（Display the authentication function about the selected information channel）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel authcap <channel number> <max privilege>
```

### 2. 输出指定通道信息（Display the information for the selected channel）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel info [<channel number>]
```

### 3. 查看通道信息（View channel information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" channel info
```

## 机箱（Chassis）
### 1. 查看系统机箱与电源子系统高层状态信息（Display information about the high‑level status of the system rack and power subsystem）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis status
```

### 2. 获取累计上电时长（The command will return the power on time）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis poh
```

### 3. 查询上次系统重启原因（不支持）（Query the reason for the last system restart.(not supported)）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis restart_cause
```

### 4. 设置机箱断电恢复后的电源策略（Set rack power strategy in case of power failure）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis policy <state>

##
list        : 返回支持的策略列表(return supported policies)
always-on   : 上电恢复后开机(turn on when power is restored)
previous    : 恢复断电前状态(return to previous state when power is restored)
always-off  : 上电恢复后保持关机(stay off after power is restored)
```

### 5. 查看与控制机箱电源状态（View and change power status）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis power

##
status : 显示当前状态(show current status)
on     : 开机(power on)
off    : 关机(power off)
reset  : 电源复位(power reset)
soft   : 软关机(power soft)
cycle  : 电源循环上下电(power cycle)
```

### 6. 设置下次系统重启的启动设备（Set boot device for next system restart）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device>
```

当前支持的启动设备（Currently supported devices）：

| device | 功能(function)                                              |
| :----: | ----------------------------------------------------------- |
|  none  | 不修改启动设备(do not change boot device)                   |
|  pxe   | 从PXE网络启动(boot from pxe)                                |
|  disk  | 使用BIOS默认启动设备(boot from BIOS default boot device)    |
|  safe  | BIOS默认设备并进入安全模式(boot from BIOS default boot device,but requires a safe mode) |
|  diag  | 从诊断分区启动(boot from the diagnostic partition)          |
| cdrom  | 从CD/DVD光驱启动(boot from the CD/DVD)                      |
|  bios  | 进入BIOS设置界面(enter bios settings)                       |

如需该启动项覆盖在多次重启持续生效，增加 `persistent` 参数（If you want to make your override persistent over reboots use the `persistent` option）：
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=persistent
```

x86 主机需要注意传统/UEFI模式选择器。IPMI默认以传统Legacy模式覆盖启动源；如需EFI模式，使用 `efiboot` 参数（If the main host machine is based on the x86 CPU you need also pay attention to the legacy/EFI mode selector. By default IPMI overrides boot source with the legacy mode enabled. To set EFI mode use `efiboot` option）：
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=efiboot
```

使用逗号 `,` 组合多个参数（You can combine options with a help of `,`）：
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootdev <device> options=persistent,efiboot
```

读取当前启动源覆盖配置（To read current boot source override setting）：
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis bootparam get 5
```

### 7. 控制机箱定位指示灯（Control panel logo light）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" chassis identify <interval>
```

## 局域网 LAN（Local Area Network(LAN)）
### 1. 输出指定通道的当前网络配置（Output the current configuration information of a given channel）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" lan print [<channel number>]
```

### 2. 设置指定通道的各项网络参数（Set the given parameters for the given channel）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" lan set <channel> <command> <parameter>
##
Valid parameter
ipaddr <x.x.x.x>                 : 设置通道IP地址(Set ip for this channel)
netmask <x.x.x.x>                : 设置子网掩码(Set netmask for this channel)
macaddr<xx:xx:xx:xx:xx:xx>       : 设置通道MAC地址(Set the mac address for this channel)
defgw ipaddr <x.x.x.x>           : 设置默认网关IP(Set the default gateway IP address)
defgw macaddr<xx:xx:xx:xx:xx:xx> : 设置默认网关MAC(Set the mac address of the default gateway)
bakgw ipaddr <x.x.x.x>           : 设置备用网关IP(Set the IP address of the backup gateway)
bakgw macaddr<xx:xx:xx:xx:xx:xx> : 设置备用网关MAC(Set the IP address of the backup gateway)
password <pass>                  : 设置用户密码(Set no user password)
access <on|off>                  : 设置LAN通道访问开关(Set the LAN channel access mode)
```

## 系统事件日志 SEL（System Event Log (SEL)）
### 1. 查询BMC中SEL概要与事件内容（Query the relevant information about SEL and its content in BMC）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel info
```

### 2. 清空SEL事件（操作不可恢复）（Clear the information in SEL,but it cannot be undone）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel clear
```

### 3. 删除单条SEL事件记录（Delete a single event）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel delete <number>
```

### 4. 获取SEL内部时钟当前时间（Display the current time of the SEL clock）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sel time get
```

## 会话管理（Session）
### 1. 显示会话信息（Display session information）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" session info all
```

## LAN串口重定向 SOL（Serial Over Lan (SOL)）
### 1. 获取指定通道SOL配置信息（Retrieve Serial‑Over‑LAN configuration information for the specified channel）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol info [<channel number>]
```

### 2. 进入SOL串口重定向会话（Put ipmitool into Serial Over LAN mode）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol activate
```

### 3. 关闭SOL串口重定向会话（Disable serial LAN in BMC mode）
```bash
ipmitool -C 17 -H "$BMC_IP" -I lanplus -U "$BMC_USER" -P "$BMC_PASSWD" sol deactivate
```

---
翻译规范说明：
1. 一级标题：`# 中文（原文文件名）`；二级标题 `## 中文（英文原文）`；三级标题 `### 中文（英文原文）`，严格对齐原文 `##`/`###` 层级标记。
2. 所有 shell 代码块、命令、占位符变量、参数、表格字段**完全保留原文不翻译**。
3. 注释、参数说明行采用`中文(英文原文)`格式。
4. 专有名词：FRU/SDR/SEL/SOL/LAN/MC/Chassis 保留标准IPMI术语，中文释义放括号。
5. 直接复制全部内容保存为 `IPMITOOL‑cheatsheet.md` 即可使用。
