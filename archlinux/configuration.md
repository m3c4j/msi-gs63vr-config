### Configuration

#### zsh config

##### Install zsh

```bash
sudo pacman -S zsh
```

##### Install Oh My ZSH!

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

https://ohmyz.sh/

##### Install theme

```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/skylerlee/zeta-zsh-theme/master/scripts/install.sh)"
```

https://github.com/skylerlee/zeta-zsh-theme

##### Install plugins

###### autojump

```shell
# arch
yay -S autojump

# centos
sudo yum install autojump-zsh
```

https://blog.csdn.net/I_lost/article/details/85219186

###### zsh-syntax-highlighting

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

###### zsh-autosuggestions

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

##### Enable Oh My ZSH!

```shell
cp $ZSH/templates/zshrc.zsh-template ~/.zshrc

vi ~/.zshrc
ZSH_THEME="zeta"
# z 命令快速跳转目录
# extract x 命令解压一切文件
# wd 常用目录切换工具
# wd add web
# 这个命令相当于给当前目录做了一个标识，标识名叫做 web ，我们下次如果再想进入这个目录，只需输入：
# wd web
# 这样就可以完成目录切换了，非常方便。
plugins=(autojump extract git wd z zsh-syntax-highlighting zsh-autosuggestions)
```



```shell
which zsh
# /usr/bin/zsh
sudo vi /etc/passwd
# username:/usr/bin/zsh
```

open a new terminal to see effects

#### theme

```bash
yay -S mojave-ct-icon-theme mojave-gtk-theme-git mcmojave-cursors-git

# use tweak to apply
```



#### msi keyboard led

https://github.com/Sayrus/msi-keyboard

```bash
yay -S msi-keyboard-git

msi-keyboard -m normal -c left,red,high -c middle,green,high -c right,blue,high -r touchpad,255,0,0 -r logo,255,255,255
```

```bash
# fix no permission problem, when autostart
sudo vi /usr/lib/udev/rules.d/99-msi.rules
SUBSYSTEM=="usb", ATTRS{idVendor}=="1770", ATTRS{idProduct}=="ff00", MODE="0666"
```

```bash
# auto start
sudo vi /usr/lib/systemd/system/msi-keyboard-led.service
[Unit]
Description=MSI Keyboard LED service

[Service]
Type=oneshot
# RemainAfterExit=yes
ExecStart=/usr/bin/msi-keyboard -m normal -c left,red,high -c middle,green,high -c right,blue,high -r touchpad,255,0,0 -r logo,255,255,255
ExecStop=/usr/bin/msi-keyboard -m normal -c left,red,high -c middle,green,high -c right,blue,high -r touchpad,255,0,0 -r logo,255,255,255

[Install]
WantedBy=sysinit.target
WantedBy=suspend.target
```

```bash
sudo systemctl enable msi-keyboard-led
```



maybe using gui version

https://github.com/stevelacy/msi-keyboard-gui



#### touchpad

```bash
yay -S libinput-gestures ydotool
```

```bash
cp /etc/libinput-gestures.conf ~/.config/libinput-gestures.conf

vi ~/.config/libinput-gestures.conf
gesture swipe up        4   _internal ws_up
gesture swipe down      4   _internal ws_down

gesture swipe left  ydotool key alt+Right
gesture swipe right ydotool key alt+Left

gesture swipe up        4   ydotool key super
gesture swipe down      4   ydotool key super
gesture swipe left      4   ydotool key alt+super+right
gesture swipe right     4   ydotool key alt+super+left

gesture swipe up        3   ydotool key super+Up
gesture swipe down      3   ydotool key super+Down
gesture swipe left      3   ydotool key super+Left
gesture swipe right     3   ydotool key super+Right

gesture swipe right_up      3   ydotool key super+d
gesture swipe left_up       3   ydotool key control+shift+Tab
gesture swipe left_down     3   ydotool key alt+F4
gesture swipe right_down    3   ydotool key super+h

gesture pinch in    ydotool key ctrl+-
gesture pinch out   ydotool key ctrl+shift+=
```

```bash
libinput-gestures-setup restart
```



#### disable NVIDIA dedicated gpu

most of time, the dedicated gpu is not needed, when in kernel version 4.x.x, it's possible to set it off using bbswitch or acpi_call. Now with kernel version 5.x.x, it's hard to do so, for linux boot strucks right before going into desktop manager.

Luckly, in msi, there is a way to set iGPU to the primary display.

When boot, press **DELETE** boot into BIOS

then hold **ALT (left) + CTRL (right) + SHIFT (right) + F2** display advance options (**sequence matters**).

go to **Advanced** tab, and go inside **System Agent (SA) Configuration**,

then go inside **Graphics Configuration**,

change **Primary Display** from **SG (Switch Graphics)** to **IGFX**,

then press **F10** save and reset.



if you need change other stuff (**DO NOT DO IT**), **with cautious**



https://github.com/geminis3/nvidia-gpu-off

```bash
# disable dedicated gpu in linux
sudo pacman -Rsc nvidia-dkms nvidia-settings nvidia-utils

lspci | grep NVIDIA

sudo pacman -S acpi_call-dkms
sudo modprobe acpi_call

sudo /usr/share/doc/acpi-call-dkms/examples/turn_off_gpu.sh
# Trying \_SB.PCI0.PEG0.PEGP._OFF: works!

sudo vi /etc/modprobe.d/blacklist.conf
blacklist nouveau

sudo vi /etc/tmpfiles.d/acpi_call.conf
w /proc/acpi/call - - - - \\_SB.PCI0.PEG0.PEGP._OFF

# sudo update-initramfs -u -k all
sudo mkinitcpio -P

sudo su
echo 1 > /sys/bus/pci/devices/0000\:01\:00.0/remove
echo 1 > /sys/bus/pci/devices/0000\:01\:00.1/remove

# persistence
sudo vi /etc/tmpfiles.d/remove_gpu_from_lspci.conf
w /sys/bus/pci/devices/0000\:01\:00.0/remove - - - - 1
w /sys/bus/pci/devices/0000\:01\:00.1/remove - - - - 1

# using powertop to check power consumption
powertop
```



#### logitech Go Pro Wireless

```bash
# piper gui
yay -S piper
```



#### add ip address

```bash
sudo ip addr add 118.31.14.196/24 dev enp61s0
```





