# UD4: Gestión de Datos con Spring Data JPA

## Índice

- [Introducción a Spring Data JPA](#introducción-a-spring-data-jpa)
- [Configuración de Spring Data JPA en un Proyecto Spring Boot](#configuración-de-spring-data-jpa-en-un-proyecto-spring-boot)
- [Definición de Entidades](#definición-de-entidades)
  - [Anotaciones Básicas](#anotaciones-básicas)
  - [Estrategias de Generación de IDs](#estrategias-de-generación-de-ids)
- [Repositorios en Spring Data JPA](#repositorios-en-spring-data-jpa)
- [Operaciones CRUD Básicas](#operaciones-crud-básicas)
  - [Equivalencia entre SQL y JPA](#equivalencia-entre-sql-y-jpa)
- [Consultas Personalizadas](#consultas-personalizadas)
- [Relaciones entre Entidades](#relaciones-entre-entidades)
  - [Tipos de Relaciones](#tipos-de-relaciones)
  - [Ejemplo Completo de Relaciones](#ejemplo-completo-de-relaciones)
- [Gestión de Transacciones](#gestión-de-transacciones)
  - [Importancia de las Transacciones](#importancia-de-las-transacciones)
  - [Uso de `@Transactional`](#uso-de-transactional)
- [Ejemplo Práctico Ampliado](#ejemplo-práctico-ampliado)
- [Ejercicios Propuestos](#ejercicios-propuestos)
- [Conclusión](#conclusión)

---

## Introducción a Spring Data JPA

Spring Data JPA es un módulo de Spring que simplifica el acceso y gestión de datos en aplicaciones Java. Proporciona una capa de abstracción sobre JPA (Java Persistence API), facilitando la interacción con bases de datos relacionales sin necesidad de escribir código repetitivo.

### ¿Qué es JPA?

JPA es una especificación de Java que define cómo manejar datos relacionales en aplicaciones Java utilizando objetos. Permite mapear clases Java a tablas de bases de datos, facilitando las operaciones de persistencia.

### Ventajas de usar Spring Data JPA

- **Simplicidad**: Reduce la cantidad de código necesario para realizar operaciones comunes.
- **Productividad**: Proporciona métodos predefinidos para operaciones CRUD.
- **Extensibilidad**: Permite crear consultas personalizadas fácilmente.
- **Integración**: Se integra perfectamente con otros módulos de Spring.

<img src="imagenes/UD4/springdata_jpa.png" alt="Spring Data" width="600"/>


---

## Configuración de Spring Data JPA en un Proyecto Spring Boot

### Dependencias

Para utilizar Spring Data JPA, es necesario incluir las siguientes dependencias en el archivo `pom.xml`:

```xml
<dependencies>
    <!-- Dependencia de Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Controlador de la base de datos (por ejemplo, H2 o MySQL) -->
    <!-- Usando H2 en este ejemplo -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### Configuración de la Base de Datos

En el archivo `application.properties` o `application.yml`, configura la conexión a la base de datos:

```properties
# Usando H2 en memoria
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# Mostrar sentencias SQL en la consola
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Generar las tablas automáticamente
spring.jpa.hibernate.ddl-auto=update
```

---

## Definición de Entidades

Las entidades representan tablas en la base de datos. Se definen como clases Java anotadas con `@Entity`.

### Anotaciones Básicas

- `@Entity`: Indica que la clase es una entidad.
- `@Table`: Especifica el nombre de la tabla (opcional).
- `@Id`: Define la clave primaria.
- `@GeneratedValue`: Especifica la estrategia de generación de la clave primaria.
- `@Column`: Configura propiedades de la columna (opcional).

### Estrategias de Generación de IDs

La anotación `@GeneratedValue` se utiliza para indicar que el valor del ID se generará automáticamente. Existen diferentes estrategias que puedes especificar con el atributo `strategy`:

- `GenerationType.AUTO`: La estrategia es elegida por el proveedor de persistencia.
- `GenerationType.IDENTITY`: Utiliza una columna de auto-incremento en la base de datos.
- `GenerationType.SEQUENCE`: Usa una secuencia de la base de datos (requiere definir una secuencia).
- `GenerationType.TABLE`: Utiliza una tabla específica para generar los IDs (menos común).

**Ejemplo:**

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

### Ejemplo de una Entidad Simple

```java
import javax.persistence.*;

@Entity
@Table(name = "usuarios")
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "nombre", nullable = false)
    private String nombre;

    private String email;

    // Getters y Setters
}
```

---

## Repositorios en Spring Data JPA

Los repositorios son interfaces que permiten realizar operaciones sobre las entidades.

### Interfaces Principales

- `CrudRepository<T, ID>`: Proporciona métodos CRUD básicos.
- `JpaRepository<T, ID>`: Extiende `CrudRepository` y añade métodos adicionales.

### Creación de un Repositorio

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    // Métodos personalizados si es necesario
}
```

---

## Operaciones CRUD Básicas

### Equivalencia entre SQL y JPA

| Operación     | SQL               | JPA Repository                        |
|---------------|-------------------|---------------------------------------|
| **Crear**     | `INSERT INTO`     | `save(entity)`                        |
| **Leer**      | `SELECT`          | `findById(id)`, `findAll()`           |
| **Actualizar**| `UPDATE`          | `save(entity)` (si ya existe)         |
| **Eliminar**  | `DELETE`          | `delete(entity)`, `deleteById(id)`    |

### Guardar una Entidad

```java
@Autowired
private UsuarioRepository usuarioRepository;

public void crearUsuario() {
    Usuario usuario = new Usuario();
    usuario.setNombre("Juan");
    usuario.setEmail("juan@example.com");
    usuarioRepository.save(usuario);
}
```

### Obtener una Entidad

```java
public void obtenerUsuario(Long id) {
    Optional<Usuario> usuario = usuarioRepository.findById(id);
    usuario.ifPresent(u -> System.out.println(u.getNombre()));
}
```

### Actualizar una Entidad

```java
public void actualizarUsuario(Long id) {
    Usuario usuario = usuarioRepository.findById(id).orElseThrow();
    usuario.setEmail("nuevoemail@example.com");
    usuarioRepository.save(usuario);
}
```

### Eliminar una Entidad

```java
public void eliminarUsuario(Long id) {
    usuarioRepository.deleteById(id);
}
```

---

## Consultas Personalizadas

Aunque Spring Data JPA proporciona métodos CRUD básicos, a veces necesitas realizar consultas más complejas.

### Métodos Derivados

Puedes crear métodos en el repositorio siguiendo una convención de nombres. Spring Data JPA genera la implementación automáticamente.

```java
List<Usuario> findByNombre(String nombre);
List<Usuario> findByNombreAndEmail(String nombre, String email);
List<Usuario> findByNombreContaining(String fragmentoNombre);
```

### Uso de la Anotación `@Query`

Puedes utilizar la anotación `@Query` para definir consultas personalizadas utilizando JPQL (Java Persistence Query Language).

```java
@Query("SELECT u FROM Usuario u WHERE u.email = ?1")
Usuario buscarPorEmail(String email);
```

### Consultas Nativas

Si necesitas ejecutar una consulta SQL nativa, puedes hacerlo:

```java
@Query(value = "SELECT * FROM usuarios WHERE email = ?1", nativeQuery = true)
Usuario buscarPorEmailNativo(String email);
```

### Resolución con JPA

Muchas consultas personalizadas pueden resolverse utilizando los métodos derivados o JPQL proporcionados por JPA, sin necesidad de escribir SQL nativo. Esto es preferible ya que mantiene la independencia de la base de datos y aprovecha el mapeo objeto-relacional.

---

## Relaciones entre Entidades

Las entidades en JPA pueden tener relaciones entre sí. Es importante mapear estas relaciones correctamente para reflejar las asociaciones en la base de datos.

### Tipos de Relaciones

1. **One-to-One (Uno a Uno):** Un registro de una tabla se asocia con un único registro de otra tabla.
2. **One-to-Many (Uno a Muchos) y Many-to-One (Muchos a Uno):** Un registro de una tabla se asocia con múltiples registros de otra tabla, y viceversa.
3. **Many-to-Many (Muchos a Muchos):** Múltiples registros de una tabla se asocian con múltiples registros de otra tabla.

### Ejemplo Completo de Relaciones

Supongamos que estamos modelando una aplicación de comercio electrónico con las siguientes entidades:

- **Usuario**
- **Pedido**
- **Producto**

#### Definición de Entidades y Relaciones

##### Entidad Usuario

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @OneToMany(mappedBy = "usuario", cascade = CascadeType.ALL)
    private List<Pedido> pedidos;

    // Getters y Setters
}
```

- **Relación:** Un usuario puede tener **muchos pedidos** (One-to-Many).
- **`mappedBy = "usuario"`:** Indica que el campo `usuario` en `Pedido` es el propietario de la relación.
- **`cascade = CascadeType.ALL`:** Las operaciones en `Usuario` se propagan a `Pedido`.

##### Entidad Pedido

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Pedido {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String fecha;

    @ManyToOne
    @JoinColumn(name = "usuario_id")
    private Usuario usuario;

    @ManyToMany
    @JoinTable(
        name = "pedido_producto",
        joinColumns = @JoinColumn(name = "pedido_id"),
        inverseJoinColumns = @JoinColumn(name = "producto_id")
    )
    private List<Producto> productos;

    // Getters y Setters
}
```

- **Relación con Usuario:**
  - **Many-to-One:** Muchos pedidos pueden pertenecer a un usuario.
  - **`@JoinColumn(name = "usuario_id")`:** Define la clave foránea en la tabla `pedido`.
- **Relación con Producto:**
  - **Many-to-Many:** Un pedido puede contener múltiples productos y un producto puede estar en múltiples pedidos.
  - **`@JoinTable`:** Define la tabla intermedia `pedido_producto` para la relación muchos a muchos.

##### Entidad Producto

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Producto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;
    private Double precio;

    @ManyToMany(mappedBy = "productos")
    private List<Pedido> pedidos;

    // Getters y Setters
}
```

- **Relación con Pedido:**
  - **Many-to-Many:** Relación inversa al Many-to-Many definido en `Pedido`.
  - **`mappedBy = "productos"`:** Indica que el propietario de la relación es el campo `productos` en `Pedido`.

#### Ejemplo de Uso

##### Crear un Usuario, Productos y un Pedido

```java
// Crear productos
Producto producto1 = new Producto();
producto1.setNombre("Laptop");
producto1.setPrecio(1200.0);

Producto producto2 = new Producto();
producto2.setNombre("Smartphone");
producto2.setPrecio(800.0);

// Guardar productos
productoRepository.save(producto1);
productoRepository.save(producto2);

// Crear usuario
Usuario usuario = new Usuario();
usuario.setNombre("María");

// Crear pedido
Pedido pedido = new Pedido();
pedido.setFecha("2023-10-15");
pedido.setUsuario(usuario);
pedido.setProductos(Arrays.asList(producto1, producto2));

// Asociar pedido al usuario
usuario.setPedidos(Arrays.asList(pedido));

// Guardar usuario y pedido gracias a la cascada
usuarioRepository.save(usuario);
```

#### Notas Importantes

- **Cascada:** Al guardar el `usuario`, gracias a `cascade = CascadeType.ALL`, también se guardan los `pedidos` asociados.
- **Relaciones Bidireccionales:** Es importante establecer ambas partes de la relación para mantener la coherencia.

---

## Gestión de Transacciones

Las transacciones garantizan la integridad y consistencia de los datos en operaciones que involucran múltiples cambios en la base de datos.

### Importancia de las Transacciones

- **Atomicidad:** Asegura que todas las operaciones dentro de una transacción se completen correctamente o ninguna lo hace.
- **Consistencia:** Mantiene la base de datos en un estado consistente antes y después de la transacción.
- **Aislamiento:** Las transacciones concurrentes no interfieren entre sí.
- **Durabilidad:** Una vez que una transacción se compromete, sus cambios persisten incluso en caso de fallos.

### Uso de `@Transactional`

En Spring, puedes gestionar transacciones mediante la anotación `@Transactional`.

#### Ejemplo:

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class PedidoService {

    @Autowired
    private PedidoRepository pedidoRepository;

    @Autowired
    private ProductoRepository productoRepository;

    @Transactional
    public void procesarPedido(Pedido pedido) {
        // Operaciones que forman parte de la transacción
        pedidoRepository.save(pedido);
        actualizarStock(pedido.getProductos());
    }

    private void actualizarStock(List<Producto> productos) {
        for (Producto producto : productos) {
            producto.setStock(producto.getStock() - 1);
            productoRepository.save(producto);
        }
    }
}
```

- **`@Transactional`:** Indica que el método `procesarPedido` debe ejecutarse dentro de una transacción.
- **Si ocurre una excepción no controlada dentro del método, la transacción se revierte.**

---

## Ejemplo Práctico Ampliado

Desarrollaremos una aplicación para gestionar una biblioteca, con las siguientes entidades:

- **Libro**
- **Autor**
- **Editorial**

### Entidades y Relaciones

#### Entidad Autor

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Autor {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @ManyToMany(mappedBy = "autores")
    private List<Libro> libros;

    // Getters y Setters
}
```

#### Entidad Editorial

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Editorial {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @OneToMany(mappedBy = "editorial")
    private List<Libro> libros;

    // Getters y Setters
}
```

#### Entidad Libro

```java
import javax.persistence.*;
import java.util.List;

@Entity
public class Libro {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String titulo;

    @ManyToMany
    @JoinTable(
        name = "libro_autor",
        joinColumns = @JoinColumn(name = "libro_id"),
        inverseJoinColumns = @JoinColumn(name = "autor_id")
    )
    private List<Autor> autores;

    @ManyToOne
    @JoinColumn(name = "editorial_id")
    private Editorial editorial;

    // Getters y Setters
}
```

### Repositorios

```java
public interface LibroRepository extends JpaRepository<Libro, Long> {
    List<Libro> findByTituloContaining(String titulo);
}

public interface AutorRepository extends JpaRepository<Autor, Long> {
}

public interface EditorialRepository extends JpaRepository<Editorial, Long> {
}
```

### Servicio con Transacciones y Consultas Personalizadas

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class BibliotecaService {

    @Autowired
    private LibroRepository libroRepository;

    @Autowired
    private AutorRepository autorRepository;

    @Autowired
    private EditorialRepository editorialRepository;

    @Transactional
    public void agregarNuevoLibro(Libro libro, List<Autor> autores, Editorial editorial) {
        // Guardar o actualizar la editorial
        editorialRepository.save(editorial);
        libro.setEditorial(editorial);

        // Guardar o actualizar los autores
        for (Autor autor : autores) {
            autorRepository.save(autor);
        }
        libro.setAutores(autores);

        // Guardar el libro
        libroRepository.save(libro);
    }

    public List<Libro> buscarLibrosPorTitulo(String titulo) {
        return libroRepository.findByTituloContaining(titulo);
    }
}
```

### Controlador con Método Completado

```java
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/biblioteca")
public class BibliotecaController {

    @Autowired
    private BibliotecaService bibliotecaService;

    @PostMapping("/libros")
    public void agregarLibro(@RequestBody LibroDTO libroDTO) {
        // Conversión de DTO a entidades
        Libro libro = new Libro();
        libro.setTitulo(libroDTO.getTitulo());

        // Convertir lista de IDs de autores a entidades
        List<Autor> autores = autorRepository.findAllById(libroDTO.getAutoresIds());

        // Obtener o crear la editorial
        Editorial editorial = editorialRepository.findById(libroDTO.getEditorialId())
                .orElseGet(() -> {
                    Editorial nuevaEditorial = new Editorial();
                    nuevaEditorial.setNombre(libroDTO.getEditorialNombre());
                    return nuevaEditorial;
                });

        // Llamada al servicio
        bibliotecaService.agregarNuevoLibro(libro, autores, editorial);
    }

    @GetMapping("/libros")
    public List<Libro> buscarLibros(@RequestParam String titulo) {
        return bibliotecaService.buscarLibrosPorTitulo(titulo);
    }
}
```

#### Explicación del Método `agregarLibro`

- **Conversión de `LibroDTO` a entidades:**
  - Se crea una instancia de `Libro` y se asigna el título.
  - Se obtienen los autores a partir de sus IDs usando `autorRepository`.
  - Se busca la editorial por ID; si no existe, se crea una nueva.
- **Llamada al servicio:**
  - Se llama al método `agregarNuevoLibro` del servicio `BibliotecaService`, pasando las entidades correspondientes.

#### Definición de `LibroDTO`

```java
public class LibroDTO {
    private String titulo;
    private List<Long> autoresIds;
    private Long editorialId;
    private String editorialNombre; // En caso de que la editorial no exista

    // Getters y Setters
}
```

---

## Ejercicios Propuestos

### Ejercicio 1: Configuración Inicial

- **Objetivo:** Configurar un proyecto Spring Boot con Spring Data JPA y H2 como base de datos.
- **Tareas:**
  - Crea un nuevo proyecto Spring Boot.
  - Incluye las dependencias necesarias en el `pom.xml`.
  - Configura la conexión a la base de datos H2 en `application.properties`.

### Ejercicio 2: Definición de Entidades y Repositorios

- **Objetivo:** Definir entidades y repositorios básicos.
- **Tareas:**
  - Crea una entidad `Cliente` con campos `id`, `nombre`, `email`.
  - Define el repositorio `ClienteRepository`.
  - Implementa un servicio para realizar operaciones CRUD sobre `Cliente`.

### Ejercicio 3: Relaciones entre Entidades

- **Objetivo:** Implementar relaciones One-to-Many y Many-to-Many.
- **Tareas:**
  - Crea las entidades `Autor`, `Libro` y `Editorial` como en el ejemplo.
  - Establece las relaciones correspondientes entre ellas.
  - Implementa métodos para agregar libros con sus autores y editoriales.

### Ejercicio 4: Consultas Personalizadas

- **Objetivo:** Crear consultas personalizadas utilizando métodos derivados y la anotación `@Query`.
- **Tareas:**
  - En `LibroRepository`, añade un método para buscar libros por nombre de autor.
  - Utiliza `@Query` para crear una consulta que busque libros publicados por una editorial específica.

### Ejercicio 5: Gestión de Transacciones y Manejo de Excepciones

- **Objetivo:** Implementar transacciones y manejar excepciones.
- **Tareas:**
  - En el servicio de biblioteca, agrega lógica para manejar casos donde la inserción de un libro falle y la transacción deba revertirse.
  - Configura `@Transactional` para que también realice rollback en excepciones controladas.
  - Implementa pruebas para verificar que los datos se mantienen consistentes en caso de errores.

---

## Conclusión

Spring Data JPA simplifica enormemente el manejo de datos en aplicaciones Spring Boot. Al entender cómo definir entidades, mapear relaciones y gestionar transacciones, puedes desarrollar aplicaciones robustas y eficientes. Las consultas personalizadas y el manejo adecuado de las transacciones permiten crear aplicaciones complejas de manera sencilla y segura.

---

**Referencias:**

- [Documentación Oficial de Spring Data JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
