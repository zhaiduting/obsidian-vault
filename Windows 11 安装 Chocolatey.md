Windows PowerShell
版权所有（C） Microsoft Corporation。保留所有权利。

安装最新的 PowerShell，了解新功能和改进！https://aka.ms/PSWindows 

PS C:\Users\zdt> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

Forcing web requests to allow TLS v1.2 (Required for requests to Chocolatey.org)

Getting latest version of the Chocolatey package for download.

Not using proxy.

Getting Chocolatey from https://community.chocolatey.org/api/v2/package/chocolatey/2.3.0.

Downloading https://community.chocolatey.org/api/v2/package/chocolatey/2.3.0 to C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip

Not using proxy.

Extracting C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip to C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall

Installing Chocolatey on the local machine

WARNING: Setting ChocolateyInstall Environment Variable on USER and not SYSTEM variables.

This is due to either non-administrator install OR the process you are running is not being run as an Administrator.

Creating ChocolateyInstall as an environment variable (targeting 'User')

Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'

WARNING: It's very likely you will need to close and reopen your shell

before you can use choco.

Installation of Chocolatey to default folder requires Administrative permissions. Please run from elevated prompt. Please see https://chocolatey.org/install for details and alternatives if needing to install as a non-administrator.

所在位置 C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall\tools\chocolateysetup.psm1:299 字符: 9

+ throw "Installation of Chocolatey to default folder requires ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : OperationStopped: (Installation of...-administrator.:String) [], RuntimeException
+ FullyQualifiedErrorId : Installation of Chocolatey to default folder requires Administrative permissions. Please

run from elevated prompt. Please see https://chocolatey.org/install for details and alternatives if needing to in

stall as a non-administrator.

PS C:\Users\zdt>

### 使用管理员权限重新安装（需要先删除一个文件夹）

Windows PowerShell
版权所有（C） Microsoft Corporation。保留所有权利。

安装最新的 PowerShell，了解新功能和改进！https://aka.ms/PSWindows 

PS C:\Users\zdt> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

Forcing web requests to allow TLS v1.2 (Required for requests to Chocolatey.org)

Getting latest version of the Chocolatey package for download.
Not using proxy.
Getting Chocolatey from https://community.chocolatey.org/api/v2/package/chocolatey/2.3.0.
Downloading https://community.chocolatey.org/api/v2/package/chocolatey/2.3.0 to C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip
Not using proxy.
Extracting C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip to C:\Users\zdt\AppData\Local\Temp\chocolatey\chocoInstall
Installing Chocolatey on the local machine
Creating ChocolateyInstall as an environment variable (targeting 'Machine')
  Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'
  
WARNING: It's very likely you will need to close and reopen your shell
  before you can use choco.
  
Restricting write permissions to Administrators
We are setting up the Chocolatey package repository.
The packages themselves go to 'C:\ProgramData\chocolatey\lib'
  (i.e. C:\ProgramData\chocolatey\lib\yourPackageName).
A shim file for the command line goes to 'C:\ProgramData\chocolatey\bin'
  and points to an executable in 'C:\ProgramData\chocolatey\lib\yourPackageName'.

Creating Chocolatey CLI folders if they do not already exist.

chocolatey.nupkg file not installed in lib.
 Attempting to locate it from bootstrapper.
 
警告: Not setting tab completion: Profile file does not exist at
'C:\Mac\Home\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1'.

Chocolatey CLI (choco.exe) is now ready.
You can call choco from anywhere, command line or powershell by typing choco.
Run choco /? for a list of functions.
You may need to shut down and restart powershell and/or consoles
 first prior to using choco.
Ensuring Chocolatey commands are on the path
Ensuring chocolatey.nupkg is in the lib folder

PS C:\Users\zdt>