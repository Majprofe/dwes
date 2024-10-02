
## **Proyecto: Aplicación de Gestión de Eventos Deportivos**

### **Descripción del Proyecto**

El objetivo es desarrollar una aplicación para gestionar eventos deportivos locales. Los usuarios podrán registrarse, crear eventos deportivos, inscribirse en eventos existentes y ver información detallada de cada evento. Esto permitirá aplicar todos los conceptos aprendidos hasta ahora, incluyendo el manejo de entidades, relaciones, repositorios, servicios, controladores y transacciones con **Spring Data JPA** y **MySQL**.

### **Objetivos Específicos**

- **Configuración de un proyecto Spring Boot** con conexión a una base de datos MySQL.
- **Definición de entidades** con relaciones entre ellas.
- **Creación de repositorios** y consultas personalizadas.
- **Implementación de servicios** con lógica de negocio y manejo de transacciones.
- **Desarrollo de controladores REST** para exponer la funcionalidad de la aplicación.
- **Aplicación de validaciones** y manejo de excepciones.

---

## **Requisitos del Proyecto**

### **Entidades Principales**

1. **Usuario**

   - **Campos:**
     - `id`: Long
     - `nombre`: String
     - `email`: String
     - `contraseña`: String
     - `fechaRegistro`: LocalDateTime
   - **Relaciones:**
     - One-to-Many con `Inscripcion` (un usuario puede tener muchas inscripciones).

2. **Evento**

   - **Campos:**
     - `id`: Long
     - `nombre`: String
     - `descripcion`: String
     - `fecha`: LocalDateTime
     - `lugar`: String
     - `capacidad`: Integer
     - `organizador`: Usuario
   - **Relaciones:**
     - Many-to-One con `Usuario` (organizador del evento).
     - One-to-Many con `Inscripcion` (un evento puede tener muchas inscripciones).

3. **Inscripcion**

   - **Campos:**
     - `id`: Long
     - `fechaInscripcion`: LocalDateTime
   - **Relaciones:**
     - Many-to-One con `Usuario` (usuario inscrito).
     - Many-to-One con `Evento` (evento al que se inscribe).

### **Funcionalidades Requeridas**

- **Registro y Autenticación de Usuarios** (puede ser simulado sin implementar seguridad completa).
- **Creación de Eventos:**
  - Los usuarios pueden crear nuevos eventos deportivos.
- **Inscripción en Eventos:**
  - Los usuarios pueden inscribirse en eventos existentes si hay cupo disponible.
- **Consulta de Eventos:**
  - Listar todos los eventos disponibles.
  - Ver detalles de un evento específico.
- **Administración de Eventos:**
  - Los organizadores pueden editar o eliminar sus eventos.

### **Consideraciones Técnicas**

- **Base de Datos:**
  - Utilizar **MySQL** como sistema de gestión de base de datos.
- **ORM:**
  - Utilizar **Spring Data JPA** para el mapeo objeto-relacional.
- **Transacciones:**
  - Gestionar transacciones para operaciones críticas (por ejemplo, inscripción a eventos).
- **Validaciones:**
  - Aplicar validaciones en los datos de entrada (por ejemplo, capacidad del evento, datos del usuario).
- **Manejo de Excepciones:**
  - Manejar adecuadamente las excepciones y errores que puedan ocurrir.
- **Documentación:**
  - Documentar las API REST utilizando Swagger o similar (opcional).

---

## **Pasos para el Desarrollo**

### **1. Configuración del Proyecto**

- **Crear un nuevo proyecto Spring Boot** utilizando Spring Initializr o tu IDE preferido.
- **Dependencias necesarias:**
  - Spring Web
  - Spring Data JPA
  - MySQL Driver
  - (Opcional) Spring Boot DevTools, Lombok

### **2. Configuración de la Base de Datos MySQL**

- **Instalar MySQL** si no está instalado.
- **Crear una base de datos** llamada `eventos_db` (o el nombre que prefieras).
- **Configurar las credenciales** en el archivo `application.properties`:

  ```properties
  spring.datasource.url=jdbc:mysql://localhost:3306/eventos_db?useSSL=false&serverTimezone=UTC
  spring.datasource.username=tu_usuario
  spring.datasource.password=tu_contraseña
  spring.jpa.hibernate.ddl-auto=update
  spring.jpa.show-sql=true
  spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
  ```

