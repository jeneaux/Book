# Remoting

There are a number of use cases where you need to remotely run scripts within SPE. Here we will try to cover a few of those use cases.

#### Remoting Automation Service

We have provided a handy way of executing scripts via web service using the Remoting Automation Service.

##### Sitecore to Sitecore communication

**Example:** The following connects a local instance of SPE to a remote instance and executes the provided script.

```powershell
Import-Function -Name New-ScriptSession
Import-Function -Name Invoke-RemoteScript

$session = New-ScriptSession -Username "admin" -Password "b" -ConnectionUri "http://remotesitecore"

$script = {
    [Sitecore.Security.Accounts.User]$user = Get-User -Identity admin
    $user
    $params.date.ToString()
}

$args = @{
    "date" = [datetime]::Now
}

Invoke-RemoteScript -ScriptBlock $script -Session $session -ArgumentList $args

Name                     Domain       IsAdministrator IsAuthenticated
----                     ------       --------------- ---------------
sitecore\admin           sitecore     True            False          
4/26/2015 6:15:41 PM
```

##### Windows PowerShell ISE to Sitecore communication

**Example:** The following connects Windows PowerShell ISE to a remote Sitecore instance and executes the provided script.

```powershell
Import-Module -Name SPE

$instanceUrls = @("http://remotesitecore")
$session = New-ScriptSession -Username "admin" -Password "b" -ConnectionUri $instanceUrls

$libraryPath = "/sitecore/media library/images/image.png"
Get-Item -Path C:\image.png | Send-MediaItem -Session $session -Destination $libraryPath

$savePath = "C:\image-$([datetime]::Now.ToString("yyyyddMM-HHmmss")).png"
Receive-MediaItem -Session $session -Path $libraryPath -Destination $savePath

    Directory: C:\

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---         5/25/2015  11:23 AM          0 image-20152505-112302.png  
```

**References:**
* Adam's initial post on [Remoting][1]

[1]: http://blog.najmanowicz.com/2014/10/10/sitecore-powershell-extensions-remoting/