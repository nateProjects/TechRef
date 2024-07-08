# Ubuntu On Windows

## Install WSL - Linux Within Windows

Within Powershell (run as Administrator) type -

``wsl --install``

Note: On older versions - ``wsl --set-default-version 2``

You may need to install as the local Admin account initially, then as your user

By default this will install Ubuntu Linux

```
wsl --list --online → To see a list of available distros.  
wsl --install -d Debian → To install Debian distro  
```

Microsoft's Guide is here -

[https://docs.microsoft.com/en-us/windows/wsl/install](https://docs.microsoft.com/en-us/windows/wsl/install)) / Ubuntu’s Guide is [here]([https://ubuntu.com/wsl](https://ubuntu.com/wsl)

Note: You can launch it by searching or selecting Ubuntu from the Applications menu

## Update Linux

``wsl -l -v ``

-> list your installed Linux distributions and check the version of WSL each is set to.

``wsl -d Debian ``

-> To run a specific wsl distribution from within PowerShell or Windows Command Prompt without changing your default distribution.

Open Your Linux Terminal & Update Linux(Ubuntu or Debian) using ``wsl -d Debian`` or ``wsl -d Ubuntu``

``sudo apt update && sudo apt upgrade``

Install Build Essentials

``sudo apt install build-essential``

Install Git

``sudo apt install git``

Configure Git

```
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"
```

Install Python

Ubuntu may already have Python installed -

``python3 --version``

To install Python -

``sudo apt install python3``

### WSL LAMP

```
sudo apt-get install lamp-server^
echo -e 'Servername localhost\nAcceptFilter http none' | sudo tee -a /etc/apache2/apache2.conf
cd /etc/apache2/sites-available/  
sudo nano test.local.conf

<VirtualHost *:80>
      ServerAdmin webmaster@localhost
      ServerName CGIWIN211
      ServerAlias *.CGIWIN211

      DocumentRoot /var/www/html

      <Directory />
               Options FollowSymLinks
               AllowOverride None
      </Directory>

      <Directory /var/www/html/>
               Options Indexes FollowSymlinks MultiViews
               AllowOverride All
#               Order allow,deny
#               Allow from all
               Require all granted
      </Directory>
</VirtualHost>

sudo a2ensite test.local.conf  
sudo service apache2 reload
```

## Use Chocolatey for Windows Packages

If you would like to install Windows apps as packages (makes it easier to script and update) -

In Powershell run Get-ExecutionPolicy. If it returns Restricted, then run Set-ExecutionPolicy AllSigned or Set-ExecutionPolicy Bypass -Scope Process if that doesn't work

Then run the following to install -

```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = 
[System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object 
System.Net.WebClient).DownloadString('[https://community.chocolatey.org/install.ps1'](https://community.chocolatey.org/install.ps1')))
```

To install the GUI -

``choco install chocolateygui``

To update run -

``choco upgrade chocolatey``

## Use VS Code with WSL

[https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode))

Install Remote Desktop Essentials

``choco install microsoft-windows-terminal``

``sudo ln -s '/mnt/c/Program Files/Microsoft VS Code/Code.exe' /usr/local/bin/code``

Note: VSCodium WSL integration is basic / whereas VSCode will auto-install extentions on

``code . ``

the first time

## Backup

Local Network - //wsl.localhost/Ubuntu

Find location - `Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss" -Recurse`

```
C:\Users\USERNAME\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_*\LocalState\*.vhdx
```

https://github.com/andrearaponi/wslSnappy

## Homebrew Install

If useful -
```
# If need install Git 2.7.0 or higher first

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
test -d ~/.linuxbrew && eval "$(~/.linuxbrew/bin/brew shellenv)"
test -d /home/linuxbrew/.linuxbrew && eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >> ~/.bashrc

# if needed change /bin/brew in .bashrc to /home/linuxbrew/.linuxbrew/bin/brew
# if needed - brew install gcc
```

## RSAT (AD Tools) Install

See - [https://woshub.com/install-rsat-feature-windows-10-powershell/](https://woshub.com/install-rsat-feature-windows-10-powershell/)

```
$currentWU = Get-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "UseWUServer" | select -ExpandProperty UseWUServer  
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "UseWUServer" -Value 0  
Restart-Service wuauserv  
Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability –Online  
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "UseWUServer" -Value $currentWU  
Restart-Service wuauserv -force
```