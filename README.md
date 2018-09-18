# Lenovo Ideapad 310 - 15ISK

> Hello and welcome to this guide about making lenovo ip310 laptop triple boot and make it ready for coding and developing (some parts are optional for you, like installing specific TextEditor or Package manager or etc)

## Requirements

> All needed files for this guide are in folders named `Windows`, `Linux`, `Mac`

* OS `.iso`, `.dmg` file linkes are in `./{OS}/os.link`
* Back up your important files in a safe place

## Bootable USB's

* First download all OS'es from `./{OS}/os.link`

### Windows Bootable USB

> You need a Windows OS for making bootable flash using `Rufus` (or Linux, or Mac)

1. Open Rufus
2. From Device, select you USB device
3. Select Windows `.iso` file
4. Set partition scheme to `GPT`
5. Set target system to `UEFI`
6. Click Start

### Linux Bootable USB

> You need a Windows OS for making bootable flash using `Rufus` (or Linux, or Mac)

1. Open Rufus
2. From Device, select you USB device
3. Select Linux `.iso` file
4. Set partition scheme to `GPT`
5. Set target system to `UEFI`
6. Click `Start`

### Mac Bootable USB

> You need a Mac OS for making bootable flash using `Unibeast` (or Windows `TransMac`, or Linux `dmg2img + dd + mkfs` )

* You can run macOS from `VMWare` and make your bootable flash if you haven't any macOS

1. Open `Disk Utility` application from `Launchpad/Other/Disk Utility`
2. Click you USB device and click `Erase`
3. Set to `GUID` + `JHFS+`
4. Format USB device
5. Open  Mac `.dmg` file
6. Copy `Mac Installer` to `Applications` folder
7. Open `Unibeast` 
8. Goto `Destination Select` and select your USB device
9. Goto `Installation` and click `Install`
10. After installing completes, open `CloverConfigurer`
11. Goto `Mount EFI` section and mount your USB device EFI partition
12. Click `Open Partition` and goto `EFI/CLOVER/config.plist`, double click to open file with `CloverConfigurer`
13. Goto `Graphics` section and check `Inject Intel`, and set `ig-platform-id` to `0x12345678` (Fake ID)
14. Goto `SMBIOS` sectopms and set `Memory Channels` combo box to `Dual Channel`, and set `SlotCount` to `1`
15. Click `Save`
16. Unmount your USB device EFI partition
17. Exit `CloverConfigurer`

## Installing

> Now it's time to going to installing OS'es, first Windows and then Linux and then Mac

* Connect you USB device and boot it from `UEFI` mode

### Windows 10 Installation

1. Open `CMD` and run `diskpart` and do these things
    ```code
    list disk
    select disk <hard disk number>
    clean
    convert gpt
    exit
    ```
2. Open installer partitioner and create `300GB` partition and format for Windows
3. Back to `CMD` and rerun `diskpart` and do these things
    1. Remove `Windows Recovery` partition and `ESP (EFI Partition)`
    2. Create new `ESP (EFI Partition)` with size of 600MB
    3. Back to installer partitioner
4. Install Windows

### GNU/Linux Installation

1. Goto paritioner
2. Create 4GB swap
3. Create 396GB root
4. Set grub installation to `/dev/sda` (Hard drive)
5. Install Linux
6. Boot installed linux (For adding `macOS Clover Bootloader` chainloader item to `GRUB2`)
7. Open terminal and do these things
    1. Open `/etc/grub.d/40_custom` with TextEditor (or lovely `VI`) :D
    2. Add these lines at the end of file
        ```code
        menuentry{

        }
        ```
8. Run `sudo update-grub`

### macOS Sierra 10.12.6 Installation

1. Open `Disk Utility`
2. Format 300GB partition as `JHFS+` format
3. Install macOS on new partition
4. Don't eject USB device
5. Reboot after installation
6. Boot flash `Clover Bootloader` and then boot `macOS` installed from that
7. Complete installation
8. Open `Multibeast` and check these items
    1. `Drivers/Audio/Universal/VoodooHDA v2.8.9`
    2. `Drivers/Disk/Intel Generaic AHCI SATA`
    3. `Drivers/Misc/*`
    4. `Drivers/Misc/VoodooTSync 4 Core`
    5. `Drivers/Network/Atheros/AtherosE2200Ethernet v2.2.1`
    6. `Drivers/USB/3rd Party USB 3.0`
    7. `Bootloader/Clover v2.4 * UEFI + Emulated NVRAM`
    8. `Customize/Graphics Configuration/Intel HD 5xx`
    9. `Customize/Graphics Configuration/Nvidia Web Drivers Boot Flag`
