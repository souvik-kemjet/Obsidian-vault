
```bash
# 1. Create a personal bin folder

$binDir = "$env:USERPROFILE\bin"

New-Item -ItemType Directory -Force -Path $binDir | Out-Null

  

# 2. Create use-madhu.bat

Set-Content -Path "$binDir\use-madhu.bat" -Value @'

@echo off

git config --global [user.name](http://user.name) "Madeshwari M"

git config --global user.email "madeshwari@kemjethimalog.com"

gh auth switch --user Madeshwari-M

echo Switched to Madhu

'@

  

# 3. Create use-keerthu.bat

Set-Content -Path "$binDir\use-keerthu.bat" -Value @'

@echo off

git config --global [user.name](http://user.name) "Keerthaveni"

git config --global user.email "keerthaveni@kemjethimalog.com"

gh auth switch --user keerthaveni-r

echo Switched to Keerthu

'@

  

# 4. Add the folder to your permanent Windows PATH

$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")

if ($currentPath -notlike "$binDir") {

    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$binDir", "User")

}

Write-Host "Setup complete! Restart your terminals." -ForegroundColor Green
```