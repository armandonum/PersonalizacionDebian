# Uso de `tar` en Linux 

## Conceptos básicos
- `c` = **create** (crear un archivo)
- `x` = **extract** (extraer)
- `t` = **list** (listar contenido)
- `f` = **file** — indica el nombre del archivo tar (si no pones `-f`, `tar` intenta usar la unidad de cinta)
- `v` = **verbose** (muestra los ficheros procesados)
- `z` = usar **gzip** (archivo `.tar.gz` o `.tgz`)
- `j` = usar **bzip2** (archivo `.tar.bz2`)
- `J` = usar **xz** (archivo `.tar.xz`)
- `C` = **change directory** antes de actuar (`-C /ruta`)

---

## Paso previo) Clonar un repositorio
```bash
sudo apt install git
```
* Instalacion de paquete git
```bash
git clone https://github.com/armandonum/flisolUSFX2025.git
```
---

## 1) Crear un tar (sin compresión)
```bash
tar -cvf backup.tar /ruta/a/dir /otra/ruta/archivo.txt
```
* -c crea, -v lista durante la creación, -f backup.tar indica el archivo resultante.

* Resultado: backup.tar con todos los archivos/directorios incluidos.

---

## 2) Crear un tar comprimido (gzip)
```bash
tar -czvf backup.tar.gz /home/armando/proyecto
```
* -z comprime con gzip -> archivo .tar.gz.

* Para bzip2 usar -j -> .tar.bz2. Para xz usar -J -> .tar.xz.
---

## 3) Listar contenido de un tar (sin extraer)
```bash
tar -tvf backup.tar # tar sin compresion 
tar -tzvf backup.tar.gz  # tar comprimido (gzip)
```
* -t lista, -v muestra detalles tipo ls -l.

---

## 4) Extraer un tar (todo)
```bash
tar -xvf backup.tar
tar -xzvf backup.tar.gz
```
* -x extrae. Por defecto extrae en el directorio actual.
---

## 5) Extraer a un directorio específico
```bash
tar -xzvf backup.tar.gz -C /tmp/restauracion
```
* ` -C /tmp/restauracion` cambia a ese directorio antes de extraer.

---

## 6) Crear tar desde una lista de ficheros

* Si tienes filelist.txt con rutas (una por línea):
```bash
tar -cvf backup.tar -T filelist.txt
``` 

---


## Cheat-sheet rápido (comandos más usados)

Crear (gzip): `tar -czvf archivo.tar.gz /mi/dir`

Listar: `tar -tzvf archivo.tar.gz`

Extraer: `tar -xzvf archivo.tar.gz`

Extraer a dir:` tar -xzvf archivo.tar.gz -C /destino`

Extraer un solo archivo:` tar -xzvf archivo.tar.gz ruta/dentro/tar`

Excluir patrón:` --exclude='*.tmp'`

Añadir a tar sin comprimir: `tar -rvf archivo.tar nuevo.txt `








