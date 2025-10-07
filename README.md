# 🧩 CURSO COMPLETO DE GESTIÓN DE USUARIOS Y GRUPOS EN LINUX

> **Objetivo:** Dominar la creación, modificación, eliminación y administración de usuarios y grupos, contraseñas y políticas de seguridad relacionadas con cuentas en Linux.

---

## 🧱 MÓDULO 1: Conceptos Fundamentales

### 🔹 ¿Qué es un usuario en Linux?
Un **usuario** es una identidad del sistema que puede ejecutar procesos y poseer archivos.  
Cada usuario tiene un **UID (User ID)** único y un **entorno personal (home directory)**.

### 🔹 ¿Qué es un grupo?
Un **grupo** es una colección de usuarios que comparten ciertos permisos o accesos.  
Cada grupo tiene un **GID (Group ID)** único.

### 🔹 Archivos importantes del sistema
| Archivo | Descripción |
|----------|--------------|
| `/etc/passwd` | Contiene información básica de los usuarios |
| `/etc/shadow` | Contiene contraseñas cifradas |
| `/etc/group` | Contiene información sobre los grupos |
| `/etc/gshadow` | Contiene contraseñas de grupos |

#### Ejemplo de `/etc/passwd`:
```
usuario:x:1001:1001:Armando Nuñez:/home/usuario:/bin/bash
```
Campos:
1. **usuario** → nombre de login  
2. **x** → contraseña (en `/etc/shadow`)  
3. **1001** → UID  
4. **1001** → GID principal  
5. **Armando Nuñez** → descripción o nombre completo  
6. **/home/usuario** → directorio personal  
7. **/bin/bash** → intérprete de comandos (shell)

---

## 🧰 MÓDULO 2: Creación y Gestión de Usuarios

### 🧾 Crear un usuario
```bash
sudo useradd nombre_usuario
```

✅ Esto crea el usuario con configuración por defecto (sin contraseña ni home).

### ⚙️ Crear usuario con home y descripción:
```bash
sudo useradd -m -c "Armando Nuñez" armando
```
> `-m` crea el directorio `/home/armando`  
> `-c` añade una descripción

### 🔑 Asignar una contraseña
```bash
sudo passwd armando
```

### 👁️ Ver información del usuario
```bash
id armando
```
Salida:
```
uid=1001(armando) gid=1001(armando) grupos=1001(armando)
```

### ✏️ Modificar un usuario existente
```bash
sudo usermod -c "Nuevo comentario" armando
sudo usermod -d /home/nuevohome armando
sudo usermod -s /bin/zsh armando
```

### 🗑️ Eliminar un usuario
```bash
sudo userdel armando
```
> Si quieres eliminar también su directorio personal:
```bash
sudo userdel -r armando
```

---

## 👥 MÓDULO 3: Gestión de Grupos

### 🧾 Crear un grupo
```bash
sudo groupadd desarrolladores
```

### ✏️ Modificar un grupo
```bash
sudo groupmod -n devs desarrolladores
```
> Renombra el grupo

### 🗑️ Eliminar un grupo
```bash
sudo groupdel devs
```

### 👤 Agregar usuario a grupo
```bash
sudo usermod -aG desarrolladores armando
```
> `-aG` = agregar al grupo **sin eliminar** los anteriores

### 📋 Ver los grupos de un usuario
```bash
groups armando
```

### 👀 Ver todos los grupos del sistema
```bash
cat /etc/group
```

---


## 🧪 MÓDULO 5: Prácticas y Ejercicios

### 🧭 Nivel básico
1. Crear 3 usuarios (`juan`, `maria`, `pedro`) con sus homes.  
2. Asignarles contraseñas.  
3. Crear un grupo `ventas` y añadir `juan` y `maria`.  
4. Verificar grupos de cada usuario.

### ⚙️ Nivel intermedio
1. Crear un grupo `proyectos`.  
2. Crear una carpeta `/srv/proyectos` y asignarle el grupo `proyectos`.  
3. Dar permisos 770.  
4. Activar el **Setgid** para que los archivos nuevos pertenezcan siempre al grupo.

## 🧭 MÓDULO 6: Administración Avanzada

### 🧩 Bloquear y desbloquear usuarios
```bash
sudo passwd -l usuario   # Bloquear
sudo passwd -u usuario   # Desbloquear
```

### 🚫 Expirar cuenta inmediatamente
```bash
sudo usermod -L usuario
```

### 🧾 Ver usuarios conectados
```bash
who
w
```

### 🧩 Cambiar de usuario
```bash
su - armando
```

---

## 🧠 MÓDULO 7: Buenas Prácticas de Administración

1. Nunca usar el usuario root directamente: usa `sudo`.
2. Usa grupos para permisos en lugar de dar acceso individual.
3. Revisa `/etc/passwd` y `/etc/shadow` periódicamente.
4. Desactiva cuentas inactivas.
5. Mantén políticas de contraseña seguras (`/etc/login.defs`).

---