9. From Build menu click Install
10. Now Open `Clover Configurer` and do these items
    1. Goto `Mount EFI` section and mount your Hard device EFI partition
    2. Click `Open Partition` and goto `EFI/CLOVER/config.plist`, double click to open file with `CloverConfigurer`
    3. Goto `Graphics` section and check `Inject Intel`, and set `ig-platform-id` to `0x12345678` (Fake ID)
    4. Goto `SMBIOS` sectopms and set `Memory Channels` combo box to `Dual Channel`, and set `SlotCount` to `1`
    5. Click `Save`
    6. Unmount your USB device EFI partition
11. Run `sudo kextcache -i /` (remove link file if error 17 shows)
12. __TODO__: GPU, Audio, PS2, Battery, HDMI, Brightness

## Customizing

### Windows 10 Customizing

1. Install Lenovo Drivers (download from lenovo official site)
2. Install Microsoft Office
3. Install Adobe XD
4. Install Telegram
5. Install Chocolaty package manager
6. Run `choco install googlechrome winrar mpv cmake git nvm Bitnami-XAMPP --installargs '"ADD_CMAKE_TO_PATH=System"'`
7. Install UGet
8. Install MinGW
9. Install Java-JDK-8
10. Install VSCode + Settings Sync(token + gistid)

### GNU/Linux Customizing

1. Run `chmod 0777 /opt`
2. Change Repository to Main Servers
3. Run `sudo apt install tor polipo`
4. Edit file `/etc/systemd/system/multi-user.target.wants/tor.service` 
    ```code
    [Unit]
    Description=Anonymizing overlay network for TCP (multi-instance-master)

    [Service]
    User=debian-tor
    Type=simple
    RemainAfterExit=yes
    ExecStart=/usr/bin/tor -f /etc/tor/torrc
    ExecReload=/usr/bin/kill -HUP $MAINPID
    KillSignal=SIGINT
    LimitNOFILE=8192
    PrivateDevices=yes

    [Install]
    WantedBy=multi-user.target
    ```
5. Edit file `/etc/polipo/config`
    ```code
    gSyslog = true
    logFile = /var/log/polipo/polipo.log
    socksParentProxy = "localhost:9050"
    socksProxyType = socks5
    ```
6. Run
    ```code
    sudo systemctl daemon-reload
    sudo systemctl restart tor.service
    sudo systemctl restart polipo.service

    sudo torsocks apt-add-repository ppa:fixnix/netspeed
    sudo torsocks apt-add-repository ppa:tista/adapta
    sudo torsocks apt-add-repository ppa:papirus/papirus
    sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
    sudo curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'

    sudo torsocks apt update
    sudo torsocks apt upgrade

    nvm install node

    sudo torsocks apt install code uget mpv audacious apt-transport-https ca-certificates curl software-properties-common guake chromium-browser indicator-multiload fish adapta-gtk-theme papirus-icon-theme docker.io gcc clang cmake erlang openjdk-8-jdk git lamp-server^

    torsocks curl -L github.com/oh-my-fish/oh-my-fish/raw/master/bin/install | fish

    fish

    omf install budspencer

    sudo ubuntu-drivers autoinstall
    ```
7. Install VSCode + Settings Sync(token + gistid)
8. Config desktop theme (`papirus`, `adapta`)
9. Set default shell to `fish`
10. Auto start `guake`
11. Config desktop panel

### macOS Sierra 10.12.6 Customizing

1. Install `HoRNDIS-9.2.pkg` for USB tethering
2. Install Adobe XD
3. Install Telegram
4. Install Homebrew package manager
5. Run
    ```code
    brew install cmake nvm fish tor polipo git
    brew cask install google-chrome mpv
    ```
6. Install Java-JDK-8
7. Install Clang
8. Install Mamp Server
9. Install Docker
10. Install VSCode + Settings Sync(token + gistid)
11. Map keyboard(`command`->`control`, `control`->`command`)