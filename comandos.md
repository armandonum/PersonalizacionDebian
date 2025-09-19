# COMANDOS — Explicación detallada

Este archivo explica **paso a paso** cada uno de los comandos y modificaciones para debian , con su propósito, riesgos, buenas prácticas y ejemplos. Está pensado para sistemas basados en Debian (Debian/Ubuntu/derivados). Lee con atención las *precauciones* antes de aplicar cambios en sistemas de producción.




# 1) `su`

### Qué hace

`su` (substitute user) cambia el usuario de la sesión. Sin argumentos `su -` te pide la contraseña de `root` y abre una sesión de root.

Ejemplos:

```bash
su -            # cambia a root (pide contraseña de root)
sudo - usuario    # cambia a 'usuario' (pide contraseña de 'usuario')
```
Ejemplo de Salida: 
```
root@debian:/home/nombre_usuario#
```

### Recomendación

- En sistemas modernos es preferible usar `sudo` por el registro y por no compartir la contraseña root.

---
# 2) Añadir usuario a sudoers

### Comandos

```bash
nano /etc/sudoers
# buscar la linea root ALL=(ALL:ALL) ALL y abajo colocar 
nombre_de_usuario ALL=(ALL:ALL) ALL
```

### Qué hace

Permite que `nombre_de_usuario` ejecute cualquier comando como cualquier usuario (incluido root) usando `sudo`.





# 3) Reiniciar la máquina para aplicar cambios


### Comandos de reinicio

```bash
sudo reboot
# o
sudo systemctl reboot
```

---

# 4) Modificar GRUB

### Archivo a editar

`/etc/default/grub`

### cambiar las lineas 

```bash
GRUB_TIMEOUT=0
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
# Cambiar resolución (ejemplo)
GRUB_GFXMODE=1024x768
```

### Explicación de variables importantes

- `GRUB_TIMEOUT` — tiempo en segundos que GRUB muestra el menú antes de arrancar la entrada por defecto.
  - `0` = arranca inmediatamente sin mostrar menú. *Riesgo:* si necesitas elegir un kernel antiguo o modo recovery, será más difícil (aunque suele mostrarse manteniendo Shift/Shift+Esc en algunos BIOS).*Se recomienda 1–5 segundos en equipos de escritorio.*
- `GRUB_CMDLINE_LINUX_DEFAULT` — parámetros pasados al kernel en el arranque normal.
  - `quiet splash` oculta mensajes del kernel y muestra la pantalla de inicio gráfica; la palabra correcta es `quiet` 

- `GRUB_GFXMODE` — fija la resolución que GRUB  usa en pantalla (ej: `1024x768`).




> `update-grub`  reconfigura los cambios que realizamos a grup


# 5) Desinstalación

### Comando

```bash
sudo apt autoremove
```

### Qué hace

Elimina paquetes instalados automáticamente como dependencias que ya no son necesarios (por ejemplo, librerías de programas desinstalados).

### Recomendaciones



- Para eliminar también archivos de configuración:

```bash
sudo apt autoremove --purge
sudo apt autoclean
```

---

# 6) Repositorios


```
deb http://deb.debian.org/debian bookworm-backports main
```

### Qué es

Añade el repositorio *backports* de Debian Bookworm. `bookworm-backports` contiene paquetes más recientes compilados para Bookworm pero empaquetados para ser compatibles con la versión estable.

### Cómo añadirlo correctamente

ir en la lista de aplicaciones "software & Updates"
marcar "software compatible con las <<DFSG>> con dependencias no libres..." y tambien "software no compatible con las <<DFSG>>"  en (otro Software) añadir el enlace ` deb http://deb.debian.org/debian bookworm-backports main`


### Uso responsable


- Para instalar desde backports:

```bash
sudo apt -t bookworm-backports install nombre_paquete
```

- Útil para kernels más recientes, controladores, etc., pero usar con precaución en servidores críticos.

---

# 7) Instalación de utilidades

### Actualizar e instalar actualizaciones mayores

```bash
sudo apt update && sudo apt dist-upgrade -y
```

- `apt update` actualiza la lista de paquetes.
- `apt dist-upgrade` (equivalente moderno: `apt full-upgrade`) instala actualizaciones y puede cambiar dependencias o eliminar paquetes para completar la actualización.
- `-y` responde "sí" automáticamente.

---

### Paquetes básicos 

- `neofetch` — muestra información del sistema en la terminal (nombre de distro, kernel, uptime, memoria, etc.).
- `htop` — monitor interactivo de procesos (mejor que `top`).

Comando:

```bash
sudo apt install neofetch htop
```

---

### Soporte de sistemas de archivos

```bash
sudo apt install  hfsplus hfsutils ntfs-3g
```


- `hfsplus`, `hfsutils` — soporte para HFS+ (sistemas Mac antiguos).
- `ntfs-3g` — driver de lectura/escritura para NTFS (Windows).



---

### GDebi y Synaptic

```bash
sudo apt install gdebi gdebi-core synaptic
```

- `gdebi` — instala paquetes `.deb` resolviendo dependencias (útil cuando instalas un .deb local).
- `synaptic` — gestor gráfico de paquetes avanzado.

---

### Compresión/descompresión

```bash
sudo apt install p7zip-full p7zip-rar rar unrar
```

- `p7zip-full` — soporte 7z.
- `p7zip-rar` / `rar` / `unrar` — trabajar con formatos RAR.

---

### Multimedia y codecs

```bash
sudo apt install ffmpeg libavcodec-extra gstreamer1.0-libav gstreamer1.0-plugins-ugly gstreamer1.0-plugins-bad gstreamer1.0-pulseaudio vorbis-tools
```

- `ffmpeg` — utilidad potente para convertir y procesar audio/video.
- `libavcodec-extra` — codecs adicionales que por licencia no vienen en la instalación por defecto.
- `gstreamer1.0-*` — complementos para la reproducción multimedia en aplicaciones que usan GStreamer.
- `vorbis-tools` — herramientas para Ogg Vorbis.

---

### Fuentes

```bash
sudo apt-get install fonts-freefont-ttf fonts-freefont-otf
sudo apt-get install ttf-mscorefonts-installer
sudo apt install fonts-ubuntu
```

- `fonts-freefont-*` — fuentes libres.
- `ttf-mscorefonts-installer` — instalador de las fuentes core de Microsoft . A veces pide interacción.
- `fonts-ubuntu` — tipografías usadas en Ubuntu.

---

# 8) Flatpak

### Instalación e integración

```bash
sudo apt install flatpak
sudo apt install gnome-software-plugin-flatpak   # integración con GNOME Software
```

### Añadir Flathub (repositorio principal)

La URL correcta para Flathub es:

```bash
sudo flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```



### Ejemplo de uso

```bash
flatpak search vlc
flatpak install flathub org.videolan.VLC
flatpak run org.videolan.VLC
```

### Notas

- Después de instalar Flatpak puede ser necesario cerrar sesión y volver a entrar para que integraciones de escritorio funcionen correctamente.

---


# paquestes para personalizacion

Extension List -> lista de extensiones
clipboard History -> porta papeles 

Vitals ->  informacion de la maquina 
desktop icons NG(DING) ->  para ver los iconoes en el escritorio 

transparent Top Bar (Asjustable transparency) -> tranparentar la barra de tareas 

dashto Dock -> configurar las barra de tareas

Desktop Cube ->  vision en 3d

Coverflow AltTap
