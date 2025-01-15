# UD5: Gestión de Datos con Spring Data JPA

## Índice

1. [Introducción a Spring Data JPA](#introducción-a-spring-data-jpa)
   - 1.1. [¿Qué es JPA?](#qué-es-jpa)
   - 1.2. [Ventajas de usar Spring Data JPA](#ventajas-de-usar-spring-data-jpa)

2. [Configuración de Spring Data JPA en un Proyecto Spring Boot](#configuración-de-spring-data-jpa-en-un-proyecto-spring-boot)
   - 2.1. [Dependencias](#dependencias)
   - 2.2. [Configuración de la Base de Datos](#configuración-de-la-base-de-datos)

3. [Definición de Entidades](#definición-de-entidades)
   - 3.1. [Anotaciones Básicas](#anotaciones-básicas)
   - 3.2. [Estrategias de Generación de IDs](#estrategias-de-generación-de-ids)
   - 3.3. [Ejemplo de una Entidad Simple](#ejemplo-de-una-entidad-simple)

4. [Repositorios en Spring Data JPA](#repositorios-en-spring-data-jpa)
   - 4.1. [Interfaces Principales](#interfaces-principales)
   - 4.2. [Creación de un Repositorio](#creación-de-un-repositorio)

5. [Operaciones CRUD Básicas](#operaciones-crud-básicas)
   - 5.1. [Equivalencia entre SQL y JPA](#equivalencia-entre-sql-y-jpa)
   - 5.2. [Operaciones CRUD](#operaciones-crud)
      - 5.2.1. [Guardar una Entidad](#guardar-una-entidad)
      - 5.2.2. [Obtener una Entidad](#obtener-una-entidad)
      - 5.2.3. [Actualizar una Entidad](#actualizar-una-entidad)
      - 5.2.4. [Eliminar una Entidad](#eliminar-una-entidad)

6. [Consultas Personalizadas](#consultas-personalizadas)
   - 6.1. [Métodos Derivados](#métodos-derivados)
   - 6.2. [Uso de la Anotación @Query](#uso-de-la-anotación-query)
   - 6.3. [Consultas Nativas](#consultas-nativas)

7. [Relaciones entre Entidades](#relaciones-entre-entidades)
   - 7.1. [Anotaciones para Mapear Relaciones en JPA](#anotaciones-para-mapear-relaciones-en-jpa)
      - 7.1.1. [@OneToOne](#onetoone)
      - 7.1.2. [@OneToMany y @ManyToOne](#onetomany-y-manytoone)
      - 7.1.3. [@ManyToMany](#manytomany)
   - 7.2. [Clave Compuesta con @EmbeddedId](#clave-compuesta-con-embeddedid)

8. [Ejemplo Práctico Ampliado](#ejemplo-práctico-ampliado)
   - 8.1. [Entidades y Relaciones](#entidades-y-relaciones)
      - 8.1.1. [Entidad Autor](#entidad-autor)
      - 8.1.2. [Entidad Editorial](#entidad-editorial)
      - 8.1.3. [Entidad Libro](#entidad-libro)
   - 8.2. [Repositorios](#repositorios)
   - 8.3. [Servicio con Transacciones y Consultas Personalizadas](#servicio-con-transacciones-y-consultas-personalizadas)
   - 8.4. [Controlador con Método Completado](#controlador-con-método-completado)
   - 8.5. [Definición de LibroDTO](#definición-de-librodto)

9. [Ejercicios Propuestos](#ejercicios-propuestos)

10. [Referencias](#referencias)



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

<img src="imagenes/UD5/springdata_jpa.png" alt="Spring Data" width="600"/>

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
    <!-- Usando MySQL en este ejemplo -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.32</version> <!-- Ajusta la versión que requieras -->
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

También puedes configurar una base de datos MySQL:

```properties
# Configuración de la base de datos MySQL
spring.datasource.url=jdbc:mysql://localhost:3306/nombre_base_datos
spring.datasource.username=nombre_usuario
spring.datasource.password=contraseña

# Dialecto de Hibernate para MySQL
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect

# Mostrar las consultas SQL generadas (opcional)
spring.jpa.show-sql=true

# Modo de actualización de la base de datos
spring.jpa.hibernate.ddl-auto=update
```

**Explicación de los parámetros:**

- `spring.datasource.url`: La URL de conexión a la base de datos.
- `spring.jpa.database-platform`: El dialecto específico de MySQL para que Hibernate sepa cómo generar las consultas SQL.
- `spring.jpa.hibernate.ddl-auto`: Controla cómo Hibernate debe gestionar el esquema de la base de datos.

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

    @Column(name = "nombre", nullable = false, length = 150)
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

| Operación      | SQL               | JPA Repository                        |
|----------------|-------------------|---------------------------------------|
| **Crear**      | `INSERT INTO`     | `save(entity)`                        |
| **Leer**       | `SELECT`          | `findById(id)`, `findAll()`           |
| **Actualizar** | `UPDATE`          | `save(entity)` (si ya existe)         |
| **Eliminar**   | `DELETE`          | `delete(entity)`, `deleteById(id)`    |

[Documentación de métodos de JPA](https://www.tutorialspoint.com/spring_boot_jpa/spring_boot_jpa_repository_methods.htm)

### Operaciones CRUD

#### Guardar una Entidad

```java
@Service
public class UsuarioService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    public Usuario crearUsuario(Usuario usuario) {
        return usuarioRepository.save(usuario);
    }
}
```

#### Obtener una Entidad

```java
public Usuario obtenerUsuario(Long id) {
    return usuarioRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Usuario no encontrado"));
}
```

#### Actualizar una Entidad

```java
public Usuario actualizarUsuario(Long id, Usuario nuevosDatos) {
    Usuario usuario = obtenerUsuario(id);
    usuario.setNombre(nuevosDatos.getNombre());
    usuario.setEmail(nuevosDatos.getEmail());
    return usuarioRepository.save(usuario);
}
```

#### Eliminar una Entidad

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

[Palabras clave para métodos de consulta en JPA](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html).

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

---

## Relaciones entre Entidades

En JPA, las entidades pueden tener relaciones entre sí. Estas relaciones reflejan cómo los datos en diferentes tablas de la base de datos están vinculados entre ellos. Spring Data JPA proporciona anotaciones que nos permiten modelar estas relaciones de una manera sencilla.

### Anotaciones para Mapear Relaciones en JPA

#### **@OneToOne**

Se utiliza cuando un registro de una entidad está relacionado con un único registro de otra entidad.

```java
@Entity
public class Perfil {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String biografia;

    @OneToOne(mappedBy = "perfil") //Indica que la clave foránea está en la clase Usuario
    private Usuario usuario;

    // Getters y Setters
}
```

```java
@Entity
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @OneToOne
    @JoinColumn(name = "perfil_id")
    private Perfil perfil;

    // Getters y Setters
}
```

En este caso, la tabla `Usuario` tendrá una columna `perfil_id` que actúa como clave foránea para relacionar al usuario con un perfil en la tabla `Perfil`. La entidad `Usuario` es la propietaria de la relación, y el campo `mappedBy = "perfil"` indica que el atributo que gestiona esta relación está en la entidad `Perfil`.

#### **@OneToMany y @ManyToOne**
Estas anotaciones definen relaciones donde una entidad puede tener varios registros relacionados con otra. En una relación `OneToMany`, el lado "uno" de la relación suele ser propietario de la relación, y en la base de datos se define mediante una clave foránea en la entidad "muchos". Por ejemplo, un cliente puede tener muchos pedidos:

```java
@Entity
public class Cliente {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @OneToMany(mappedBy = "cliente", cascade = CascadeType.ALL)
    private List<Pedido> pedidos;

    // Getters y Setters
}
```

```java
@Entity
public class Pedido {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String producto;

    @ManyToOne
    @JoinColumn(name = "cliente_id")
    private Cliente cliente;

    // Getters y Setters
}
```

#### **@ManyToMany**

Define relaciones donde múltiples registros de una entidad están relacionados con múltiples registros de otra entidad.

```java
@Entity
public class Estudiante {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    @ManyToMany
    @JoinTable(
        name = "estudiante_curso",
        joinColumns = @JoinColumn(name = "estudiante_id"),
        inverseJoinColumns = @JoinColumn(name = "curso_id")
    )
    private List<Curso> cursos;

    // Getters y Setters
}
```

```java
@Entity
public class Curso {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String titulo;

    @ManyToMany(mappedBy = "cursos")
    private List<Estudiante> estudiantes;

    // Getters y Setters
}
```

En este caso, la anotación `@JoinTable` se utiliza para crear una tabla intermedia llamada `estudiante_curso` en la base de datos. Esta tabla contiene dos columnas: `estudiante_id` y `curso_id`, que son claves foráneas referenciando los identificadores de las tablas `Estudiante` y `Curso` respectivamente.

### Clave Compuesta con `@EmbeddedId`
A veces, necesitamos que una tabla tenga más de una columna como clave primaria, es decir, una **clave compuesta**. En JPA, esto se logra con dos anotaciones principales:
- **`@Embeddable`**: Para crear una clase que representa las columnas de la clave compuesta.
- **`@EmbeddedId`**: Para usar esa clase como la clave primaria en una entidad.
  
#### Paso 1: Crear la Clase Embebida con `@Embeddable`
Esta clase contendrá las columnas que forman la clave compuesta, como por ejemplo los IDs de dos entidades relacionadas.
```java
@Embeddable
public class MatriculaId implements Serializable {
    private Long estudianteId;
    private Long asignaturaId;
    // Constructores, Getters y Setters
    // Sobrescribir equals() y hashCode()
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        MatriculaId that = (MatriculaId) o;
        return estudianteId.equals(that.estudianteId) && asignaturaId.equals(that.asignaturaId);
    }
    @Override
    public int hashCode() {
        return Objects.hash(estudianteId, asignaturaId);
    }
}
```
- Implementa `equals()` y `hashCode()` para garantizar la correcta comparación y uso en estructuras de datos como `Set` o `Map`.

#### Paso 2: Usar `@EmbeddedId` en la Entidad
En la entidad `Matricula`, usamos la clase `MatriculaId` como clave primaria.
```java
@Entity
public class Matricula {
    @EmbeddedId
    private MatriculaId id;
    @ManyToOne
    @JoinColumn(name = "estudiante_id")
    @MapsId("estudianteId")
    private Estudiante estudiante;
    @ManyToOne
    @JoinColumn(name = "asignatura_id")
    @MapsId("asignaturaId")
    private Asignatura asignatura;
    private String periodo;
    // Constructores, Getters y Setters
}
```
- `@MapsId`: Enlaza cada una de las claves foráneas con los campos de `MatriculaId`

---

## Ejemplo Práctico Ampliado

Desarrollaremos una aplicación para gestionar una biblioteca, con las siguientes entidades:

- **Libro**
- **Autor**
- **Editorial**

### Entidades y Relaciones

#### Entidad Autor

```java
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
@Service
public class BibliotecaService {

    @Autowired
    private LibroRepository libroRepository;

    @Autowired
    private AutorRepository autorRepository;

    @Autowired
    private EditorialRepository editorialRepository;

    @Transactional
    public void agregarNuevoLibro(LibroDTO libroDTO) {
        // Convertir DTO a entidad Libro
        Libro libro = new Libro();
        libro.setTitulo(libroDTO.getTitulo());

        // Asignar editorial
        Editorial editorial = editorialRepository.findById(libroDTO.getEditorialId())
                .orElseThrow(() -> new ResourceNotFoundException("Editorial no encontrada"));
        libro.setEditorial(editorial);

        // Asignar autores
        List<Autor> autores = autorRepository.findAllById(libroDTO.getAutoresIds());
        if (autores.isEmpty()) {
            throw new ResourceNotFoundException("Autores no encontrados");
        }
        libro.setAutores(autores);

        // Guardar libro
        libroRepository.save(libro);
    }

    public List<Libro> buscarLibrosPorTitulo(String titulo) {
        return libroRepository.findByTituloContaining(titulo);
    }
}
```

### Controlador con Método Completado

```java
@RestController
@RequestMapping("/biblioteca")
public class BibliotecaController {

    @Autowired
    private BibliotecaService bibliotecaService;

    @PostMapping("/libros")
    public ResponseEntity<String> agregarLibro(@RequestBody LibroDTO libroDTO) {
        bibliotecaService.agregarNuevoLibro(libroDTO);
        return ResponseEntity.ok("Libro agregado exitosamente");
    }

    @GetMapping("/libros")
    public List<Libro> buscarLibros(@RequestParam String titulo) {
        return bibliotecaService.buscarLibrosPorTitulo(titulo);
    }
}
```

### Definición de `LibroDTO`

```java
public class LibroDTO {
    private String titulo;
    private List<Long> autoresIds;
    private Long editorialId;

    // Getters y Setters
}
```

---

## Ejercicios Propuestos

### Ejercicio 1: Configuración Inicial

**Tareas:**

1. Crea un nuevo proyecto Spring Boot.
2. Incluye las dependencias necesarias en el `pom.xml`.
3. Configura la conexión a la base de datos H2 en `application.properties`.

---

### Ejercicio 2: Definición de Entidades y Repositorios

**Tareas:**

1. Crea una entidad `Cliente` con los siguientes campos:
    - `id` (Long)
    - `nombre` (String)
    - `email` (String)
2. Define el repositorio `ClienteRepository`.
3. Implementa un servicio para realizar operaciones CRUD sobre `Cliente`.

---

### Ejercicio 3: Crear las entidades Viaje y PaqueteTuristico y relaciones

**Tareas:**

1. Crea una entidad `Viaje` con los siguientes campos:
    - `id` (Long)
    - `destino` (String)
    - `fechaSalida` (LocalDate)
    - `fechaRegreso` (LocalDate)
    - `precio` (float)
2. Crea una entidad `PaqueteTuristico` con los siguientes campos:
    - `id` (Long)
    - `nombre` (String)
    - `descripcion` (String)
    - `precioTotal` (float)
3. Establece las relaciones:
    - Un `Cliente` puede tener muchos `Viajes` (`@OneToMany`).
    - Un `PaqueteTuristico` puede tener varios `Viajes` (`@OneToMany`).
    - Un `Cliente` puede reservar varios `PaqueteTuristico`s y un `PaqueteTuristico` puede ser reservado por varios clientes (`@ManyToMany`).
4. Genera las tablas correspondientes en la base de datos utilizando JPA.

---

### Ejercicio 4: CRUD de Viaje

**Tareas:**

1. Crea un servicio que permita realizar operaciones CRUD sobre `Viaje`.
2. Crea un controlador con endpoints para cada operación:
    - Crear un nuevo viaje.
    - Obtener todos los viajes.
    - Actualizar un viaje existente.
    - Eliminar un viaje.
3. Añade un endpoint para que el cliente pueda listar todos los viajes que ha reservado.

---

### Ejercicio 5: Búsqueda de viajes por destino o rango de precios

**Tareas:**

1. Implementa un método en `ViajeRepository` para buscar viajes por destino.
2. Implementa un método para buscar viajes dentro de un rango de precios.
3. Añade un controlador que permita a los usuarios buscar viajes utilizando estos filtros.

---

### Ejercicio 6: Asignación de paquetes turísticos a clientes

**Tareas:**

1. Crea un controlador que permita asignar un `PaqueteTuristico` a un `Cliente`.
2. Implementa una funcionalidad para mostrar los detalles de un `PaqueteTuristico` junto con los viajes incluidos.
3. Asegúrate de que los clientes puedan ver los paquetes que han reservado.

---

### Ejercicio 7: Calcular el precio total de los viajes de un cliente

**Tareas:**

1. En el servicio de `Cliente`, implementa un método que calcule el precio total de todos los viajes reservados por un cliente.
2. Añade un endpoint en el controlador para mostrar el precio total de los viajes del cliente.

---

**Referencias:**

- [Documentación Oficial de Spring Data JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