### **3. Definición de Entidades**

#### **Entidad Usuario**

```java
import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.List;

@Entity
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;
    private String email;
    private String contraseña;
    private LocalDateTime fechaRegistro;

    @OneToMany(mappedBy = "usuario", cascade = CascadeType.ALL)
    private List<Inscripcion> inscripciones;

    @OneToMany(mappedBy = "organizador", cascade = CascadeType.ALL)
    private List<Evento> eventosOrganizados;

    // Getters y Setters
}
```

#### **Entidad Evento**

```java
import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.List;

@Entity
public class Evento {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;
    private String descripcion;
    private LocalDateTime fecha;
    private String lugar;
    private Integer capacidad;

    @ManyToOne
    @JoinColumn(name = "organizador_id")
    private Usuario organizador;

    @OneToMany(mappedBy = "evento", cascade = CascadeType.ALL)
    private List<Inscripcion> inscripciones;

    // Getters y Setters
}
```

#### **Entidad Inscripcion**

```java
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
public class Inscripcion {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private LocalDateTime fechaInscripcion;

    @ManyToOne
    @JoinColumn(name = "usuario_id")
    private Usuario usuario;

    @ManyToOne
    @JoinColumn(name = "evento_id")
    private Evento evento;

    // Getters y Setters
}
```

### **4. Creación de Repositorios**

#### **UsuarioRepository**

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    Usuario findByEmail(String email);
}
```

#### **EventoRepository**

```java
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface EventoRepository extends JpaRepository<Evento, Long> {
    List<Evento> findByNombreContaining(String nombre);
}
```

#### **InscripcionRepository**

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface InscripcionRepository extends JpaRepository<Inscripcion, Long> {
    boolean existsByUsuarioAndEvento(Usuario usuario, Evento evento);
    int countByEvento(Evento evento);
}
```

### **5. Implementación de Servicios**

#### **UsuarioService**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class UsuarioService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    public Usuario registrarUsuario(Usuario usuario) {
        usuario.setFechaRegistro(LocalDateTime.now());
        return usuarioRepository.save(usuario);
    }

    public Usuario obtenerUsuarioPorId(Long id) {
        return usuarioRepository.findById(id).orElseThrow(() -> new RuntimeException("Usuario no encontrado"));
    }

    public Usuario obtenerUsuarioPorEmail(String email) {
        return usuarioRepository.findByEmail(email);
    }

    // Otros métodos según sea necesario
}
```

#### **EventoService**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class EventoService {

    @Autowired
    private EventoRepository eventoRepository;

    @Transactional
    public Evento crearEvento(Evento evento) {
        return eventoRepository.save(evento);
    }

    public List<Evento> listarEventos() {
        return eventoRepository.findAll();
    }

    public Evento obtenerEventoPorId(Long id) {
        return eventoRepository.findById(id).orElseThrow(() -> new RuntimeException("Evento no encontrado"));
    }

    // Otros métodos como editar y eliminar eventos
}
```

#### **InscripcionService**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class InscripcionService {

    @Autowired
    private InscripcionRepository inscripcionRepository;

    @Autowired
    private EventoService eventoService;

    @Autowired
    private UsuarioService usuarioService;

    @Transactional
    public void inscribirUsuarioEnEvento(Long usuarioId, Long eventoId) {
        Evento evento = eventoService.obtenerEventoPorId(eventoId);
        Usuario usuario = usuarioService.obtenerUsuarioPorId(usuarioId);

        // Verificar si ya está inscrito
        if (inscripcionRepository.existsByUsuarioAndEvento(usuario, evento)) {
            throw new RuntimeException("El usuario ya está inscrito en este evento");
        }

        // Verificar capacidad
        int inscritos = inscripcionRepository.countByEvento(evento);
        if (inscritos >= evento.getCapacidad()) {
            throw new RuntimeException("El evento ha alcanzado su capacidad máxima");
        }

        Inscripcion inscripcion = new Inscripcion();
        inscripcion.setFechaInscripcion(LocalDateTime.now());
        inscripcion.setUsuario(usuario);
        inscripcion.setEvento(evento);

        inscripcionRepository.save(inscripcion);
    }

    // Otros métodos según sea necesario
}
```

### **6. Desarrollo de Controladores**

#### **UsuarioController**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/usuarios")
public class UsuarioController {

    @Autowired
    private UsuarioService usuarioService;

    @PostMapping("/registro")
    public Usuario registrarUsuario(@RequestBody Usuario usuario) {
        return usuarioService.registrarUsuario(usuario);
    }

    // Otros endpoints como autenticación (simulada)
}
```

