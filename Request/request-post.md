Si enviaste los datos con **Content-Type: multipart/form-data**, en el archivo de destino (`_layout.php`), debes acceder a los valores a través de `$_POST` o `$_FILES` (si enviaste archivos).  

---

### **Ejemplo de Envío con `multipart/form-data`**
Si usaste `file_get_contents()` con un contexto como este:

```php
$data = [
    "tab" => "misbases",
    "user" => $user,
    "rol" => $rol
];

$boundary = uniqid();
$delimiter = '------' . $boundary;
$body = '';

foreach ($data as $name => $value) {
    $body .= "--$delimiter\r\n";
    $body .= "Content-Disposition: form-data; name=\"$name\"\r\n\r\n";
    $body .= "$value\r\n";
}

$body .= "--$delimiter--\r\n";

$options = [
    "http" => [
        "header" => "Content-Type: multipart/form-data; boundary=$boundary",
        "method" => "POST",
        "content" => $body
    ]
];

$context = stream_context_create($options);
$response = file_get_contents("http://localhost/shared/_layout.php", false, $context);
```

---

### **Cómo recibir los datos en `_layout.php`**
En `_layout.php`, puedes acceder a los valores con `$_POST`:

```php
$tab = $_POST['tab'] ?? null;
$user = $_POST['user'] ?? null;
$rol = $_POST['rol'] ?? null;

echo "Tab: " . $tab . "<br>";
echo "User: " . $user . "<br>";
echo "Rol: " . $rol . "<br>";
```

---

### **Si estás enviando archivos**
Si también enviaste un archivo, debes acceder a él con `$_FILES`:

```php
if (!empty($_FILES['archivo']['name'])) {
    $nombreArchivo = $_FILES['archivo']['name'];
    $tipoArchivo = $_FILES['archivo']['type'];
    $tmpArchivo = $_FILES['archivo']['tmp_name'];

    move_uploaded_file($tmpArchivo, "uploads/" . $nombreArchivo);
    echo "Archivo subido: " . $nombreArchivo;
}
```

---

### **Resumen**
- **Datos enviados en `multipart/form-data`** → Usa `$_POST` para acceder a los valores.
- **Si se envían archivos** → Usa `$_FILES` para obtener la información del archivo.
- **El `Content-Type` correcto en la solicitud** debe ser `"multipart/form-data; boundary=$boundary"`.

Si sigues sin recibir los datos correctamente, dime cómo los estás enviando y te ayudo a corregirlo. 🚀
