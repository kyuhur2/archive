## display git branches

### pre-set

- allow powershell to run scripts
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

- open powershell profile
```powershell
if (!(Test-Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

notepad $PROFILE
```

### script

```powershell
# Get the current Git branch
function Get-GitBranch {
    $branch = git rev-parse --abbrev-ref HEAD 2>$null

    if ($LASTEXITCODE -eq 0) {
        return $branch
    }

    return $null
}

# Get the root folder name of the current Git repository
function Get-GitRootFolder {
    $root = git rev-parse --show-toplevel 2>$null

    if ($LASTEXITCODE -eq 0 -and $root) {
        return Split-Path $root -Leaf
    }

    return $null
}

# Get the current path relative to the Git repository root
function Get-GitRelativePath {
    $relativePath = git rev-parse --show-prefix 2>$null

    if ($LASTEXITCODE -eq 0) {
        return $relativePath.TrimEnd("/")
    }

    return $null
}

# Get the current Python virtual environment name
function Get-VenvName {
    if ($env:VIRTUAL_ENV) {
        return Split-Path $env:VIRTUAL_ENV -Leaf
    }

    if ($env:CONDA_DEFAULT_ENV) {
        return $env:CONDA_DEFAULT_ENV
    }

    return $null
}

# Custom PowerShell prompt
function prompt {
    $gitBranch       = Get-GitBranch
    $gitRootFolder   = Get-GitRootFolder
    $gitRelativePath = Get-GitRelativePath
    $venv            = Get-VenvName

    $username = $env:USERNAME
    $hostname = $env:COMPUTERNAME

    if ($gitRootFolder) {
        if ($gitRelativePath) {
            $displayPath = "$gitRootFolder/$gitRelativePath"
        }
        else {
            $displayPath = $gitRootFolder
        }
    }
    else {
        $displayPath = $PWD.Path
    }

    # Virtual environment
    if ($venv) {
        Write-Host "($venv) " -ForegroundColor Magenta -NoNewline
    }

    # Username and computer name
    Write-Host "[" -NoNewline
    Write-Host $username -ForegroundColor Green -NoNewline
    Write-Host "@" -NoNewline
    Write-Host $hostname -ForegroundColor Cyan -NoNewline
    Write-Host "] " -NoNewline

    # Current directory
    Write-Host "[$displayPath] " -ForegroundColor Blue -NoNewline

    # Git branch
    if ($gitBranch) {
        Write-Host "($gitBranch)" -ForegroundColor Red -NoNewline
    }
    else {
        Write-Host "()" -ForegroundColor DarkGray -NoNewline
    }

    return " `$ "
}
```