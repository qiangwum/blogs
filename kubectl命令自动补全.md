```
1. yum install -y bash-completion
2. locate bash_completion
3. source /usr/share/bash-completion/bash_completion
4. source <(kubectl completion bash)
```
---
#### 永久生效将下面命令放入开机执行脚本里（.bashrc）
---
```
 source /usr/share/bash-completion/bash_completion
 source <(kubectl completion bash)
```
