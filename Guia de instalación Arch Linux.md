1) En caso de querer el teclado en español usaremos el comando ```loadkeys es```
2) Verificamos que tenemos conexión a internet con el comando ```ping -c 1 google.com```
![[ping.png]]
3) Particionamos el disco, para esto usaremos el comando ```cfdisk``` y seleccionaremos la opción "dos" (Esta configuración es para máquinas virtuales)
![[cfdisk.png]]
* Vamos a crear tres particiones nuevas, boot, root y swap
* La primera partición la haremos de 512 megas 
 ![[primera partición.png]] 
 * Seleccionaremos primary puesto a que va a ser una partición primaria
![[Tipo de partición.png]]
 * Como puedes ver, en verde nos muestra el espacio que nos queda disponible y en blanco los 512 megas que ya particionamos. Con la flecha hacia abajo seleccionaremos el espacio libre "lo que está en verde" y vamos a crear una nueva partición 
![[neww.png]]
* Para esta partición vamos a restar 4.5G a el espacio que nos queda libre, en mi caso me quedan 99.5G lo que al restarle 4.5G me quedaría 95G así que creamos una nueva partición primaria con 95G 
![[Espacio Disponible.png]] 
![[particion95.png]] De igual forma la mantenemos como partición primaria.
* Por último creamos una partición con los 4.5G que nos restan, sin embargo después de crearla seleccionaremos type y elegiremos "Linux swap/solaris"
![[swap.png]]
 Así nos deberían quedar nuestras particiones.
 ![[4.5created.png]]
 Una vez creadas seleccionaremos la opción write y escribiremos la palabra "yes" 
 ![[write.png]] ![[yes.png]] Ahora podemos seleccionar quit y nos regresará a la terminal de arch 
 ![[quit.png]] 
4) Validamos que las particiones se crearon correctamente con el comando ``` lsblk```
![[lsblk.png]] 
Ahora tenemos que formatear las particiones
 * Para boot ```mkfs.vfat -F 32 /dev/sda1```
 * Para root ```mkfs.ext4 /dev/sda2```
 * Para swap ```mkswap /dev/sda3```
 * Terminamos con ```swapon``` para aplicar los cambios

![[formateodeparticones.png]]

 5) Ahora montaremos el sistema en las particiones formateadas
* Para root lo montaremos en /mnt con ```mount /dev/sda2 /mnt```
* Para boot crearemos una carpeta llamad boot dentro de /mnt con el comando
```mount --mkdir /dev/sda1 /mnt/boot```
* Ahora activamos el swap con ```swapon```
6) Ahora si instalaremos los paquetes del sistema con 
```pacstrap -K /mnt linux linux-firmware base base-devel git nano networkmanager grub```
NOTA: En caso de que lance algún error puedes optar por ```pacman -Sy && pacman -S archlinux-keyring```
7) Ahora debemos crear el archivo fstab el cual contiene las particiones del sistema 
```genfstab -U /mnt >> /mnt/etc/fstab```
8) Con el siguiente comando nos movemos a nuestro nuevo sistema ```arch-chroot /mnt```
9) Le asignamos contraseña al usuario root con ```passwd```
10) Creamos un usuario con ```useradd -m usuario``` donde usuario será el nombre del usuario que vas a crear
11) Ahora definimos nuestra zona horaria con el comando
```ln -sf /usr/share/zoneinfo/(Region)/(Ciudad) /etc/localtime```
![[zonahoraria.png]]
12) Ejecutamos el comando ```hwclock --systohc```
13) Editamos el idioma con ```nano /etc/locale.gen``` con el atajo ```control + w``` podemos buscar las líneas en_US y es_ES las cuales des comentaremos quitando el #
![[keymap.png]]
Para guardar los cambios ejecutamos el comando ```locale-gen``` 
![[localegen.png]]
14) En caso de no querer cambiar el layout del teclado cada vez que iniciamos sesión tenemos que crear o editar el archivo vsconsole.conf con el siguiente comando  ```echo "KEYMAP=es" > /etc/vconsole.conf```
15) Cambiaremos el nombre de nuestro host con el siguiente comando:
```echo "(nombredetuequipo)" > /etc/hostname``` 
16) Ahora configuramos nuestro local host en el archivo de /etc/hosts donde agregaremos los siguientes parámetros: 
------------------------------------------------------------------------------------
127.0.0.1 	localhost
::1		localhost
127.0.0.1	(nombredetuequipo).localhost (nombredetuequipo)

![[localhost.png]]

17) Usamos el comando ```mkinitcpio -P``` para indicar el booteo al sistema operativo principal
18) Instalaremos el gestor de arranque grub con los siguientes comandos 
``` grub-install /dev/sda``` y posteriormente ```grub-mkconfig -o /boot/grub/grub.cfg"```
19) Ahora reiniciamos el sistema con el atajo de teclado ```control + D```
20) Rebooteamos la máquina con el comando ```reboot now```
![[grubb.png]] ![[primerboot.png]]
22)  Anteriormente creamos un usuario, este lo agregaremos a wheele con el siguiente comando 
```usermod -aG wheel (usuario)``` así mismo editaremos el archivo /etc/sudoers para descomentar la linea de texto ```%wheel``` 
![[wheel.png]] 
Esto hará que cuando queramos convertirnos en x usuario nos pida la contraseña por motivos de seguridad.
![[uidcube.png]]
23) De momento no tenemos conexión a internet por lo que ejecutaremos los siguientes comandos para activar los servicios de red sin necesidad de rebootear la máquina
```systemctl enable NetworkManager"``` y ```"systemctl start NetworkManager```
![[internetconfig.png]]
24) Instalamos paquetes necesarios ```pacman -S xorg xorg-server``` y ```pacman -S gnome```
25) Para iniciar en la interfaz gráfica de gnome cada vez que encendamos la máquina usaremos el siguiente comando ```systemctl enable gdm```
26) Instalaremos sudo y kitty antes de concluir con ```pacman -S kitty sudo```
27) En caso de estar en VMware también instalaremos los paquetes necesarios para la interfaz gráfica 
```pacman -S gtkmm open-vm-tools xf86-video-vmware xf86-input-vmmouse``` las cuales activaremos con el comando ```systemctl enable vmtoolsd``` y posteriormente reiniciaremos la maquina con ```reboot now```

# Listo ya tenemos instalado Arch limpio y listo para personalizarlo 

![[gnaomebase.png]]

Te recomiendo tomar un snapshot de la máquina base estable y limpia de cualquier paquetería antes de personalizara, así en caso de que se rompa alguna dependencia siempre podemos volver a nuestra base inicial

![[snapshot.png]]