# Instalación del sistema base
## Preinstalación‎
- Preparación y arranque desde un medio de instalación
- Listar distribuciones de teclado disponibles
```bash
ls /usr/share/kbd/keymaps/**/*.map.gz
```
- Establecer una distribución de teclado de las disponibles (sin extensión de archivo)
```bash
loadkeys la-latin1
```
- Verificar el modo de arranque (BIOS o UEFI) listando el directorio efivars
```bash
ls /sys/firmware/efi/efivars
```
- Verificar estado del adaptador inalámbrico
```bash
ip link
```
- Conectarse a la red Wi-Fi usando iwctl
    - Iniciar la utilidad
    ```bash
    iwctl
    ```
    - Listar los adaptadores disponibles
    ```bash
    device list
    ```
    - Escanear redes accesibles accesibles al adaptador
    ```bash
    station ADAPTADOR get-networks
    ```
    - Conectarse a una red
    ```bash
    station ADAPTADOR connect RED
    ```
    - Ingresar contraseña de la red
    - Salir de la utilidad
    ```bash
    exit
    ```
- Comprobar la conexión
```bash
ping archlinux.org
```
- Actualizar el reloj del sistema
```bash
timedatectl set-ntp true
```
- Listar los dispositivos de almacenamiento disponibles (/dev/sdx, /dev/nvme0n1 o /dev/mmcblk0) y sus particiones
```bash
fdisk -l
```
- Preparar las particiones usando fdisk
    - Iniciar la utilidad en el dispositivo de almacenamiento a particionar (x es la letra del dispositivo)
    ```bash
    fdisk /dev/sdx
    ```
    - Listar las particiones en el dispositivo
    ```bash
    p
    ```
    - Borrar y crear las particiones como sea necesario
    - Cambiar el tipo de la partición root (/) a linux (N es el número de la partición)
    ```bash
    t
    N
    linux
    ```
    - En el caso de usar BIOS, marcar la partición root como de arranque
    ```bash
    a
    N
    ```
    - Cambiar el tipo de la partición de intercambio a swap (S es el número de la partición)
    ```bash
    t
    S
    swap
    ```
    - Escribir los cambios y salir de la utilidad
    ```bash
    w
    ```
- Formatear la partición root como ext4 
```bash
mkfs.ext4 /dev/sdxN
```
- Formatear la partición de intercambio como swap
```bash
mkswap /dev/sdxS
```
- Montar la partición root a /mnt
```bash
mount /dev/sdxN /mnt
```
- Activar la partición swap
```bash
swapon /dev/sdxS
```
- En el caso de usar UEFI, crear el directorio /mnt/boot/efi (o /mnt/efi)
```bash
mkdir /mnt/boot && mkdir /mnt/boot/efi
```
- En el caso de usar UEFI, montar la partición EFI a /mnt/boot/efi (o /mnt/efi) (E es el número de la partición)
```bash
mount /dev/sdxE /mnt/boot/efi
```
## Instalación
- Crear la lista de servidores usando reflector para ordenar los últimos servidores sincronizados y ordenarlos por taza de descarga 
```bash
reflector --verbose --latest 7 --sort rate --save /etc/pacman.d/mirrorlist
```
- Instalar los paquetes esenciales base, linux (linux-hardened, linux-lts o linux-zen) y linux-firmware. También se pueden instalar otros paquetes necesarios para tener un sistema base funcional como intel-ucode (o amd-ucode), ntfs-3g, networkmanager, sudo, nano (o vim), etc.
```bash
pacstrap /mnt base linux linux-firmware intel-ucode ntfs-3g networkmanager sudo nano
```
## Configuración del sistema
- Generar un archivo fstab
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
- Cambiar al root del nuevo sistema
```bash
arch-chroot /mnt
```
- Establecer zona horaria
```bash
ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime
```
- Ejecutar hwclock para generar /etc/adjtime
```bash
hwclock --systohc
```
- Editar /etc/locale.gen y descomentar `en_US.UTF-8 UTF-8` y otras locales (configuraciones regionales) necesarias, como `es_MX.UTF-8 UTF-8`
- Generar las locales
```bash
locale-gen
```
- Crear el archivo /etc/locale.conf y agregar la variable de lenguaje
```bash
echo "LANG=es_MX.UTF-8" >> /etc/locale.conf
```
- Modificar /etc/vconsole.conf y establecer la distribución de teclado estableciendo la variable como `KEYMAP=la-latin1`
- Crear el archivo /etc/hostname y agregar el nombre del host (para claridad en mi red local suelo usar la nomenclatura: propietario-Fabricante-Modelo)
```bash
echo "propietario-Fabricante-Modelo" >> /etc/hostname
```
- Añadir el nombre del host a /etc/hosts (si el sistema tiene dirección IP permanente, se debe usar en lugar de 127.0.1.1)
```bash
echo "127.0.0.1 localhost" >> /etc/hosts
echo "::1 localhost" >> /etc/hosts
echo "127.0.1.1	propietario-Fabricante-Modelo.localdomain propietario-Fabricante-Modelo" >> /etc/hosts
```
- Establecer contraseña del usuario root
```bash
passwd
```
- Instalar un bootloader (cargador de arranque), sin este, el sistema normalmente no iniciara
- Salir del root del nuevo sistema
```bash
exit
```
- Desmontar particiones
```bash
umount -R /mnt
```
- Reiniciar la maquina
```bash
reboot
```
- Remover el medio de instalación
