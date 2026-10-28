# WSL + GNU GCC
This environment is designed to sidestep Microsoft licensing requirements by using the GNU GCC toolchain
rather than the msvc toolchain.

## WSL setup
```Powershell
# Ensure the script is running elevated
if (-not ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole(
    [Security.Principal.WindowsBuiltInRole]::Administrator
)) {
    Write-Error 'This script must be run from an elevated PowerShell session.'
    exit 1
}

# Preferred method
wsl --install

# Fallback if WSL install fails
if ($LASTEXITCODE -ne 0) {
    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux -NoRestart
    Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
}

Restart-Computer
```
## Install ubuntu
```Powershell
$UbuntuInstalled = wsl --list --quiet 2>$null |
    ForEach-Object { $_.Trim() } |
    Where-Object { $_ -eq 'Ubuntu' }

if ("Ubuntu" -ne $UbuntuInstalled) {
    wsl --install --distribution Ubuntu
} else {
    Write-Host 'Ubuntu is already installed.'
}
```

## Open ubuntu
```Powershell
wsl -d Ubuntu 
```

## Install rustc 
[Rust install instructions]https://rust-lang.org/tools/install/
```Shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### Update the PATH
```Shell
 . "$HOME/.cargo/env"
```

### Add the GNU GCC build target
```Shell
rustup target add x86_64-pc-windows-gnu
```

### Validate target is installed
```Shell
rustup target list --installed
```

## Build with the GNU GCC target
```Shell
cargo build --release --target x86_64-pc-windows-gnu
```
