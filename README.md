# PersonalArchLinuxInstallGuide
1.	Preparación del medio de instalación

	`# dd bs=4M if=Archlinux_2020.10.01_x86-64.iso of=/dev/sdc status=progress oflag=sync`
  
2.	Arrancar desde el medio de instalación

3.	Listar configuraciones de teclado disponibles

	`# ls /usr/share/kbd/keymaps/**/*.map.gz`
  
4.	Establecer la distribución de teclado

	`# loadkeys la-latin1`
  
5.	Verificar el modo de arranque

	`# ls /sys/firmware/efi/efivars`
  
6.	Verificar estado del adaptador inalámbrico

	`# ip link`
  
7.	Conectarse a la red Wi-Fi
	1.	Iniciar la utilidad iwctl
  
		`# iwctl`
    
	2.	Listar los adaptadores disponibles
  
		`# device list`
    
	3.	Escanear redes para el adaptador
  
		`# station wlan0 get-networks`
    
	4.	Conectar a una red
  
		`# station wlan0 connect NETWORK-SSID`
    
	5.	Salir de la utilidad
  
		`# exit`
    
8.	Comprobar la conexión

	`# ping archlinux.org`
  
9.	Actualizar el reloj

	`# timedatectl set-ntp true`
  
10.	Listar los dispositivos y las particiones

	`# fdisk -l`
  
11.	Preparar las particiones con fdisk
	1.	Abrir fdisk en el dispositivo de almacenamiento
  
		`# fdisk /dev/sdX(LetraDispositivo)`
    
	2.	Listar las particiones
  
		`p`
    
	3.	Cambiar el tipo de partición a Linux
  
		`t X(NumeroParticion) 83`
    
	4.	Cambiar el tipo de partición a Swap
  
		`t X(NumeroParticion) 82`
    
	5.	Marcar partición como de arranque
  
		`a X(NumeroParticion)`
    
	6.	Escribir los cambios
  
		`w`
    
12.	Formatear la partición root (/) como ext4

	`# mkfs.ext4 -f /dev/sdX(LetraDispositivo)Y(NumeroParticion)`
  
13.	Formatear la partición de intercambio como swap

	`# mkswap /dev/sdX(LetraDispositivo)Y(NumeroParticion)`
  
14.	Montar la partición root a /mnt

	`# mount /dev/sdX(LetraDispositivo)Y(NumeroParticion) /mnt`
  
15.	Activar la partición Swap

	`# swapon /dev/sdX(LetraDispositivo)Y(NumeroParticion)`

16.	Montar la partición EFI a /mnt/boot/efi (o /mnt/efi)

	`# mount /dev/sdX(LetraDispositivo)Y(NumeroParticion) /mnt/boot/efi`
  
17.	Actualizar la lista con los 5 mejores servidores

	`# reflector --verbose --latest 5 --sort rate --save /etc/pacman.d/mirrorlist`
  
18.	Instalar los paquetes esenciales

	`# pacstrap /mnt base linux-lts linux-firmware nano iproute2 dhcpcd wpa_supplicant networkmanager grub zsh zsh-completions zsh-autosuggestions zsh-syntax-highlighting sudo dialog xf86-video-intel ntfs-3g os-prober`
  
19.	Generar un archivo fstab

	`# genfstab -U /mnt >> /mnt/etc/fstab`
  
20.	Hacer chroot al sistema

	`# arch-chroot /mnt`
  
22.	Agregar otras particiónes a fstab
  
23.	Cambiar zona horaria

	`# ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime`
  
24.	Ejecutar hwclock para generar /etc/adjtime

	`# hwclock --systohc`
  
25.	Editar /etc/locale.gen y descomentar la locales necesarias

	`en_US.UTF-8 UTF-8`
  
	`es_MX.UTF-8 UTF-8`
  
26.	Generar las locales

	`# locale-gen`
  
27.	Crear el archivo /etc/locale.conf y agregar la variable de lenguaje

	`LANG=es_MX.UTF-8`
  
28.	Modificar /etc/vconsole.conf y agregar el teclado

	`KEYMAP=la-latin1`
  
29.	Crear el archivo /etc/hostname

	`zahid-HP-Mini-110`
  
30.	Añadir el host a /etc/hosts

	`127.0.0.1 localhost`
  
	`::1 localhost`
  
	`127.0.1.1 zahid-HP-Mini-110.localdomain zahid-HP-Mini-110`
  
31.	Establecer contraseña root

	`# passwd`
  
32.	Instalar GRUB

	`# grub-install --target=i386-pc /dev/sda`
  
33.	Generar el archivo de configuración del GRUB

	`# grub-mkconfig -o /boot/grub/grub.cfg`
  
34. Salir de chroot

	`# exit`
  
35. Salir desmontar particiones

	`# umount -R /mnt`
  
36. Reiniciar sin la usb 

	`# reboot`
  
37. Acceder como root

38. Conectarse a la red con networkmanager

	`# nmtui`
  
39. Crear un nuevo usuario

	`# useradd -m -g users -G wheel,storage,power,ftp,http,rfkill,network -s /bin/zsh zahid`
  
40. Crear contraseña para nuevo usuario

	`# passwd zahid`
  
41. Descomentar el archivo /etc/sudoers

	`# EDITOR=nano visudo`
  
	`%wheel ALL=(ALL) ALL`
  
42. Descomentar en /etc/pacman.conf para ñadir soporte multilib

	`[multilib]`
  
	`Include = /etc/pacman.d/mirrorlist`
  
43. Añadir variables de entorno a /etc/environment

	`EDITOR="/usr/bin/nano"`
  
44. Acceder como usuario no root

45. Configurar zsh

46. Instalar yay

	`# pacman -S --needed git base-devel`
  
	`$ git clone https://aur.archlinux.org/yay.git`
  
	`$ cd yay`
  
	`$ makepkg -si`
  
47. Instalar drivers adicionales

	`$ yay -S wd719x-firmware aic94xx-firmware`
  
48. Habilitar los servicios de conección de red al inicio

	`# systemctl enable netctl-ifplugd@enp3s0.service`
	
	`# systemctl enable netctl-auto@wlp1s0b1.service`
  
49. Instalar X11, Openbox, LightDM y otras utililerias GUI

	`# pacman -S xorg-server lightdm lightdm-gtk-greeter openbox obconf pcmanfm tint2 leafpad nitrogen sakura zip unzip unrar xarchiver xdg-user-dirs pulseaudio pulseaudio-alsa volumeicon gpicview xorg-xdpyinfo neofetch mpv gvfs gvfs-smb gvfs-mtp gvfs-gphoto2`
	
50. Establecer distribución de teclado para X11

	`# localectl set-x11-keymap latam hpmini110 deadtilde,dvorak grp:alt_shift_toggle`
	
51. Instalar fuentes de Microsoft:
	https://wiki.archlinux.org/title/Microsoft_fonts#Installation
