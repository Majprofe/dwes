# U3: Controladores, Rutas y el Patrón MVC en Spring Boot

## Índice

1. [Introducción al Patrón MVC](#introducción-al-patrón-mvc)
   - [1.1 ¿Qué es MVC?](#11-qué-es-mvc)
   - [1.2 Beneficios de Usar MVC](#12-beneficios-de-usar-mvc)
2. [MVC en Spring Boot](#mvc-en-spring-boot)
   - [2.1 Componentes de Spring MVC](#21-componentes-de-spring-mvc)
3. [Controladores en Spring Boot](#controladores-en-spring-boot)
   - [3.1 Creación de un Controlador](#31-creación-de-un-controlador)
   - [3.2 Anotaciones Comunes](#32-anotaciones-comunes)
4. [Uso de ResponseEntity](#uso-de-responseentity)
5. [Mapeo de Rutas y Solicitudes](#mapeo-de-rutas-y-solicitudes)
   - [5.1 Anotaciones de Mapeo](#51-anotaciones-de-mapeo)
   - [5.2 Manejo de Parámetros](#52-manejo-de-parámetros)
6. [Vistas y Plantillas](#vistas-y-plantillas)
   - [6.1 Introducción a Thymeleaf](#61-introducción-a-thymeleaf)
   - [6.2 Integración de Thymeleaf en Spring Boot](#62-integración-de-thymeleaf-en-spring-boot)
7. [Pasando Datos entre Modelo y Vista](#pasando-datos-entre-controlador-y-vista)
8. [Ejercicios](#ejercicios)


---

## Introducción al Patrón MVC

### 1.1 ¿Qué es MVC?

El **Patrón MVC (Modelo-Vista-Controlador)** es un patrón de arquitectura de software que separa una aplicación en tres componentes principales:

- **Modelo:** Representa los datos y la lógica de negocio.
- **Vista:** Presenta los datos al usuario y maneja la interfaz de usuario.
- **Controlador:** Gestiona las solicitudes del usuario, interactúa con el modelo y selecciona la vista apropiada para presentar la respuesta.

<img src="imagenes/UD3/mvc_pattern.png" alt="Patrón MVC" width="600"/>

### 1.2 Beneficios de Usar MVC

- **Separación de Responsabilidades:** Facilita el mantenimiento y escalabilidad.
- **Reutilización de Código:** Los componentes pueden reutilizarse en diferentes partes de la aplicación.
- **Facilita el Trabajo en Equipo:** Los desarrolladores pueden trabajar en diferentes componentes simultáneamente.

---

## MVC en Spring Boot

### 2.1 Componentes de Spring MVC

- **Modelo:** Objetos Java que representan los datos.
- **Controlador:** Clases anotadas que manejan las solicitudes HTTP y devuelven respuestas, hace de intermediario entre el modelo y la vista.
- **Vista:** Plantillas que generan la interfaz de usuario (por ejemplo, Thymeleaf, JSP).

<img src="imagenes/UD3/spring_mvc_architecture.png" alt="Arquitectura Spring MVC" width="600"/>

---

## Controladores en Spring Boot

### 3.1 Creación de un Controlador

En Spring Boot, un controlador es una clase anotada con `@Controller` o `@RestController`.

**Ejemplo de Controlador con Modelo:**

```java
// Modelo: Producto
package com.ejemplo.demo;

public class Producto {
    private String nombre;
    private double precio;

    // Constructor
    public Producto(String nombre, double precio) {
        this.nombre = nombre;
        this.precio = precio;
    }

    // Getters y Setters
    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public double getPrecio() {
        return precio;
    }

    public void setPrecio(double precio) {
        this.precio = precio;
    }
}

// Controlador: ProductoController
package com.ejemplo.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ProductoController {

    @GetMapping("/producto")
    public String verProducto(Model model) {
        Producto producto = new Producto("Camiseta", 19.99);
        model.addAttribute("producto", producto);
        return "detalleProducto"; // Retorna una vista llamada "detalleProducto.html"
    }
}
```

### 3.2 Anotaciones Comunes

- `@Controller`: Indica que la clase es un controlador, estos métodos devuelven vistas.
- `@RestController`: Combina `@Controller` y `@ResponseBody`, apropiado para APIs REST, que devuelven datos (JSON o XML).
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: Especifican el método HTTP y la ruta para el manejo de solicitudes.

## Uso de ResponseEntity

Antes de continuar, es muy recomendable revisar este artículo sobre los diferentes [métodos HTTP](https://www.dariawan.com/tutorials/rest/http-methods-spring-restful-services/) en servicios web REST. Explica claramente cómo deben ser las operaciones que se pueden llevar a cabo sobre los recursos del sistema.

Las operaciones devuelven un [código de estado HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Status) correspondiente. Esto lo logramos con el uso de `ResponseEntity` en el controlador.

### Ejemplo básico de uso:

```java
@RestController
@RequestMapping("/api")
public class UsuarioController {

    @GetMapping("/usuario/{id}")
    public ResponseEntity<Usuario> obtenerUsuario(@PathVariable Long id) {
        Usuario usuario = usuarioService.findById(id);
        if (usuario != null) {
            return ResponseEntity.ok(usuario); // 200 OK
        } else {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body(null); // 404 Not Found
        }
    }
}
```
En este ejemplo, `ResponseEntity.ok(usuario)` devuelve una respuesta 200 con el objeto `Usuario`. Si no se encuentra, devuelve un 404 (Not Found).

### Personalización de cabeceras
```java
@GetMapping("/usuario/{id}")
public ResponseEntity<Usuario> obtenerUsuarioConCabecera(@PathVariable Long id) {
    Usuario usuario = usuarioService.findById(id);
    if (usuario != null) {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Custom-Header", "value");
        return ResponseEntity.ok().headers(headers).body(usuario); // 200 OK con cabecera personalizada
    } else {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(null); // 404 Not Found
    }
}
```
Podemos añadir una cabecera personalizada utilizando `HttpHeaders` para agregar información adicional a la respuesta.

### Códigos de estado personalizados
```java
@PostMapping("/usuario")
public ResponseEntity<String> crearUsuario(@RequestBody Usuario usuario) {
    if (usuarioService.existe(usuario)) {
        return ResponseEntity.status(HttpStatus.CONFLICT).body("Usuario ya existe"); // 409 Conflict
    } else {
        usuarioService.save(usuario);
        return ResponseEntity.status(HttpStatus.CREATED).body("Usuario creado"); // 201 Created
    }
}
```
Este ejemplo muestra cómo devolver diferentes códigos de estado (`201 Created` o `409 Conflict`) dependiendo de la operación realizada.

### Métodos comunes de la clase ResponseEntity
1. `ok()`: Devuelve un ResponseEntity con un estado `200 OK`.
```java
return ResponseEntity.ok(data);
```

2. `status(HttpStatus)`: Permite especificar un estado HTTP personalizado.
```java
return ResponseEntity.status(HttpStatus.CREATED).body(data);
```

3. `badRequest()`: Devuelve un estado `400 Bad Request`.
```java
return ResponseEntity.badRequest().build();
```

4. `notFound()`: Devuelve un estado `404 Not Found`.
```java
return ResponseEntity.notFound().build();
```
5. `build()`: Crea un `ResponseEntity` vacío.
```java
return ResponseEntity.status(HttpStatus.CREATED).build(); //Sólo devuelve el estado HTTP sin cuerpo en la respuesta
```

---

## Mapeo de Rutas y Solicitudes

### 5.1 Anotaciones de Mapeo

- **`@RequestMapping`**: Define rutas y métodos HTTP.

  ```java
  @RequestMapping(value = "/usuarios", method = RequestMethod.GET)
  ```

- **Atajos:**
  - `@GetMapping("/ruta")`
  - `@PostMapping("/ruta")`
  - `@PutMapping("/ruta")`
  - `@DeleteMapping("/ruta")`

### 5.2 Manejo de Parámetros

- **Parámetros de Ruta (Path Variables):**

  ```java
  @GetMapping("/usuarios/{id}")
  public String obtenerUsuario(@PathVariable("id") Long id) {
      // Lógica para obtener usuario por ID
  }
  ```
La llamada desde el cliente sería: `http://localhost:8080/usuarios/5`

- **Parámetros de Consulta (Query Parameters):**

  ```java
  @GetMapping("/buscar")
  public String buscar(@RequestParam("q") String consulta) {
      // Lógica de búsqueda
  }
  ```
La llamada desde el cliente sería: `http://localhost:8080/buscar?q=camiseta`

- **Parámetros en el Body (Body Parameters):**

  ```java
  @PostMapping("/addreserva")
  public String buscar(@RequestBody ReservaDTO reserva) {
      // Lógica para añadir la reserva
  }
  ```
La llamada desde el cliente sería: `http://localhost:8080/addreserva`

---

## Vistas y Plantillas

### 6.1 Introducción a Thymeleaf

**Thymeleaf** es un motor de plantillas moderno y versátil para Java, integrado fácilmente con Spring Boot.

- Sintaxis sencilla y expresiva.
- Soporta internacionalización.
- Permite previsualizar plantillas sin necesidad de ejecutar la aplicación.

### 6.2 Integración de Thymeleaf en Spring Boot

Spring Boot incluye soporte para Thymeleaf por defecto cuando se agrega la dependencia.

**Agregar Dependencia en `pom.xml`:**

```xml
<dependencies>
    <!-- Otras dependencias -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
</dependencies>
```

**Estructura de Directorios:**

- Las plantillas Thymeleaf deben ubicarse en `src/main/resources/templates/`.

**Ejemplo de Plantilla `detalleProducto.html`:**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Detalle Producto</title>
</head>
<body>
    <h1>Detalles del Producto</h1>
    <p>Nombre: <span th:text="${producto.nombre}"></span></p>
    <p>Precio: <span th:text="${producto.precio}"></span></p>
</body>
</html>
```

---

## Pasando Datos entre Controlador y Vista

En Spring MVC, los datos se pasan del controlador a la vista mediante el objeto `Model`.

**Ejemplo:**

```java
@GetMapping("/producto")
public String verProducto(Model model) {
    Producto producto = new Producto("Camiseta", 19.99);
    model.addAttribute("producto", producto);
    return "detalleProducto"; // Retorna una vista llamada "detalleProducto.html"
}
```

En la plantilla `detalleProducto.html`, puedes acceder al objeto `producto`:

```html
<p>Nombre: <span th:text="${producto.nombre}"></span></p>
<p>Precio: <span th:text="${producto.precio}"></span></p>
```

---

## Ejercicios

1. **Arquitectura Hexagonal**
    - Busca información sobre la arquitectura hexagonal y realiza un resumen de la misma.

2. **Creación de un Controlador y Vista:**
   - Crea un controlador que maneje la ruta `/bienvenida`.
   - Pasa un mensaje de bienvenida desde el controlador a la vista.
   - Crea una plantilla Thymeleaf que muestre el mensaje.

3. **Manejo de Parámetros de Ruta:**
   - Crea una ruta que reciba el nombre de un usuario como parámetro: `/usuario/{nombre}`.
   - El controlador debe saludar al usuario por su nombre.
   - Muestra el saludo en una vista.

4. **Lista de Elementos en una Vista:**
   - En el controlador, crea una lista de objetos (por ejemplo, productos, libros, etc.).
   - Pasa la lista al modelo.
   - En la vista, muestra la lista utilizando un bucle de Thymeleaf.

   **Ejemplo en la Vista:**

   ```html
   <ul>
       <li th:each="item : ${lista}">
           <span th:text="${item.nombre}">Nombre</span> - 
           <span th:text="${item.precio}">Precio</span>
       </li>
   </ul>
   ```

5. **Formulario y Manejo de Datos POST:**
   - Crea un formulario en una vista para agregar un nuevo elemento.
   - Configura el controlador para manejar la solicitud POST y recibir los datos.
   - Muestra una confirmación o agrega el elemento a una lista existente.

---