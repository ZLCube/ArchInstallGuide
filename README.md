# Guía de instalación ArchLinux/BlackArch desde cero

En esta guía tendrás el paso a paso de cómo instalar Arch linux desde cero en vmware desde el archivo .iso, acabaremos con una interfaz gráfica gnome y posteriormente veremos cómo instalar las dependencias y herramientas de blackarch. Un agradecimiento especial a L4nder por su apoyo ---> https://lander4k.github.io/

Empezando desde el primer booteo tendremos una terminal como root.

1) En caso de querer el teclado en español usaremos el comando ```loadkeys es```
2) Verificamos que tenemos conexión a internet con el comando ```ping -c 1 google.com```

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/ping.png)

3) Particionamos el disco, para esto usaremos el comando ```cfdisk``` y seleccionaremos la opción "dos" (Esta configuración es para máquinas virtuales)

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/cfdisk.png)

* Vamos a crear tres particiones nuevas, boot, root y swap
* La primera partición la haremos de 512 megas 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/primera%20partición.png)

 * Seleccionaremos primary puesto a que va a ser una partición primaria

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/Tipo%20de%20partición.png)

 * Como puedes ver, en verde nos muestra el espacio que nos queda disponible y en blanco los 512 megas que ya particionamos. Con la flecha hacia abajo seleccionaremos el espacio libre "lo que está en verde" y vamos a crear una nueva partición 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/neww.png)

* Para esta partición vamos a restar 4.5G a el espacio que nos queda libre, en mi caso me quedan 99.5G lo que al restarle 4.5G me quedaría 95G así que creamos una nueva partición primaria con 95G 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/Espacio%20Disponible.png)

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/particion95.png)

De igual forma la mantenemos como partición primaria.
* Por último creamos una partición con los 4.5G que nos restan, sin embargo después de crearla seleccionaremos type y elegiremos "Linux swap/solaris"

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/swap.png)

 Así nos deberían quedar nuestras particiones.

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/4.5created.png)

 Una vez creadas seleccionaremos la opción write y escribiremos la palabra "yes" 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/write.png) 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/yes.png) 

Ahora podemos seleccionar quit y nos regresará a la terminal de arch 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/quit.png)

4) Validamos que las particiones se crearon correctamente con el comando ``` lsblk```

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/lsblk.png)

Ahora tenemos que formatear las particiones
 * Para boot ```mkfs.vfat -F 32 /dev/sda1```
 * Para root ```mkfs.ext4 /dev/sda2```
 * Para swap ```mkswap /dev/sda3```
 * Terminamos con ```swapon``` para aplicar los cambios

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/formateodeparticones.png)

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

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/zonahoraria.png)

12) Ejecutamos el comando ```hwclock --systohc```
13) Editamos el idioma con ```nano /etc/locale.gen``` con el atajo ```control + w``` podemos buscar las líneas en_US y es_ES las cuales des comentaremos quitando el #

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/keymap.png)

Para guardar los cambios ejecutamos el comando ```locale-gen``` 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/localegen.png)

14) En caso de no querer cambiar el layout del teclado cada vez que iniciamos sesión tenemos que crear o editar el archivo vsconsole.conf con el siguiente comando  ```echo "KEYMAP=es" > /etc/vconsole.conf```
15) Cambiaremos el nombre de nuestro host con el siguiente comando:
```echo "(nombredetuequipo)" > /etc/hostname``` 
16) Ahora configuramos nuestro local host en el archivo de /etc/hosts donde agregaremos los siguientes parámetros: 

------------------------------------------------------------------------------------
```
127.0.0.1 	localhost

::1		localhost

127.0.0.1	(nombredetuequipo).localhost (nombredetuequipo)
```

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/localhost.png)

17) Usamos el comando ```mkinitcpio -P``` para indicar el booteo al sistema operativo principal
18) Instalaremos el gestor de arranque grub con los siguientes comandos 
``` grub-install /dev/sda``` y posteriormente ```grub-mkconfig -o /boot/grub/grub.cfg"```
19) Ahora reiniciamos el sistema con el atajo de teclado ```control + D```
20) Rebooteamos la máquina con el comando ```reboot now```

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/grubb.png) ![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/primerboot.png)

22)  Anteriormente creamos un usuario, este lo agregaremos a wheele con el siguiente comando 
```usermod -aG wheel (usuario)``` así mismo editaremos el archivo /etc/sudoers para descomentar la linea de texto ```%wheel``` 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/wheel.png) 

Esto hará que cuando queramos convertirnos en x usuario nos pida la contraseña por motivos de seguridad.

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/uidcube.png)

23) De momento no tenemos conexión a internet por lo que ejecutaremos los siguientes comandos para activar los servicios de red sin necesidad de rebootear la máquina
```systemctl enable NetworkManager"``` y ```"systemctl start NetworkManager```

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/internetconfig.png)

24) Instalamos paquetes necesarios ```pacman -S xorg xorg-server``` y ```pacman -S gnome```
25) Para iniciar en la interfaz gráfica de gnome cada vez que encendamos la máquina usaremos el siguiente comando ```systemctl enable gdm```
26) Instalaremos sudo y kitty antes de concluir con ```pacman -S kitty sudo```
27) En caso de estar en VMware también instalaremos los paquetes necesarios para la interfaz gráfica 
```pacman -S gtkmm open-vm-tools xf86-video-vmware xf86-input-vmmouse``` las cuales activaremos con el comando ```systemctl enable vmtoolsd``` y posteriormente reiniciaremos la maquina con ```reboot now```

# Listo ya tenemos instalado Arch limpio y listo para personalizarlo 

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/gnaomebase.png)

Te recomiendo tomar un snapshot de la máquina base estable y limpia de cualquier paquetería antes de personalizara, así en caso de que se rompa alguna dependencia siempre podemos volver a nuestra base inicial

![](https://github.com/ZLCube/ArchInstallGuide/blob/main/Contenido/snapshot.png)


# Blackarch

Ahora para instalar las herramientas de BlackArch por encima de nuestra instalación limpia de Arch Linux necesitamos lo siguiente:

1) Copiaremos el script de instalación con el siguiente comando:
```curl -O https://blackarch.org/strap.sh```
2) Le damos permiso de ejecución al archivo con:
```chmod +x strap.sh```
3) Por último ejecutamos el script de instalación:
```./strap.sh```

Todo este procedimiento nos dejará instalado el repositorio de BlackArch donde lo único que necesitaremos para instalar las herramientas es el comando sudo pacman -S 
* Por ejemplo instalemos metasploit con el comando ```sudo pacman -S metasploit```
* La misma dinámica nos servirá para cualquier otra herramienta que necesitemos
