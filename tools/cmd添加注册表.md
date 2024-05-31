[reg 命令 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows-server/administration/windows-commands/reg)

# 1. 
` 
reg add <keyname> [/v valuename | /ve] [/t datatype] [/s separator] [/d data] [/f] [/reg:32 | /reg:64]
`
# 2. 添加和修改
## 2.1 添加注册表目录
要添加一个注册表项目录\mysoft\erp25到HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\中

`
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25
`
## 2.2 添加注册表值
添加注册表项servername和值

`
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25/v servername /t REG_SZ /d 192.168.10.11
`
## 2.3 修改注册表项的值
现在要修改servername的值为 192.168.1.1，可在命令后面加参数 /f ，/f表示不用询问信息而直接添加子项或项

`
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25/v servername /t REG_SZ /d 192.168.1.1/f 
`
# 3 查询
查看指定路径的注册表项的子项

`
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25
`
## 3.1 指定名称查询

`
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25/v servername
`
# 4 删除
删除名称是servername的值

`
reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25/v servername /f
`
## 4.1 删除指定路径

`
reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\mysoft\erp25/f
`
