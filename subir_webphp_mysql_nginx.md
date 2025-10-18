#  Despliegue de una página PHP con CRUD y MySQL en AWS EC2 usando **Nginx**

Este documento explica paso a paso cómo subir una aplicación **PHP + MySQL** a una instancia **EC2 (Ubuntu)** usando **Nginx** como servidor web.

---

##  1. Crear y configurar la instancia EC2

1. Entra a [AWS Management Console](https://aws.amazon.com/console/).
2. Ve a **EC2 → Launch Instance**.
3. Elige:
   - AMI: **Ubuntu Server 24.04 LTS**.
   - Tipo de instancia: **t2.micro (Free Tier)**.
   - Par de claves (Key Pair): crea o selecciona uno.
4. Configura **Security Group** 

<img width="976" height="393" alt="image" src="https://github.com/user-attachments/assets/378901a0-370d-4a9e-9cd8-d04c4395f0c0" />

   - 
5. Lanza la instancia.

---

##  2. Conectarse a la instancia

```bash
ssh -i "tu-clave.pem" ubuntu@<IP_PUBLICA_EC2>
```

---

##  3. Instalar Nginx, PHP y MySQL

```bash
sudo apt update -y
sudo apt install -y nginx php-fpm php-mysql mysql-server unzip git
```

Activa los servicios:

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl enable mysql
sudo systemctl start mysql
```

Verifica que Nginx esté activo:
```bash
systemctl status nginx
```

---

##  4. Configurar MySQL y base de datos

Accede a MySQL:
```bash
sudo mysql
```

Crea la base de datos y tabla:
```sql
CREATE DATABASE IF NOT EXISTS crud_php CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
USE crud_php;
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
EXIT;
```

Para permitir conexión de `root` sin sudo:
```bash
sudo mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '';"
sudo systemctl restart mysql
```

---

##  5. Subir los archivos PHP

Cambia al directorio raíz de Nginx:
```bash
cd /var/www/html
sudo rm -rf *
```

Crea los archivos del CRUD  y editalos con nano :

**config.php**
```php
<?php
$host = 'localhost';
$dbname = 'crud_php';
$username = 'root';
$password = '';
try {
  $conn = new PDO("mysql:host=$host;dbname=$dbname;charset=utf8", $username, $password);
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
  die("Error de conexión: " . $e->getMessage());
}
?>
```

**index.php**
```php
<?php
require 'config.php';
$stmt = $conn->query("SELECT * FROM users");
$users = $stmt->fetchAll(PDO::FETCH_ASSOC);
?>
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>CRUD PHP + MySQL</title>
</head>
<body>
<h1>Lista de Usuarios</h1>
<a href="create.php">Agregar Usuario</a>
<table border="1" cellpadding="10">
<tr><th>ID</th><th>Nombre</th><th>Email</th><th>Acciones</th></tr>
<?php foreach($users as $user): ?>
<tr>
<td><?= $user['id'] ?></td>
<td><?= $user['name'] ?></td>
<td><?= $user['email'] ?></td>
<td>
  <a href="edit.php?id=<?= $user['id'] ?>">Editar</a> |
  <a href="delete.php?id=<?= $user['id'] ?>">Eliminar</a>
</td>
</tr>
<?php endforeach; ?>
</table>
</body>
</html>
```

Crea también `create.php`, `edit.php`, y `delete.php` (mismos que se encuentraen en el tutorial de apache.

---

## 6. Configurar Nginx para PHP

Crea un archivo de configuración:
```bash
sudo nano /etc/nginx/sites-available/crud_php
```

Contenido:
```nginx
server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock; # Ajusta versión PHP si es necesario
    }
}
```

Activa el sitio y reinicia Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/crud_php /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

##  7. Probar en el navegador

Abre en tu navegador:
```
http://<IP_PUBLICA_EC2>
```
Deberías ver tu CRUD funcionando.

---

##  ¡Listo!
Tu aplicación **PHP + MySQL** con **Nginx** está desplegada en EC2.

