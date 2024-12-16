
# UD7: Almacenamiento de Ficheros en Spring Boot

## Índice

1. [Introducción](#introducción)
2. [Preparación del Entorno](#preparación-del-entorno)
3. [Almacenamiento Local de Ficheros](#almacenamiento-local-de-ficheros)
    - [Flujo Básico](#flujo-básico)
    - [Ejemplo de Código - Subida](#ejemplo-de-código---subida)
    - [Ejemplo de Código - Descarga](#ejemplo-de-código---descarga)
4. [Seguridad y Buenas Prácticas](#seguridad-y-buenas-prácticas)
5. [Alternativas de Almacenamiento](#alternativas-de-almacenamiento)
6. [Ejercicio Práctico](#ejercicio-práctico)

---

## Introducción

En muchas aplicaciones web es necesario almacenar y gestionar ficheros: imágenes, documentos PDF, hojas de cálculo, etc. Con Spring Boot, podemos configurar rápidamente endpoints para subir, descargar y gestionar dichos ficheros. Además, podemos combinar esta funcionalidad con bases de datos, almacenamiento en la nube o almacenamiento local, según las necesidades.

**Objetivos:**

- Configurar y utilizar Spring Boot para subir y descargar ficheros.
- Implementar un servicio sencillo de almacenamiento local.
- Conocer buenas prácticas y opciones avanzadas (nube, bases de datos, etc.).

---

## Preparación del Entorno

1. **Dependencias en `pom.xml`**:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```
   
   Con esta dependencia podemos manejar peticiones HTTP, incluida la subida de ficheros `multipart`.

2. **Estructura del proyecto**:
   - `src/main/java`: Clases Java (controladores, servicios, entidades, etc.)
   - `src/main/resources`: Configuración (`application.properties`) y, opcionalmente, plantillas y recursos estáticos.

3. **Propiedad en `application.properties`**:
   ```properties
   file.upload-dir=uploads
   ```
   
   Esto configura el directorio donde se guardarán los ficheros subidos. El directorio `uploads` se creará automáticamente al intentar subir el primer fichero si no existe.  
   
   También podemos limitar el tamaño máximo de los ficheros:
   ```properties
   spring.servlet.multipart.max-file-size=10MB
   spring.servlet.multipart.max-request-size=10MB
   ```

---

## Almacenamiento Local de Ficheros

La opción más sencilla es el almacenamiento local, guardando los ficheros en el propio servidor donde corre la aplicación.

### Flujo Básico

1. **Subida**:
   - El cliente envía una petición POST `multipart/form-data` con el fichero.
   - El controlador recibe un `MultipartFile`.
   - Se guarda el fichero en el directorio configurado (`uploads`).

2. **Descarga**:
   - El cliente hace una petición GET con el nombre del fichero.
   - El controlador localiza el fichero en el sistema de ficheros.
   - Devuelve el fichero como un `Resource` descargable.

---

### Ejemplo de Código - Subida

```java
@RestController
@RequestMapping("/files")
public class FileController {

    @Value("${file.upload-dir}")
    //Inyecta el valor del directorio definido en application.properties como ruta para los ficheros
    private String uploadDir;

    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
        try {
            Path uploadPath = Paths.get(uploadDir).toAbsolutePath().normalize();
            // Obtenemos la ruta absoluta y normalizada del directorio de subida.
            Files.createDirectories(uploadPath);
            // Si el directorio no existe, lo crea.

            String fileName = UUID.randomUUID().toString() + "_" + file.getOriginalFilename();
            // Generamos un nombre único para el fichero, evitando colisiones con ficheros existentes.
            Path filePath = uploadPath.resolve(fileName);
            // Resolvemos el nombre del fichero en la ruta completa.

            Files.copy(file.getInputStream(), filePath, StandardCopyOption.REPLACE_EXISTING);
            // Copiamos el contenido del fichero subido a la ruta destino en el servidor.

            return ResponseEntity.ok("Fichero subido correctamente: " + fileName);
        } catch (IOException e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Error al subir el fichero");
        }
    }
}
```

### Ejemplo de Código - Descarga

```java
@RestController
@RequestMapping("/files")
public class FileDownloadController {

    @Value("${file.upload-dir}")
    private String uploadDir;

    @GetMapping("/download/{filename}")
    public ResponseEntity<Resource> downloadFile(@PathVariable String filename) {
        try {
            Path filePath = Paths.get(uploadDir).resolve(filename).normalize();
            //Resuelve la ruta ej: upload/miarchivo.txt
            Resource resource = new UrlResource(filePath.toUri());
            //Crea un recurso que representa al archivo en esa ubicación

            if (!resource.exists()) {
                return ResponseEntity.status(HttpStatus.NOT_FOUND).build();
            }
            // Si no existe lanzamos una respuesta no exitosa

            String contentType = Files.probeContentType(filePath);
            //Determinadmos el tipo de archivo que es, basándose en su extensión
            if (contentType == null) {
                contentType = "application/octet-stream";
            }
            //Si no consigue determinar el tipo de archivo, se le asigna el tipo octet-stream - Datos binarios genéricos

            return ResponseEntity.ok()
                    .contentType(MediaType.parseMediaType(contentType)) //Indica tipo de archivo
                    .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
                    .body(resource); //Incluimos el archivo
        } catch (IOException e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }
}
```

---

## Seguridad y Buenas Prácticas

- **Limitación de tamaño**: Configurar en `application.properties` el tamaño máximo de ficheros.
- **Validación de tipos de ficheros**: Comprobar extensiones y tipos MIME.
- **Nombres seguros**: Usar UUIDs para evitar colisiones y acceso a rutas no deseadas.
- **Directorio separado**: Mantener los ficheros subidos en una carpeta separada de la aplicación.
- **Respaldos y escalabilidad**: Considerar almacenamiento en la nube o sistemas de archivos compartidos para entornos de producción.

---

## Ejercicio Práctico

**Objetivo**: Implementar un pequeño módulo de gestión de ficheros que permita:

1. Subir un fichero (POST `/files`).
2. Listar todos los ficheros subidos (GET `/files`).
3. Descargar un fichero específico (GET `/files/{filename}`).
4. Eliminar un fichero (DELETE `/files/{filename}`).

**Pasos**:

1. Configurar `application.properties` con `file.upload-dir=uploads`.
2. Crear un servicio `FileStorageService` con métodos `store()`, `listAll()`, `load()`, `delete()`.
3. Implementar el controlador `FileManagementController` con los endpoints mencionados.
4. Probar con Postman u otra herramienta:
   - Subir un fichero y comprobar que se crea en la carpeta `uploads`.
   - Listar ficheros para ver el nombre subido.
   - Descargar y verificar el contenido.
   - Eliminar y comprobar que desaparece.

---
