# UD4: Servicios

## Índice

1. [Servicios](#servicios)
   - 1.1. [Definición de la Capa de Servicio](#definición-de-la-capa-de-servicio)
   - 1.2. [Implementación de Servicios en Spring Boot](#implementación-de-servicios-en-spring-boot)
2. [Gestión de Transacciones](#gestión-de-transacciones)
   - 2.1. [Importancia de las Transacciones](#importancia-de-las-transacciones)
   - 2.2. [Uso de @Transactional](#uso-de-transactional)
3. [Manejo de Excepciones](#manejo-de-excepciones)
   - 3.1. [ResponseStatusException](#responsestatusexception)
   - 3.2. [Excepciones Personalizadas](#excepciones-personalizadas)
   - 3.3. [Visualización de Excepciones](#visualización-de-excepciones)
4. [Caché](#caché)
   - 4.1. [Configuración de la Caché](#configuración-de-la-caché)
   - 4.2. [Uso de Anotaciones de Caché](#uso-de-anotaciones-de-caché)
5. [Patrón DTO](#patrón-dto)
   - 5.1. [Definición y Uso de DTOs](#definición-y-uso-de-dtos)
   - 5.2. [Mapeadores](#mapeadores)
6. [Validadores](#validadores)
   - 6.1. [Validación con Spring Validation](#validación-con-spring-validation)
   - 6.2. [Uso de Jakarta Bean Validation](#uso-de-jakarta-bean-validation)
7. [Buenas Prácticas en la Capa de Servicio](#buenas-prácticas-en-la-capa-de-servicio)
8. [Ejercicio Práctico: Implementación de un Servicio Completo](#ejercicio-práctico-implementación-de-un-servicio-completo)


---

## Servicios

### Definición de la Capa de Servicio

La capa de servicio en una aplicación Spring Boot es donde se implementa la lógica de negocio. Actúa como intermediario entre los controladores (que manejan las solicitudes HTTP) y los repositorios (que interactúan con la base de datos).

### Implementación de Servicios en Spring Boot

Para definir un servicio, se utiliza la anotación `@Service`. Esta indica a Spring que la clase es un componente de servicio y permite la inyección de dependencias.

**Ejemplo de un Servicio Simple:**


```java
@Service
public class UsuarioService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    public Usuario crearUsuario(Usuario usuario) {
        return usuarioRepository.save(usuario);
    }

    public Usuario obtenerUsuario(Long id) {
        return usuarioRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("Usuario no encontrado"));
    }

    public Usuario actualizarUsuario(Long id, Usuario nuevosDatos) {
        Usuario usuario = obtenerUsuario(id);
        usuario.setNombre(nuevosDatos.getNombre());
        usuario.setEmail(nuevosDatos.getEmail());
        return usuarioRepository.save(usuario);
    }

    public void eliminarUsuario(Long id) {
        usuarioRepository.deleteById(id);
    }
}
```
### Gestión de Transacciones

Las transacciones garantizan la integridad y consistencia de los datos en operaciones que involucran múltiples cambios en la base de datos.

#### Importancia de las Transacciones

- **Atomicidad:** Todas las operaciones se completan o ninguna lo hace.
- **Consistencia:** Mantiene la base de datos en un estado coherente.
- **Aislamiento:** Las transacciones concurrentes no interfieren entre sí.
- **Durabilidad:** Los cambios persisten incluso en caso de fallos.

#### Uso de `@Transactional`

En Spring, puedes gestionar transacciones mediante la anotación `@Transactional`.

```java
@Service
public class PedidoService {

    @Autowired
    private PedidoRepository pedidoRepository;

    @Autowired
    private ProductoRepository productoRepository;

    @Transactional
    public void procesarPedido(Pedido pedido) {
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

### Lanzamiento de Excepciones

### ResponseStatusException
Los errores se pueden manejar de dos formas: con excepciones. Desde la versión 5 de Spring, se puede usar [ResponseStatusException](https://www.baeldung.com/spring-response-status-exception). De esta forma, podemos lanzar una excepción y Spring se encarga de convertirla en un error HTTP en base al contenido que se le indica, dando la respuesta adecuada. 

```java
public class RaquetaValidator {

    public void validate(Raqueta raqueta) {
        // las distintas condiciones
        if (raqueta.getMarca() == null || raqueta.getMarca().isEmpty()) {
            throw new ResponseStatusException(
                    HttpStatus.BAD_REQUEST, "La marca no puede estar vacía");
        }
        if (raqueta.getModelo() == null || raqueta.getModelo().isEmpty()) {
            throw new ResponseStatusException(
                    HttpStatus.BAD_REQUEST, "El modelo no puede estar vacío");
        }
        if (raqueta.getPrecio() == null || raqueta.getPrecio() < 0) {
            throw new ResponseStatusException(
                    HttpStatus.BAD_REQUEST, "El precio no puede ser negativo");
        }
    }

}
```

```java
 @Override
public Raqueta findById(Long id) {
    log.info("findById");
    return raquetasRepository.findById(id).orElseThrow(
            () -> new ResponseStatusException(
                    HttpStatus.NOT_FOUND, "No se ha encontrado la raqueta con id: " + id)
    );
}
```

### Excepciones personalizadas
La otra opción es crear nuestras sistema de excepciones ye errores asociado al dominio. Para ello, vamos a crear una clase `RaquetaException` que herede de `RuntimeException` y que tenga un constructor con un mensaje. De esta forma, podemos lanzar una excepción y Spring se encarga de convertirla en un error HTTP en base al contenido que se le indica, dando la respuesta adecuada. El Response Stuatus lo definimos con una anotación. de esta manera nos es más sencillo testear y acotar las excepciones que se produzcan.

```java
// Nos permite devolver un estado cuando salta la excepción
@ResponseStatus(HttpStatus.NOT_FOUND)
public class TenistaNotFoundException extends RuntimeException {
    public TenistaNotFoundException(String mensaje) {
        super(mensaje);
    }
}
```

```java
public class TenistaService {

    public Tenista findById(Long id) {
        return tenistaRepository.findById(id).orElseThrow(
                () -> new TenistaNotFoundException("No se ha encontrado el tenista con id: " + id)
        );
    }
}
```

### Visualizando las excepciones
Para mostrar los errores de forma correcta debemos añadir en nuestro fichero de propiedades la siguiente configuración:

```properties
# Para que muestre el mensaje de error de excepciones
server.error.include-message=always
```

Esto hará que el mensaje de error sea visible en el cuerpo de las respuestas HTTP para excepciones no gestionadas, y se puede ver en herramientas como Postman, cURL o navegadores.

## Cache
Para usar [cache](https://www.baeldung.com/spring-cache-tutorial) en Spring Boot, debemos añadir la dependencia de Spring Cache:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```


Y añadir la anotación `@EnableCaching` en la clase principal (método main y anotada con` @SpringBootApplication`) de nuestra aplicación y en los servicios que queramos cachear añadimos la anotación `@CacheConfig` con el nombre de la cache que queramos usar. Aunque esto no es obligatorio, es recomendable usarlo para no tener que repetir el nombre de la cache en cada método. 
```java
@CacheConfig(cacheNames = {"raquetas"})
```

- @Cacheable: Se usa en metodos de consulta. Se usa para indicar que un método es cachable. Si el método ya ha sido ejecutado, se devuelve el resultado de la cache. Si no, se ejecuta el método y se guarda el resultado en la cache. Se le puede indicar el nombre de la cache, el key y el tiempo de expiración. Se recomienda usar el key como identificador
- @CachePut: Se usa en métodos para guardar o modificar. Se usa para indicar que un método es cachable. Si el método ya ha sido ejecutado, se ejecuta de nuevo y se guarda el resultado en la cache. Se le puede indicar el nombre de la cache, el key y el tiempo de expiración. Se recomienda usar result.key para la cache
- @CacheEvict: Se usa en métodos de borrado. Se usa para indicar que un método es cachable. Si el método ya ha sido ejecutado, se elimina el resultado de la cache. Se le puede indicar el nombre de la cache, el key y el tiempo de expiración. Se recomienda usar el Key.

```java
@CacheConfig(cacheNames = {"raquetas"})
public RaquetasCacheado() {

    // ....

    public getRaquetas() {
        return raquetasRepository.findAll();
    }

    // Cachea con el id como key
    @Cacheable(key = "#id")
    public Raqueta findById(Long id) {
        //...
    }

    // Cachea con el id del resultado de la operación como key
   @CachePut(key = "#raqueta.id") 
    public Raqueta save(Raqueta raqueta) {
        //...
    }

    // El key es opcional, si no se indica
    @CacheEvict(key = "#id")
    public void deleteById(Long id) {
        //...
    }

}
```

## Patrón DTO
Los [dto](https://www.oscarblancarteblog.com/2018/11/30/data-transfer-object-dto-patron-diseno/) son objetos que se usan para transportar datos entre capas. Se usan para evitar que se expongan las entidades de la base de datos o los modelos de nuestra aplicación, asi como se usan para ensamblar distintos objetos, eliminar campos que no queremos que se vean o pasar de un tipo de dato a otro. 

![](./images/dto.svg)

```java
public class RaquetaResponseDto {
    private final Long id;
    private final UUID uuid;
    private final String marca;
    private final String modelo;
    private final Double precio;
    private final String imagen;
}

```

## Mapeadores
Los mapeadores son clases que se encargan de convertir de un tipo de objeto a otro. En este caso, de un dto a un modelo de nuestra aplicación y viceversa. Podemos usar librerías para mapear como [ModelMapper](https://modelmapper.org/), o hacerlo nosotros mismos.

![](./images/mapeador.jpg)

```java
public class RaquetaMapper {

    // Mapeamos de modelo a DTO
    public RaquetaResponseDto toResponse(Raqueta raqueta) {
        return new RaquetaResponseDto(
                raqueta.getId(),
                raqueta.getUuid(),
                raqueta.getMarca(),
                raqueta.getModelo(),
                raqueta.getPrecio(),
                raqueta.getImagen()
        );
    }

    public List<RaquetaResponseDto> toResponseList(List<Raqueta> raquetas) {
        return raquetas.stream()
                .map(this::toResponse) //LLamamos para cada elemento a la funcion toResponse
                .toList();
    }

    // Mapeamos de DTO a modelo
    public Raqueta toModel(RaquetaRequestDto dto) {
        return new Raqueta(
                dto.getMarca(),
                dto.getModelo(),
                dto.getPrecio(),
                dto.getImagen()
        );
    }

    public List<Raqueta> toModelList(List<RaquetaRequestDto> dtos) {
        return dtos.stream()
                .map(this::toModel) //LLamamos para cada elemento a la funcion toModel
                .toList();
    }
}
```

## Validadores
Los [validadores](https://www.baeldung.com/spring-boot-bean-validation) son clases que se encargan de validar los datos que nos llegan y lanzar excepciones en caso de que no sean correctos. 

Podemos crear una clase que se encargue de validar los datos de una raqueta:

```java
public class RaquetaValidator {

    public void validate(Raqueta raqueta) {
        if (raqueta.getMarca() == null || raqueta.getMarca().isBlank()) {
            throw new InvalidRaquetaException("La marca no puede ser nula o estar en blanco");
        }
        if (raqueta.getModelo() == null || raqueta.getModelo().isBlank()) {
            throw new InvalidRaquetaException("El modelo no puede ser nulo o estar en blanco");
        }
        if (raqueta.getPrecio() == null || raqueta.getPrecio() < 0) {
            throw new InvalidRaquetaException("El precio no puede ser nulo o negativo");
        }
    }
}
```

O podemos usar el sistema de validación de Spring, que nos permite validar los datos de una forma más sencilla. Para ello, debemos añadir la dependencia de Spring Validation:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

De esta manera podemos usar las anotaciones de validación de Spring:

```java
public class TenistaRequestDto {
    @NotBlank(message = "El nombre no puede estar vacío")
    private String nombre;
    @Min(value = 0, message = "El ranking no puede ser negativo")
    private Integer ranking;
    @NotBlank(message = "El país no puede estar vacío")
    private String pais;
    private String imagen;
    @Min(value = 0, message = "El id de la raqueta no puede ser negativo")
    private Long raquetaId; // Id de la raqueta, puede ser null
}
```

Ahora si queremos validar los datos de un dto, podemos usar `@Valid` en el parámetro del método:

```java
@PostMapping("")
public ResponseEntity<TenistaResponseDto> postTenista(
        @Valid @RequestBody TenistaRequestDto tenista
) {
    return ResponseEntity.created(null).body(
            tenistaMapper.toResponse(
                    tenistasService.save(tenistaMapper.toModel(tenista)))
    );
}
```

No debes olvidar añadir un [handler](https://www.baeldung.com/spring-boot-bean-validation#the-exceptionhandler-annotation) anotado como @ExceptionHandler y el código de error para el error @ResponseStatus(HttpStatus.BAD_REQUEST) para capturar estas excepciones en tu controlador:

```java
 // Para capturar los errores de validación
@RestControllerAdvice
public class ValidacionExceptionHandler {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Map<String, String> handleValidationExceptions(
            MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach((error) -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        return errors;
    }
}
```

### Jakarta Bean Validation
Son los [validadores](https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#validator-defineconstraints-spec) que podemos usar en Spring. Aquí tienes los más importantes.

| Restricción | Descripción |
| --- | --- |
| @Null | El elemento debe ser nulo. |
| @NotNull | El elemento no debe ser nulo. |
| @AssertTrue | El elemento debe ser verdadero. |
| @AssertFalse | El elemento debe ser falso. |
| @Min | El elemento debe ser un número cuyo valor sea mayor o igual al mínimo especificado. |
| @Max | El elemento debe ser un número cuyo valor sea menor o igual al máximo especificado. |
| @DecimalMin | El elemento debe ser un número cuyo valor sea mayor o igual al mínimo especificado. |
| @DecimalMax | El elemento debe ser un número cuyo valor sea menor o igual al máximo especificado. |
| @Negative | El elemento debe ser un número estrictamente negativo (es decir, se considera que 0 es un valor inválido). |
| @NegativeOrZero | El elemento debe ser un número negativo o cero. |
| @Positive | El elemento debe ser un número estrictamente positivo (es decir, se considera que 0 es un valor inválido). |
| @PositiveOrZero | El elemento debe ser un número positivo o cero. |
| @Size | El tamaño del elemento debe estar entre los límites especificados (incluidos). |
| @Digits | El elemento debe ser un número dentro del rango aceptado. |
| @Past | El elemento debe ser una fecha, hora o instante en el pasado. |
| @PastOrPresent | El elemento debe ser una fecha, hora o instante en el pasado o en el presente. |
| @Future | El elemento debe ser una fecha, hora o instante en el futuro. |
| @FutureOrPresent | El elemento debe ser una fecha, hora o instante en el presente o en el futuro. |
| @Pattern | El elemento anotado debe coincidir con la expresión regular especificada. |
| @NotEmpty | El elemento anotado no debe ser nulo ni vacío. |
| @NotBlank | El elemento anotado no debe ser nulo y debe contener al menos un carácter que no sea un espacio en blanco. |
| @Email | La cadena debe ser una dirección de correo electrónico bien formada. |

### Buenas Prácticas en la Capa de Servicio

- **Separación de Responsabilidades:** Mantén la lógica de negocio en los servicios, no en los controladores o repositorios.
- **Inyección de Dependencias:** Utiliza constructor o campos con `@Autowired` para inyectar repositorios u otros servicios.
- **Gestión de Excepciones:** Maneja las excepciones y proporciona mensajes claros.
- **Transaccionalidad:** Usa `@Transactional` cuando sea necesario para asegurar la consistencia de los datos.

---
## Ejercicio Práctico: Implementación de un Servicio Completo

### **Descripción**

Desarrolla un sistema de gestión de "Productos" que implemente todas las características explicadas en este tema. Los productos tendrán los siguientes atributos:

- **id** (Long): Identificador único.
- **nombre** (String): No puede estar vacío.
- **precio** (Double): Debe ser positivo.
- **stock** (Integer): Debe ser mayor o igual a 0.

---

### **Objetivos del Ejercicio**

1. **Caché**:
   - Implementa caché para optimizar las consultas de productos por ID.
2. **Validación**:
   - Valida los datos del producto al momento de crearlo o actualizarlo.
3. **Excepciones**:
   - Usa `ResponseStatusException` para manejar errores comunes.
   - Crea excepciones personalizadas para casos específicos.
4. **DTOs y Mapeadores**:
   - Utiliza un DTO para transferir datos entre capas.
   - Implementa un mapeador para convertir entre entidades y DTOs.
5. **Pruebas**:
   - Verifica las funcionalidades utilizando herramientas como Postman.

---

### **Pruebas con Postman**

1. **Crear Producto** (`POST /api/productos`):
   - Envía datos válidos e inválidos para probar la validación y el manejo de errores.
2. **Consultar Producto** (`GET /api/productos/{id}`):
   - Verifica el uso de caché al realizar múltiples consultas del mismo producto.
3. **Eliminar Producto** (`DELETE /api/productos/{id}`):
   - Verifica que los datos se eliminan correctamente de la base de datos y de la caché.
