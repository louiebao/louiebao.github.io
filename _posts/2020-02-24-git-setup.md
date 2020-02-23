### Install Git

[https://git-scm.com/download/win](https://git-scm.com/download/win)

### Setup .gitconfig

[core]
  autocrlf = false # Check in and check out the files exactly as they are
	ignorecase = true
	sshCommand = "ssh -v"

[alias]
    l = log \"--pretty=format:%C(yellow bold)%h %C(red bold)%ad %C(white bold)%an %C(reset)%s%C(cyan bold)%d\" --all --decorate --date=short
    la = log --author="louie" \"--pretty=format:%C(yellow bold)%h %C(red bold)%ad %C(white bold)%an %C(reset)%s%C(cyan bold)%d\" --all --decorate --date=short
    h = log --follow \"--pretty=format:%C(yellow bold)%h %C(red bold)%ad %C(white bold)%an %C(reset)%s%C(cyan bold)%d\" --all --decorate --date=short
    f = show --name-status \"--format=%C(yellow bold)%h %C(red bold)%ad %C(white bold)%an %C(reset)%s%C(cyan bold)%d\" --date=short
    deleted = log --diff-filter=D --summary \"--pretty=format:%C(yellow bold)%h %C(red bold)%ad %C(white bold)%an %C(reset)%s%C(cyan bold)%d\" --date=short

    s = status
    a = add -A
    b = branch
    dif = difftool --cached --dir-diff
	  bdif = difftool --dir-diff
    sl = shortlog -e -s -n --all --no-merges   

### Setup command prompt

Configure command prompt shortcut:
- Target: %windir%\system32\cmd.exe /K "cd c:\dev & powershell"

Configure command prompt Options:
- Tick QuickEdit mode
- Tick Insert mode

### Setup poshgit

Run command to install posh-git:
> PowerShellGet\Install-Module posh-git -Scope CurrentUser -Force

Import-Module posh-git
$GitPromptSettings.DefaultPromptPrefix = '$(Get-Date -f "ddd, dd MMM yyyy hh:mm:ss tt")`r`n'

### Setup git Pre-Commit hook

Run command:
> Set-Content "C:\dev\xxx\.git\hooks\pre-commit" "#!C:/Program\ Files/Git/usr/bin/sh.exe`r`nexec powershell.exe -File ""C:\dev\xxx\Check-Code-Blah.ps1""`r`nexit"