#### **EventoController**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/eventos")
public class EventoController {

    @Autowired
    private EventoService eventoService;

    @GetMapping
    public List<Evento> listarEventos() {
        return eventoService.listarEventos();
    }

    @PostMapping
    public Evento crearEvento(@RequestBody Evento evento) {
        return eventoService.crearEvento(evento);
    }

    @GetMapping("/{id}")
    public Evento obtenerEvento(@PathVariable Long id) {
        return eventoService.obtenerEventoPorId(id);
    }

    // Otros endpoints como editar y eliminar eventos
}
```

#### **InscripcionController**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/inscripciones")
public class InscripcionController {

    @Autowired
    private InscripcionService inscripcionService;

    @PostMapping
    public void inscribirUsuario(@RequestParam Long usuarioId, @RequestParam Long eventoId) {
        inscripcionService.inscribirUsuarioEnEvento(usuarioId, eventoId);
    }

    // Otros endpoints según sea necesario
}
```

### **7. Validaciones y Manejo de Excepciones**

- Utiliza anotaciones como `@Valid` y `@NotNull` en tus entidades y controladores para validar datos de entrada.
- Implementa un controlador global de excepciones usando `@ControllerAdvice` y `@ExceptionHandler` para manejar errores de forma consistente.

### **8. Pruebas y Documentación**

- **Pruebas:**
  - Utiliza herramientas como Postman para probar los endpoints.
  - Escribe pruebas unitarias y de integración con JUnit y Mockito (opcional).
- **Documentación:**
  - Agrega documentación a tus métodos y clases.
  - Utiliza Swagger para documentar tus API REST (opcional).

---

## **Entrega del Proyecto**

- **Código Fuente:**
  - Subir el código a un repositorio en GitHub o similar.
- **Instrucciones:**
  - Proporcionar instrucciones claras sobre cómo ejecutar la aplicación.
  - Incluir scripts SQL si es necesario (aunque `ddl-auto=update` generará las tablas).
- **Documentación:**
  - Explicar las decisiones de diseño y cómo se aplicaron los conceptos aprendidos.
- **Demostración:**
  - Preparar una breve presentación o video mostrando el funcionamiento de la aplicación (opcional).

---

## **Resultados de Aprendizaje**

Al completar este proyecto, el alumnado será capaz de:

- Configurar y conectar una aplicación Spring Boot con una base de datos MySQL.
- Definir entidades y relaciones complejas utilizando JPA.
- Crear repositorios y realizar consultas personalizadas.
- Implementar servicios con lógica de negocio y transacciones.
- Desarrollar controladores REST para exponer funcionalidades.
- Aplicar validaciones y manejar excepciones de manera efectiva.
- Comprender la importancia de las transacciones en operaciones críticas.

---

## **Consejos Adicionales**

- **Seguridad:**
  - Aunque la implementación completa de seguridad está fuera del alcance, puedes simular la autenticación o utilizar Spring Security para agregar autenticación básica.
- **Mejoras:**
  - Implementa paginación en la lista de eventos.
  - Permite que los usuarios busquen eventos por nombre o fecha.
  - Agrega la posibilidad de cancelar inscripciones.
- **Experiencia de Usuario:**
  - Si te sientes cómodo, puedes desarrollar una interfaz web sencilla utilizando Thymeleaf o un cliente frontend con Angular/React (opcional).


