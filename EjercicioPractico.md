# Ejercicios Prácticos de Comandos Básicos y Permisos en Linux


---

## 📂 Parte 1: Comandos Básicos

### 1. Navegación
- Usa `pwd` para ver en qué directorio estás.
- Cambia al directorio `/tmp` con `cd`.
- Regresa a tu directorio personal con `cd ~`.

### 2. Exploración
- Lista todos los archivos, incluidos los ocultos, con `ls -la`.
- Muestra la estructura de directorios usando `tree` (si está instalado).
- Verifica el tipo de un archivo con `file`.

### 3. Creación y manipulación de archivos
- Crea un archivo vacío llamado `prueba.txt` con `touch`.
- Agrega texto dentro usando `echo "Hola Linux" > prueba.txt`.
- Muestra el contenido con `cat prueba.txt`.
- Copia este archivo a otro con `cp prueba.txt copia.txt`.
- Mueve `copia.txt` a un directorio llamado `backup/` con `mv`.

### 4. Eliminación
- Elimina el archivo `copia.txt` con `rm`.
- Borra un directorio vacío con `rmdir`.
- Borra un directorio con archivos dentro con `rm -r`.

---

## 🔐 Parte 2: Permisos en Linux

### 1. Ver permisos
- Lista los permisos de los archivos en tu directorio con `ls -l`.

### 2. Cambiar permisos con `chmod`
- Crea un archivo llamado `script.sh`.
- Dale permisos de ejecución al propietario: `chmod u+x script.sh`.
- Dale permisos de lectura a todos: `chmod a+r script.sh`.
- Quita permisos de escritura al grupo: `chmod g-w script.sh`.

### 3. Cambiar propietario con `chown`
- Si tienes permisos de superusuario:
  - Crea un archivo `privado.txt`.
  - Cámbiale el propietario a `root` con `sudo chown root privado.txt`.

### 4. Cambiar grupo con `chgrp`
- Crea un archivo `grupo.txt`.
- Cámbiale el grupo a `users` con `sudo chgrp users grupo.txt`.

---

