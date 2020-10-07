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
  
		`# station wlan0 connect ARRIS-CA32`
    
	5.	Salir de la utilidad
  
		`# exit`
    
8.	Comprobar la conexión

	`# ping archlinux.org`
  
9.	Actualizar el reloj para que coincida con Windows

	`# timedatectl set-local-rtc 1 --adjust-system-clock`
  
10.	Listar los dispositivos y las particiones

	`# fdisk -l`
  
11.	Preparar las particiones con fdisk
	1.	Abrir fdisk en el dispositivo de almacenamiento
  
		`# fdisk /dev/sda`
    
	2.	Listar las particiones
  
		`p`
    
	3.	Cambiar el tipo de partición a Linux
  
		`t 5 83`
    
	4.	Cambiar el tipo de partición a Swap
  
		`t 6 82`
    
	5.	Marcar partición como de arranque
  
		`a 5`
    
	6.	Escribir los cambios
  
		`w`
    
12.	Formatear la partición como btrfs

	`# mkfs.btrfs -f /dev/sda5`
  
13.	Formatear la partición como Swap

	`# mkswap /dev/sda6`
  
14.	Montar la partición root a /mnt

	`# mount /dev/sda5 /mnt`
  
15.	Activar la partición Swap

	`# swapon /dev/sda6`
  
16.	Actualizar la lista con los 5 mejores servidores

	`# reflector --verbose --latest 5 --sort rate --save /etc/pacman.d/mirrorlist`
  
17.	Instalar los paquetes esenciales

	`# pacstrap /mnt base linux-lts linux-firmware nano iproute2 dhcpcd wpa_supplicant netctl grub zsh 	zsh-completions zsh-autosuggestions zsh-syntax-highlighting sudo dialog`
  
18.	Generar un archivo fstab

	`# genfstab -U /mnt >> /mnt/etc/fstab`
  
19.	Hacer chroot al sistema

	`# arch-chroot /mnt`
  
20.	Agregar la partición Swap a fstab

	`# echo “UUID=$(lsblk -no UUID /dev/sda6) none swap defaults 0 0” > /etc/fstab`
  
21.	Cambiar zona horaria

	`# ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime`
  
22.	Ejecutar hwclock para generar /etc/adjtime

	`# hwclock --systohc`
  
23.	Editar /etc/locale.gen y descomentar la locales necesarias

	`en_US.UTF-8 UTF-8`
  
	`es_MX.UTF-8 UTF-8`
  
24.	Generar las locales

	`# locale-gen`
  
25.	Crear el archivo /etc/locale.conf y agregar la variable de lenguaje

	`LANG=es_MX.UTF-8`
  
26.	Modificar /etc/vconsole.conf y agregar el teclado

	`KEYMAP=la-latin1`
  
27.	Crear el archivo /etc/hostname

	`zahid-HP-Mini-110`
  
28.	Añadir el host a /etc/hosts

	`127.0.0.1 localhost`
  
	`::1 localhost`
  
	`127.0.1.1 zahid-HP-Mini-110.localdomain zahid-HP-Mini-110`
  
29.	Establecer contraseña root

	`# passwd`
  
30.	Instalar GRUB

	`# grub-install --target=i386-pc /dev/sda`
  
31.	Generar el archivo de configuración del GRUB

	`# grub-mkconfig -o /boot/grub/grub.cfg`
  
32. Salir de chroot

	`# exit`
  
33. Salir desmontar particiones

	`# umount -R /mnt`
  
34. Reiniciar sin la usb 

	`# reboot`
  
35. Acceder como root

36. Crear un perfil para netctl en /etc/netctl/wifi-ARRIS-CA32

	`# wifi-menu`
  
37. Crear un nuevo usuario

	`# useradd -m -g users -G wheel,storage,power -s /bin/bash zahid`
  
38. Crear contraseña para nuevo usuario

	`# passwd zahid`
  
39. Descomentar el archivo /etc/sudoers

	`# EDITOR=nano visudo`
  
	`%wheel ALL=(ALL) ALL`
  
40. Descomentar en /etc/pacman.conf para ñadir soporte multilib

	`[multilib]`
  
	`Include = /etc/pacman.d/mirrorlist`
  
41. Añadir variables de entorno a /etc/environment

	`EDITOR="/usr/bin/nano"`
  
42. Acceder como usuario no root

43. Configurar zsh

44. Instalar yay

	`# pacman -S --needed git base-devel`
  
	`$ git clone https://aur.archlinux.org/yay.git`
  
	`$ cd yay`
  
	`$ makepkg -si`
  
45. Instalar drivers adicionales

	`$ yay -S wd719x-firmware aic94xx-firmware`
  
46. Instalar X11

	`# pacman -S openbox ttf-dejavu ttf-liberation`
  
47. Visitar https://blog.desdelinux.net/instalacion-de-openbox-en-arch-linux/
