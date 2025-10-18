#  Guía completa: Subir un CRUD en PHP + MySQL a una instancia EC2 (Ubuntu) usando apache

Esta guía te lleva desde **cero** hasta tener un **CRUD funcional en PHP y MySQL** corriendo en una instancia EC2 de AWS Ubuntu.

---

## 1. Crear la instancia EC2

1. Ve a **AWS Console → EC2 → Launch Instance**.
<img width="1305" height="552" alt="image" src="https://github.com/user-attachments/assets/4863c44b-d059-4678-9bd3-587f6c117a3a" />

2. Asigna un nombre, por ejemplo: `crud-php-server`.
  <img width="916" height="164" alt="image" src="https://github.com/user-attachments/assets/dafad24b-d280-4be2-91c0-b3ad83cb2fef" />

3. **AMI:** Ubuntu Server 22.04 LTS.
4. **Tipo de instancia:** `t2.micro` (asegurece de que este "disponible para la capa gratuita).
5. **Key Pair:** crea una o selecciona una existente y descarga el archivo `.pem`.
6. **Security Group:** abre los puertos:
   - `22` (SSH)
   - `80` (HTTP)
   - `443` (HTTPS) opcional
     <img width="976" height="393" alt="image" src="https://github.com/user-attachments/assets/0c67378b-e548-4615-96c0-41762b3dab1e" />

7. Lanza la instancia y espera a que esté en estado **Running**.

---

##  2. Conectarte a la instancia

```bash
ssh -i "tu-clave.pem" ubuntu@<IP_PUBLICA_DE_TU_INSTANCIA>
```

Ejemplo:
```bash
ssh -i "mi-clave.pem" ubuntu@3.92.100.5
```

---

##  3. Instalar Apache, PHP y MySQL
dentro de la instancia 

```bash
sudo apt update -y
sudo apt install apache2 php libapache2-mod-php php-mysql mysql-server git unzip -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

Prueba Apache:
en un navegador
```
http://<IP_PUBLICA>
```
Debe mostrar la página de bienvenida de Apache.

---

##  4. Configurar MySQL y crear la base de datos

Entra a MySQL:
```bash
sudo mysql
```

Ejecuta lo siguiente:

```sql
CREATE DATABASE IF NOT EXISTS crud_php CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
USE crud_php;

CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE USER 'phpuser'@'localhost' IDENTIFIED BY 'php123';
GRANT ALL PRIVILEGES ON crud_php.* TO 'phpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 🧩 5. Subir el proyecto PHP CRUD

Ubicación del sitio web:

```
/var/www/html/
```

Limpia el contenido anterior:
```bash
sudo rm -rf /var/www/html/*
```

Clona el proyecto o crea los archivos directamente:
```bash
cd /var/www/html
sudo nano config.php
```

### `config.php`
```php
<?php
$host = 'localhost';
$dbname = 'crud_php';
$username = 'phpuser';
$password = 'php123';

try {
  $conn = new PDO("mysql:host=$host;dbname=$dbname;charset=utf8", $username, $password);
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
  die("Error de conexión: " . $e->getMessage());
}
?>
```

### `index.php`
```php
<?php include 'config.php'; ?>
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>CRUD PHP + MySQL</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
  <h2 class="text-center mb-4">Gestión de Usuarios</h2>
  <a href="create.php" class="btn btn-primary mb-3">➕ Nuevo Usuario</a>
  <table class="table table-bordered table-striped">
    <thead class="table-dark">
      <tr>
        <th>ID</th>
        <th>Nombre</th>
        <th>Email</th>
        <th>Fecha de Creación</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      <?php
      $stmt = $conn->query("SELECT * FROM users ORDER BY id DESC");
      while ($row = $stmt->fetch(PDO::FETCH_ASSOC)):
      ?>
      <tr>
        <td><?= $row['id'] ?></td>
        <td><?= htmlspecialchars($row['name']) ?></td>
        <td><?= htmlspecialchars($row['email']) ?></td>
        <td><?= $row['created_at'] ?></td>
        <td>
          <a href="edit.php?id=<?= $row['id'] ?>" class="btn btn-sm btn-warning">✏️ Editar</a>
          <a href="delete.php?id=<?= $row['id'] ?>" class="btn btn-sm btn-danger" onclick="return confirm('¿Eliminar este usuario?');">🗑️ Eliminar</a>
        </td>
      </tr>
      <?php endwhile; ?>
    </tbody>
  </table>
</div>
</body>
</html>
```

### `create.php`
```php
<?php include 'config.php'; ?>
<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
  $name = $_POST['name'];
  $email = $_POST['email'];
  $sql = "INSERT INTO users (name, email) VALUES (:name, :email)";
  $stmt = $conn->prepare($sql);
  $stmt->execute([':name' => $name, ':email' => $email]);
  header('Location: index.php');
  exit;
}
?>
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Crear Usuario</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
  <h2>Nuevo Usuario</h2>
  <form method="POST">
    <div class="mb-3">
      <label>Nombre</label>
      <input type="text" name="name" class="form-control" required>
    </div>
    <div class="mb-3">
      <label>Email</label>
      <input type="email" name="email" class="form-control" required>
    </div>
    <button class="btn btn-success">Guardar</button>
    <a href="index.php" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
</body>
</html>
```

### `edit.php`
```php
<?php include 'config.php'; ?>
<?php
$id = $_GET['id'] ?? null;
if (!$id) {
  header('Location: index.php');
  exit;
}
$stmt = $conn->prepare("SELECT * FROM users WHERE id = :id");
$stmt->execute([':id' => $id]);
$user = $stmt->fetch(PDO::FETCH_ASSOC);
if (!$user) die("Usuario no encontrado.");
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
  $name = $_POST['name'];
  $email = $_POST['email'];
  $sql = "UPDATE users SET name = :name, email = :email WHERE id = :id";
  $stmt = $conn->prepare($sql);
  $stmt->execute([':name' => $name, ':email' => $email, ':id' => $id]);
  header('Location: index.php');
  exit;
}
?>
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Editar Usuario</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
<div class="container mt-5">
  <h2>Editar Usuario</h2>
  <form method="POST">
    <div class="mb-3">
      <label>Nombre</label>
      <input type="text" name="name" value="<?= htmlspecialchars($user['name']) ?>" class="form-control" required>
    </div>
    <div class="mb-3">
      <label>Email</label>
      <input type="email" name="email" value="<?= htmlspecialchars($user['email']) ?>" class="form-control" required>
    </div>
    <button class="btn btn-primary">Actualizar</button>
    <a href="index.php" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
</body>
</html>
```

### `delete.php`
```php
<?php include 'config.php'; ?>
<?php
$id = $_GET['id'] ?? null;
if ($id) {
  $stmt = $conn->prepare("DELETE FROM users WHERE id = :id");
  $stmt->execute([':id' => $id]);
}
header('Location: index.php');
exit;
?>
```

---

## 6. Permisos y prueba

```bash
sudo chown -R www-data:www-data /var/www/html
sudo systemctl restart apache2
```

Abre tu navegador:
```
http://<IP_PUBLICA_DE_TU_EC2>
```
Deberías ver el CRUD funcionando 🎉

---

##  Resultado final

Tu servidor EC2 ahora ejecuta:
- Apache sirviendo PHP
- MySQL con base de datos `crud_php`
- CRUD funcional para la tabla `users`

Puedes acceder desde:
```
http://<IP_PUBLICA_DE_TU_EC2>
```




