```
zypper install MozillaFirefox
zypper source-install apache2-mod_nss
zypper list-updates
zypper remove MozillaFirefox
zypper search usb*
zypper info usbutils
zypper search --repo Linuxprobe_Repo  #在库中搜索
zypper lr
zypper lr --uri
zypper removerepo MyLinuxRepo
zypper modifyrepo -d Mozillarepo    disable禁用库
zypper modifyrepo -e Mozillarepo    enable使用库
zypper refresh Mozillarepo          刷新库
# 添加本地repo
zypper ar file:///mnt/ local_repo
```
