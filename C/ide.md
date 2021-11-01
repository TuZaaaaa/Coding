# C
vscode 控制台输出中文乱码问题
解决方案：
```json
    "terminal.integrated.profiles.windows":{
        "PowerShell": {
            "source": "PowerShell",
            "overrideName": true,
            "args": ["-NoExit", "/c", "chcp 65001"],
            "icon": "terminal-powershell",
            "env": {
                "TEST_VAR": "value"
            }
        }
    },
    "terminal.integrated.defaultProfile.windows": "PowerShell"
```