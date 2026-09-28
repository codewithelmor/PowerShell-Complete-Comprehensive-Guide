# PowerShell — Complete Comprehensive Guide

A detailed reference and use-case guide for PowerShell: core concepts, syntax, commands (cmdlets), and real-world scripting patterns. Written for beginners through intermediate users who want a single reference document.

---

## Table of Contents

1. [What is PowerShell](#1-what-is-powershell)
2. [Installing & Editions](#2-installing--editions)
3. [PowerShell Basics](#3-powershell-basics)
4. [Variables & Data Types](#4-variables--data-types)
5. [Operators](#5-operators)
6. [Cmdlet Syntax & Getting Help](#6-cmdlet-syntax--getting-help)
7. [The Pipeline](#7-the-pipeline)
8. [Control Flow](#8-control-flow)
9. [Functions](#9-functions)
10. [Arrays, Hashtables & Objects](#10-arrays-hashtables--objects)
11. [Error Handling](#11-error-handling)
12. [Working with Files & Folders](#12-working-with-files--folders)
13. [Working with Processes & Services](#13-working-with-processes--services)
14. [Working with the Registry](#14-working-with-the-registry)
15. [Networking Commands](#15-networking-commands)
16. [Remoting (Running Commands on Other Machines)](#16-remoting-running-commands-on-other-machines)
17. [Modules](#17-modules)
18. [Scheduled Tasks & Automation](#18-scheduled-tasks--automation)
19. [Security & Execution Policy](#19-security--execution-policy)
20. [Working with CSV, JSON & XML](#20-working-with-csv-json--xml)
21. [Common Use Cases (End-to-End Scripts)](#21-common-use-cases-end-to-end-scripts)
22. [Best Practices](#22-best-practices)
23. [Quick Reference Cheat Sheet](#23-quick-reference-cheat-sheet)

---

## 1. What is PowerShell

PowerShell is a command-line shell and scripting language built on the .NET runtime. Unlike traditional shells (cmd.exe, bash) that pass plain text between commands, PowerShell passes **structured .NET objects** through its pipeline. This means you can access properties and methods directly instead of parsing text output.

Two main variants exist:
- **Windows PowerShell** — built into Windows, versions 1.0–5.1, `.NET Framework` based, Windows-only.
- **PowerShell (Core) 7+** — cross-platform (Windows, macOS, Linux), open source, built on `.NET`, the actively developed version. Executable is `pwsh` (vs `powershell.exe` for Windows PowerShell).

Use `$PSVersionTable` to check what you're running:

```powershell
$PSVersionTable
```

This prints a table including `PSVersion`, `PSEdition` (`Desktop` or `Core`), and `OS`.

---

## 2. Installing & Editions

**Windows:** PowerShell 5.1 ships with Windows 10/11. To install PowerShell 7:

```powershell
winget install --id Microsoft.PowerShell --source winget
```

**macOS:**
```bash
brew install --cask powershell
```

**Linux (Ubuntu example):**
```bash
sudo apt-get update
sudo apt-get install -y powershell
```

Launch PowerShell 7 with `pwsh`, Windows PowerShell with `powershell`.

---

## 3. PowerShell Basics

### The Console / ISE / VS Code
- `powershell.exe` / `pwsh` — the interactive console.
- **PowerShell ISE** — legacy Windows-only GUI editor (Windows PowerShell only, not Core).
- **VS Code + PowerShell extension** — the modern recommended editor, works with both editions, has IntelliSense, debugging, and linting (PSScriptAnalyzer).

### Comments
```powershell
# Single-line comment

<#
Multi-line
comment block
#>
```

### Case Insensitivity
PowerShell commands, parameters, and variable names are **case-insensitive** by default (`$Name` and `$name` are the same variable). String *comparisons* using `-eq` are also case-insensitive unless you use the case-sensitive variant (`-ceq`).

### Running a Script
```powershell
.\MyScript.ps1
```
You must reference the script with `.\` (or a full path) even if you're in the same folder — PowerShell does not run scripts from the current directory by default for security reasons, unlike cmd.exe.

---

## 4. Variables & Data Types

Variables start with `$` and are dynamically typed:

```powershell
$name = "Elmor"
$age = 35
$isActive = $true
$price = 19.99
$nothing = $null
```

### Explicit typing
```powershell
[int]$count = 10
[string]$text = "Hello"
[datetime]$today = Get-Date
```

### Common types
| Type | Description | Example |
|---|---|---|
| `[string]` | Text | `"Hello"` |
| `[int]` | 32-bit integer | `42` |
| `[long]` | 64-bit integer | `9999999999` |
| `[double]` | Floating point | `3.14` |
| `[bool]` | Boolean | `$true` / `$false` |
| `[datetime]` | Date/time | `Get-Date` |
| `[array]` | Collection | `@(1,2,3)` |
| `[hashtable]` | Key/value pairs | `@{Name="A"}` |

### Automatic (built-in) variables
| Variable | Meaning |
|---|---|
| `$_` or `$PSItem` | Current object in the pipeline |
| `$args` | Arguments passed to a script/function |
| `$Error` | Array of the most recent errors |
| `$Home` | Current user's home directory |
| `$PWD` | Current working directory |
| `$null` | Null/empty value |
| `$true` / `$false` | Boolean literals |
| `$LASTEXITCODE` | Exit code of the last native (non-PowerShell) command |
| `$?` | `True`/`False` — whether the last command succeeded |
| `$PSVersionTable` | Version/edition info |
| `$Profile` | Path to the current user's profile script |

### Environment variables
```powershell
$env:USERNAME
$env:Path
$env:COMPUTERNAME
```

### String interpolation & formatting
```powershell
$name = "Elmor"
"Hello, $name!"                      # Hello, Elmor!
"Hello, ${name}!"                    # useful when text touches the variable name
"Result: $($age * 2)"                # embed expressions with $(...)
'Hello, $name'                       # single quotes = literal, no interpolation
"{0} is {1} years old" -f $name,$age # -f format operator
```

Here-strings for multi-line text:
```powershell
$text = @"
Line one, $name
Line two
"@
```

---

## 5. Operators

### Comparison
| Operator | Meaning |
|---|---|
| `-eq` | Equal |
| `-ne` | Not equal |
| `-gt` / `-ge` | Greater than / or equal |
| `-lt` / `-le` | Less than / or equal |
| `-like` | Wildcard match (`*`, `?`) |
| `-notlike` | Wildcard non-match |
| `-match` | Regex match |
| `-notmatch` | Regex non-match |
| `-contains` | Collection contains value |
| `-in` | Value is in collection |
| `-is` | Type check |

All comparison operators are case-*insensitive* by default; prefix with `c` (`-ceq`, `-cmatch`) for case-sensitive, or `i` (`-ieq`) to be explicit about case-insensitive.

### Logical
```powershell
-and, -or, -not (or !), -xor
```

### Arithmetic
```powershell
+  -  *  /  %   # add, subtract, multiply, divide, modulus
```

### Assignment
```powershell
=  +=  -=  *=  /=  %=
```

### Redirection & special
```powershell
| # pipeline
`# backtick — line-continuation / escape character
;  # statement separator on one line
..  # range operator, e.g. 1..5
```

---

## 6. Cmdlet Syntax & Getting Help

PowerShell commands are called **cmdlets** and follow a strict `Verb-Noun` naming pattern, e.g. `Get-Process`, `Set-Location`, `New-Item`, `Remove-Item`.

List approved verbs:
```powershell
Get-Verb
```

### Getting help (use this constantly)
```powershell
Get-Help Get-Process
Get-Help Get-Process -Examples
Get-Help Get-Process -Full
Get-Help Get-Process -Online
Update-Help          # downloads the latest help files (run as admin)
```

### Discovering commands
```powershell
Get-Command                       # list all available commands
Get-Command -Verb Get             # all "Get-" cmdlets
Get-Command -Noun Service         # all cmdlets that work with Service
Get-Command *process*             # wildcard search by name
```

### Inspecting objects
```powershell
Get-Process | Get-Member          # list all properties & methods of an object
```
`Get-Member` is one of the most useful commands for exploration — it shows exactly what you can do with whatever object a cmdlet returns.

---

## 7. The Pipeline

The pipeline (`|`) passes the full object (not just text) from one cmdlet to the next.

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 } | Sort-Object CPU -Descending | Select-Object -First 5
```

Reading this left to right: get all processes → filter to those using more than 100 CPU seconds → sort descending by CPU → take the top 5.

### Key pipeline cmdlets
| Cmdlet | Alias | Purpose |
|---|---|---|
| `Where-Object` | `?`, `where` | Filter objects |
| `Select-Object` | `select` | Choose specific properties, or `-First`/`-Last`/`-Unique` |
| `Sort-Object` | `sort` | Sort by property |
| `Group-Object` | `group` | Group by property value |
| `ForEach-Object` | `%`, `foreach` | Run a script block per item |
| `Measure-Object` | `measure` | Count/sum/average/min/max |
| `Tee-Object` | `tee` | Split output to a file and continue pipeline |

### Filtering examples
```powershell
Get-Process | Where-Object { $_.Name -eq "chrome" }
Get-Service | Where-Object Status -eq "Running"      # simplified syntax (no $_ needed for one comparison)
Get-ChildItem | Where-Object { $_.Length -gt 1MB }
```

### Selecting & shaping output
```powershell
Get-Process | Select-Object Name, Id, CPU
Get-Process | Select-Object -First 10
Get-Process | Select-Object -Property Name, @{Name="CPU_Rounded"; Expression={[math]::Round($_.CPU,2)}}
```

### ForEach-Object
```powershell
1..5 | ForEach-Object { $_ * 2 }
Get-ChildItem *.txt | ForEach-Object { Rename-Item $_.FullName ($_.Name -replace ".txt",".bak") }
```

---

## 8. Control Flow

### If / ElseIf / Else
```powershell
$score = 85
if ($score -ge 90) {
    "A"
} elseif ($score -ge 80) {
    "B"
} else {
    "C"
}
```

### Switch
```powershell
$day = "Mon"
switch ($day) {
    "Mon" { "Start of week" }
    "Fri" { "Almost weekend" }
    default { "Midweek" }
}
```
`switch` also supports wildcards, regex, and arrays as input:
```powershell
switch -Wildcard ("report.pdf") {
    "*.pdf" { "PDF document" }
    "*.docx" { "Word document" }
}
```

### For loop
```powershell
for ($i = 0; $i -lt 5; $i++) {
    Write-Host "Iteration $i"
}
```

### ForEach loop (statement, not cmdlet)
```powershell
foreach ($item in 1..5) {
    Write-Host $item
}
```

### While / Do-While / Do-Until
```powershell
$i = 0
while ($i -lt 5) { $i++; Write-Host $i }

do { $i-- } while ($i -gt 0)

do { $i++ } until ($i -eq 5)
```

### Break / Continue
```powershell
foreach ($n in 1..10) {
    if ($n -eq 3) { continue }   # skip this iteration
    if ($n -eq 7) { break }      # exit the loop
    Write-Host $n
}
```

---

## 9. Functions

### Basic function
```powershell
function Get-Square {
    param([int]$Number)
    return $Number * $Number
}

Get-Square -Number 5   # 25
```

### Advanced function (cmdlet-like, with parameter validation)
```powershell
function Get-Greeting {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$Name,

        [ValidateSet("Formal","Casual")]
        [string]$Style = "Casual"
    )

    if ($Style -eq "Formal") {
        "Good day, $Name."
    } else {
        "Hey $Name!"
    }
}

Get-Greeting -Name "Elmor" -Style Formal
```

`[CmdletBinding()]` turns your function into an "advanced function" that supports common parameters like `-Verbose`, `-Debug`, `-ErrorAction`, and `-WhatIf` (when combined with `SupportsShouldProcess`).

### Pipeline input in functions
```powershell
function Get-Doubled {
    param(
        [Parameter(ValueFromPipeline=$true)]
        [int]$Number
    )
    process {
        $Number * 2
    }
}

1..5 | Get-Doubled
```

### Returning multiple values
```powershell
function Get-Stats {
    param([int[]]$Numbers)
    [PSCustomObject]@{
        Min = ($Numbers | Measure-Object -Minimum).Minimum
        Max = ($Numbers | Measure-Object -Maximum).Maximum
        Avg = ($Numbers | Measure-Object -Average).Average
    }
}

Get-Stats -Numbers 1,5,10,20
```

---

## 10. Arrays, Hashtables & Objects

### Arrays
```powershell
$fruits = @("Apple", "Banana", "Cherry")
$fruits[0]              # Apple
$fruits.Count           # 3
$fruits += "Mango"      # append (creates a new array under the hood)
$fruits[-1]             # last item
$fruits[1..2]           # slice: Banana, Cherry
```

### Hashtables (dictionaries)
```powershell
$person = @{ Name = "Elmor"; Role = "Engineer"; Years = 10 }
$person.Name                   # Elmor
$person["Role"]                # Engineer
$person.Add("Company","CoDev")
$person.Remove("Years")
foreach ($key in $person.Keys) { "$key = $($person[$key])" }
```

Ordered hashtable (preserves insertion order):
```powershell
$ordered = [ordered]@{ First = 1; Second = 2 }
```

### Custom objects (PSCustomObject)
This is the standard way to build structured objects in PowerShell:
```powershell
$employee = [PSCustomObject]@{
    Name   = "Elmor Cabalfin"
    Title  = "Senior Software Engineer"
    Skills = @(".NET", "Azure", "Blazor")
}

$employee.Name
$employee | Format-List
$employee | ConvertTo-Json
```

### Arrays of custom objects (like a mini-table)
```powershell
$team = @(
    [PSCustomObject]@{ Name="A"; Role="Dev" }
    [PSCustomObject]@{ Name="B"; Role="QA" }
)
$team | Format-Table -AutoSize
$team | Where-Object Role -eq "Dev"
```

---

## 11. Error Handling

### Try / Catch / Finally
```powershell
try {
    1 / 0
} catch {
    Write-Host "Error caught: $($_.Exception.Message)"
} finally {
    Write-Host "This always runs."
}
```

### Terminating vs non-terminating errors
Many cmdlets produce **non-terminating** errors by default (the script keeps going). `try/catch` only catches **terminating** errors. Force a cmdlet's errors to be terminating with `-ErrorAction Stop`:

```powershell
try {
    Get-Item "C:\DoesNotExist.txt" -ErrorAction Stop
} catch {
    "Failed: $($_.Exception.Message)"
}
```

### ErrorAction values
| Value | Behavior |
|---|---|
| `Continue` (default) | Show error, keep running |
| `SilentlyContinue` | Suppress error, keep running |
| `Stop` | Turn into a terminating error (catchable) |
| `Inquire` | Prompt the user |
| `Ignore` | Suppress and don't add to `$Error` |

### Inspecting errors
```powershell
$Error[0]                          # last error
$Error[0].Exception.Message
$Error[0].InvocationInfo.Line      # the line that failed
```

### Custom errors
```powershell
throw "Something went wrong"

# or a full error record:
Write-Error "Custom error message" -ErrorAction Stop
```

### Global error preference
```powershell
$ErrorActionPreference = "Stop"   # makes ALL cmdlets terminate on error by default
```

---

## 12. Working with Files & Folders

```powershell
# Navigate
Get-Location                          # pwd
Set-Location "C:\Projects"            # cd
Push-Location "C:\Temp"; Pop-Location # save/restore location

# List
Get-ChildItem                         # ls / dir
Get-ChildItem -Recurse -Filter *.log
Get-ChildItem -Path C:\Logs -File     # files only
Get-ChildItem -Path C:\Logs -Directory

# Create / Remove
New-Item -Path "C:\Temp\file.txt" -ItemType File
New-Item -Path "C:\Temp\NewFolder" -ItemType Directory
Remove-Item "C:\Temp\file.txt"
Remove-Item "C:\Temp\NewFolder" -Recurse -Force

# Copy / Move / Rename
Copy-Item "a.txt" "b.txt"
Copy-Item "C:\Src" "C:\Dst" -Recurse
Move-Item "a.txt" "C:\Archive\"
Rename-Item "old.txt" "new.txt"

# Test existence
Test-Path "C:\Temp\file.txt"

# Read / Write content
Get-Content "file.txt"
Get-Content "file.txt" -Raw                # whole file as one string
Get-Content "file.txt" -Tail 10            # last 10 lines (like tail)
Set-Content "file.txt" "New content"       # overwrite
Add-Content "file.txt" "Appended line"     # append
Out-File -FilePath "file.txt" -InputObject $data

# File properties
Get-Item "file.txt" | Select-Object Name, Length, LastWriteTime, CreationTime

# Hashing / integrity
Get-FileHash "file.txt" -Algorithm SHA256

# Searching content
Select-String -Path "*.log" -Pattern "ERROR"
Get-ChildItem -Recurse -Filter *.cs | Select-String "TODO"

# Zipping
Compress-Archive -Path "C:\Data\*" -DestinationPath "C:\Data.zip"
Expand-Archive -Path "C:\Data.zip" -DestinationPath "C:\Data"
```

---

## 13. Working with Processes & Services

```powershell
# Processes
Get-Process
Get-Process -Name "chrome"
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Stop-Process -Name "notepad" -Force
Start-Process "notepad.exe"
Start-Process "powershell.exe" -ArgumentList "-File C:\script.ps1" -Verb RunAs   # run elevated

# Services
Get-Service
Get-Service -Name "wuauserv"
Start-Service -Name "wuauserv"
Stop-Service -Name "wuauserv" -Force
Restart-Service -Name "wuauserv"
Set-Service -Name "wuauserv" -StartupType Automatic
Get-Service | Where-Object Status -eq "Stopped"
```

---

## 14. Working with the Registry

The registry is exposed as a PowerShell drive (`HKLM:`, `HKCU:`), so file cmdlets work on it too:

```powershell
Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion"
Get-ItemProperty -Path "HKLM:\SOFTWARE\MyApp" -Name "Version"
New-Item -Path "HKCU:\Software\MyApp"
New-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting1" -Value "True" -PropertyType String
Set-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting1" -Value "False"
Remove-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting1"
Remove-Item -Path "HKCU:\Software\MyApp" -Recurse
```

---

## 15. Networking Commands

```powershell
Test-Connection google.com                  # like ping
Test-NetConnection google.com -Port 443     # test a specific port (PS 5.1+/Windows)
Resolve-DnsName google.com                  # DNS lookup

# Web requests
Invoke-WebRequest -Uri "https://example.com" -OutFile "page.html"
Invoke-RestMethod -Uri "https://api.example.com/data" -Method Get

# REST API POST example
$body = @{ name = "Elmor"; role = "Engineer" } | ConvertTo-Json
Invoke-RestMethod -Uri "https://api.example.com/users" -Method Post -Body $body -ContentType "application/json"

# Network adapters/config
Get-NetAdapter
Get-NetIPAddress
Get-NetTCPConnection | Where-Object State -eq "Listen"
```

---

## 16. Remoting (Running Commands on Other Machines)

```powershell
# One-off command on a remote machine
Invoke-Command -ComputerName "Server01" -ScriptBlock { Get-Process }

# Interactive remote session
Enter-PSSession -ComputerName "Server01"
Exit-PSSession

# Persistent session (reusable, faster for multiple calls)
$session = New-PSSession -ComputerName "Server01"
Invoke-Command -Session $session -ScriptBlock { Get-Service }
Remove-PSSession $session

# Copy files over a session (PS 5+)
Copy-Item "C:\local.txt" -Destination "C:\remote.txt" -ToSession $session
```
Remoting requires WinRM to be enabled on the target (`Enable-PSRemoting` run as admin on the target machine).

---

## 17. Modules

Modules package reusable functions/cmdlets.

```powershell
Get-Module -ListAvailable            # modules installed on this machine
Import-Module ActiveDirectory        # load a module into the session
Get-Command -Module ActiveDirectory  # what it provides

# From the PowerShell Gallery
Find-Module -Name "Az"
Install-Module -Name "Az" -Scope CurrentUser
Update-Module -Name "Az"
Uninstall-Module -Name "Az"
```

### Writing your own module
1. Put functions in a `.psm1` file, e.g. `MyTools.psm1`.
2. Optionally add a manifest with `New-ModuleManifest -Path MyTools.psd1`.
3. `Import-Module .\MyTools.psm1` or place it under a folder on `$env:PSModulePath` so it auto-loads by name.

```powershell
# MyTools.psm1
function Get-Hello { "Hello from my module!" }
Export-ModuleMember -Function Get-Hello
```

---

## 18. Scheduled Tasks & Automation

```powershell
# Create a scheduled task that runs a script daily at 8 AM
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-File C:\Scripts\daily.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At 8am
Register-ScheduledTask -TaskName "DailyReport" -Action $action -Trigger $trigger -User "SYSTEM"

# Manage tasks
Get-ScheduledTask -TaskName "DailyReport"
Start-ScheduledTask -TaskName "DailyReport"
Disable-ScheduledTask -TaskName "DailyReport"
Unregister-ScheduledTask -TaskName "DailyReport" -Confirm:$false
```

### Background jobs (run something async in the same session)
```powershell
$job = Start-Job -ScriptBlock { Start-Sleep 5; "Done" }
Get-Job
Receive-Job -Job $job -Wait
Remove-Job -Job $job
```

---

## 19. Security & Execution Policy

Execution policy controls whether scripts are allowed to run (not a full security boundary, but a safety guard rail):

```powershell
Get-ExecutionPolicy
Get-ExecutionPolicy -List
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

| Policy | Meaning |
|---|---|
| `Restricted` | No scripts allowed (Windows default) |
| `AllSigned` | Only digitally signed scripts run |
| `RemoteSigned` | Local scripts run freely; downloaded scripts must be signed |
| `Unrestricted` | All scripts run, warns for downloaded ones |
| `Bypass` | Nothing blocked, no warnings |

### Credentials
```powershell
$cred = Get-Credential                       # prompts securely for user/password
Invoke-Command -ComputerName Server01 -Credential $cred -ScriptBlock { Get-Service }

# Secure strings (avoid plaintext passwords in scripts)
$securePwd = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("username",$securePwd)
```

### Running as Administrator
```powershell
Start-Process powershell -Verb RunAs
```
Or check inside a script:
```powershell
$isAdmin = ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
if (-not $isAdmin) { Write-Warning "Please run as Administrator" }
```

### Script signing
```powershell
$cert = Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert
Set-AuthenticodeSignature -FilePath .\script.ps1 -Certificate $cert
```

---

## 20. Working with CSV, JSON & XML

### CSV
```powershell
$data = Import-Csv "employees.csv"
$data | Where-Object Department -eq "IT"
$data | Export-Csv "output.csv" -NoTypeInformation
```

### JSON
```powershell
$json = Get-Content "data.json" -Raw | ConvertFrom-Json
$json.name

$obj = [PSCustomObject]@{ Name="Elmor"; Role="Engineer" }
$obj | ConvertTo-Json | Out-File "output.json"
```

### XML
```powershell
[xml]$xml = Get-Content "data.xml"
$xml.Root.Item.Name

$data | Export-Clixml "data.xml"      # PowerShell's own serialized format (preserves object types)
$restored = Import-Clixml "data.xml"
```

---

## 21. Common Use Cases (End-to-End Scripts)

### 21.1 Bulk-rename files
```powershell
Get-ChildItem -Path "C:\Photos" -Filter "*.jpeg" | ForEach-Object {
    Rename-Item -Path $_.FullName -NewName ($_.BaseName + ".jpg")
}
```

### 21.2 Find and delete files older than 30 days
```powershell
Get-ChildItem "C:\Logs" -Filter *.log |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item -Force
```

### 21.3 Monitor disk space and alert
```powershell
Get-CimInstance Win32_LogicalDisk -Filter "DriveType=3" | ForEach-Object {
    $freePct = [math]::Round(($_.FreeSpace / $_.Size) * 100, 1)
    if ($freePct -lt 10) {
        Write-Warning "$($_.DeviceID) is low on space: $freePct% free"
    }
}
```

### 21.4 Bulk-create Active Directory users from a CSV
```powershell
Import-Module ActiveDirectory
Import-Csv "newusers.csv" | ForEach-Object {
    New-ADUser -Name $_.FullName -SamAccountName $_.Username `
        -UserPrincipalName "$($_.Username)@corp.local" `
        -Path "OU=Employees,DC=corp,DC=local" -Enabled $true `
        -AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -Force)
}
```

### 21.5 Automated backup of a folder with a timestamped zip
```powershell
$source = "C:\Data"
$dest   = "C:\Backups\Data_$(Get-Date -Format 'yyyyMMdd_HHmmss').zip"
Compress-Archive -Path "$source\*" -DestinationPath $dest
Write-Host "Backup created: $dest"
```

### 21.6 Parse an IIS/app log for errors and export a report
```powershell
Get-Content "app.log" |
    Select-String "ERROR" |
    ForEach-Object { $_.Line } |
    Out-File "errors_report.txt"
```

### 21.7 Check a list of servers for a given service status
```powershell
$servers = "Server01","Server02","Server03"
foreach ($s in $servers) {
    try {
        $svc = Invoke-Command -ComputerName $s -ScriptBlock { Get-Service -Name "Spooler" } -ErrorAction Stop
        [PSCustomObject]@{ Server=$s; Status=$svc.Status }
    } catch {
        [PSCustomObject]@{ Server=$s; Status="Unreachable" }
    }
} | Format-Table -AutoSize
```

### 21.8 Simple REST API health check with retries
```powershell
$url = "https://api.example.com/health"
$maxRetries = 3
for ($i = 1; $i -le $maxRetries; $i++) {
    try {
        $resp = Invoke-RestMethod -Uri $url -TimeoutSec 5
        Write-Host "Healthy: $($resp.status)"
        break
    } catch {
        Write-Warning "Attempt $i failed: $($_.Exception.Message)"
        Start-Sleep -Seconds 5
    }
}
```

### 21.9 Convert Excel-style CSV export into JSON for an API
```powershell
Import-Csv "products.csv" | ConvertTo-Json -Depth 3 | Out-File "products.json" -Encoding UTF8
```

### 21.10 A reusable logging function pattern for scripts
```powershell
function Write-Log {
    param([string]$Message, [string]$Level = "INFO")
    $line = "[{0}] [{1}] {2}" -f (Get-Date -Format "yyyy-MM-dd HH:mm:ss"), $Level, $Message
    Add-Content -Path "C:\Logs\script.log" -Value $line
    Write-Host $line
}

Write-Log "Script started"
try {
    # ... work ...
    Write-Log "Step completed"
} catch {
    Write-Log $_.Exception.Message -Level "ERROR"
}
```

---

## 22. Best Practices

- **Use full cmdlet names in scripts** (`Get-ChildItem`, not `gci` or `ls`) — aliases are fine interactively but hurt readability and portability in saved scripts.
- **Use approved verbs** (`Get-Verb`) for your own functions so they behave predictably.
- **Always add comment-based help** to functions/scripts (`<# .SYNOPSIS .DESCRIPTION .PARAMETER .EXAMPLE #>`) so `Get-Help` works on them.
- **Use `[CmdletBinding()]` and typed parameters** with validation attributes (`ValidateSet`, `ValidateRange`, `Mandatory`) instead of manually checking inputs.
- **Prefer `try/catch` with `-ErrorAction Stop`** over letting errors silently pass.
- **Avoid `Write-Host` for data output** — it can't be captured/piped. Use `Write-Output` (or just let the value fall through) for data, and `Write-Host`/`Write-Verbose`/`Write-Warning` only for console-only messages.
- **Use `-WhatIf` / `-Confirm`** (via `SupportsShouldProcess`) on any function that changes or deletes something, so users can preview effects.
- **Don't hardcode credentials.** Use `Get-Credential`, secure strings, or a secrets vault/module.
- **Test scripts with `-WhatIf` and small datasets** before running against production.
- **Use `Set-StrictMode -Version Latest`** during development to catch typos in variable/property names early.
- **Version-control scripts** (git) and keep reusable logic in modules rather than copy-pasting between scripts.
- **Run PSScriptAnalyzer** (`Invoke-ScriptAnalyzer`) to lint scripts for style and common mistakes.

---

## 23. Quick Reference Cheat Sheet

| Task | Command |
|---|---|
| List files | `Get-ChildItem` |
| Change directory | `Set-Location` |
| Read file | `Get-Content` |
| Write file | `Set-Content` / `Add-Content` |
| Copy/Move/Delete | `Copy-Item` / `Move-Item` / `Remove-Item` |
| List processes | `Get-Process` |
| Kill process | `Stop-Process` |
| List/start/stop services | `Get-Service` / `Start-Service` / `Stop-Service` |
| Filter pipeline | `Where-Object` |
| Reshape output | `Select-Object` |
| Sort | `Sort-Object` |
| Group | `Group-Object` |
| Loop over items | `ForEach-Object` / `foreach` |
| Get object structure | `Get-Member` |
| Run remote command | `Invoke-Command` |
| Web request | `Invoke-WebRequest` / `Invoke-RestMethod` |
| Import/Export CSV | `Import-Csv` / `Export-Csv` |
| Convert to/from JSON | `ConvertTo-Json` / `ConvertFrom-Json` |
| Help for a command | `Get-Help <cmdlet> -Examples` |
| Discover commands | `Get-Command` |
| Check execution policy | `Get-ExecutionPolicy` |
| Run script | `.\script.ps1` |

---

*This guide covers the core PowerShell language and the most commonly used cmdlets across file, system, network, and automation scenarios. For deep dives on any single area (Active Directory, Azure/Az module, DSC, or a specific script you're building), ask and it can be expanded into its own section.*
