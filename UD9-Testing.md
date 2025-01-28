- [Iniciación al testing en Spring Boot](#iniciación-al-testing-en-spring-boot)
  - [Testing](#testing)
  - [Test unitarios](#test-unitarios)
  - [Test de integración](#test-de-integración)
  - [Testeando los controladores](#testeando-los-controladores)
- [Prácticas de Testing](#Prácticas-de-Testing-en-Spring-Boot)

![](./imagenes/UD9/banner06.png)

# Iniciación al testing en Spring Boot

## Testing
El testing es una parte fundamental en el desarrollo de software. Nos permite asegurar que nuestro código funciona correctamente y que no se rompe cuando hacemos cambios en él. Para ello debe es importante que diseñemos nuestros test de forma que sean fáciles de mantener y que nos permitan detectar errores de forma rápida y cubran todos los aspectos de nuestro código.

Tenemos varios niveles de tests:
- Test unitarios: estos tests se encargan de probar una unidad de código (una clase, un método, etc.) de forma aislada. Para ello se suelen utilizar mocks para aislar la unidad de código que estamos probando de las dependencias que tiene.
- Test de integración: estos tests se encargan de probar que las distintas unidades de código funcionan correctamente cuando se integran entre ellas. Para ello se suelen utilizar bases de datos en memoria para simular el acceso a datos.
- Test End-to-End: estos tests se encargan de probar que todo el sistema funciona correctamente. Para ello se suelen utilizar herramientas que simulan un navegador web y que permiten simular las acciones que haría un usuario en la aplicación. Por ejemplo cuando usamos Postman.
  
## Test unitarios
Para realizar los test unitarios con Spring Boot podemos utilizar [JUnit 5](https://www.baeldung.com/junit-5) y [Mockito](https://www.baeldung.com/mockito-series). Para ello debemos añadir la dependencia de Starter de Test en nuestro proyecto:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

Agregar Mockito:
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.5.0</version> <!-- Sustituye por la versión más reciente -->
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

Para realizar los test unitarios en clases que no dependan de otras, no necesitamos realizar mocks. Podremos usar JUnit 5 para realizar los test. Por ejemplo de un repositorio o mapeador o servicio sin dependencias.

Es importante que los test sean independientes, es decir, que no dependan unos de otros. Para ello debemos asegurarnos que cada test se encarga de inicializar los datos que necesita para funcionar. Para ello podemos utilizar los métodos `@BeforeEach` y `@AfterEach` que se ejecutan antes y después de cada test. También podemos utilizar los métodos `@BeforeAll` y `@AfterAll` que se ejecutan antes y después de todos los test de la clase.

Además, usaremos las aserciones de JUnit 5 para comprobar que el resultado de la ejecución de nuestro código es el esperado. Por ejemplo:

Clase a testear:
```java
public class ConversorTemperatura {
    public double convertirCelsiusAFahrenheit(double celsius) {
        return (celsius * 9 / 5) + 32;
    }

    public double convertirFahrenheitACelsius(double fahrenheit) {
        return (fahrenheit - 32) * 5 / 9;
    }
}
```

Test unitario:
```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class ConversorTemperaturaTest {
    private final ConversorTemperatura conversor = new ConversorTemperatura();

    @Test
    void testConvertirCelsiusAFahrenheit() {
        double resultado = conversor.convertirCelsiusAFahrenheit(0);
        assertEquals(32, resultado, "0°C debería ser 32°F");
    }

    @Test
    void testConvertirFahrenheitACelsius() {
        double resultado = conversor.convertirFahrenheitACelsius(32);
        assertEquals(0, resultado, "32°F debería ser 0°C");
    }
}

```

Si estamos testeando controladores o servicios, es posible que necesitemos realizar mocks de las dependencias que tienen. Para ello podemos utilizar Mockito.

Es por ello que debemos extender nuestra clase de test con `@ExtendWith(MockitoExtension.class)` y utilizar la anotación `@Mock` para indicar que queremos que se cree un mock de la dependencia. Además, debemos indicarle a Mockito que inyecte los mocks en la clase que estamos testeando con la anotación `@InjectMocks`.

De esta manera cada vez que se use un método de la clase mockeada, se ejecutará el código que hemos definido en el mock. De esta manera nos concentraremos en probar el código de la clase que estamos testeando y no el de las dependencias.

Para simular el comportamiento de los mocks, podemos utilizar el método `when` de Mockito. Para verificar que se ha llamado a un método de un mock, podemos utilizar el método `verify` de Mockito.

Servicio a probar:
```java
@Service
public class ProductoService {

    private final ProductoRepository productoRepository;

    public ProductoService(ProductoRepository productoRepository) {
        this.productoRepository = productoRepository;
    }

    public Producto guardarProducto(Producto producto) {
        return productoRepository.save(producto);
    }

    public Optional<Producto> buscarProductoPorNombre(String nombre) {
        return productoRepository.findByNombre(nombre);
    }

    public void eliminarProducto(Long id) {
        productoRepository.deleteById(id);
    }
}
```
Test con Mockito:
```java
@ExtendWith(MockitoExtension.class)
class ProductoServiceTest {

    @Mock
    private ProductoRepository productoRepository;

    @InjectMocks
    private ProductoService productoService;

    @Test
    void testGuardarProducto() {
        Producto producto = new Producto();
        producto.setNombre("Manzana");
        producto.setPrecio(1.50);

        when(productoRepository.save(any(Producto.class))).thenReturn(producto);

        Producto guardado = productoService.guardarProducto(producto);

        assertNotNull(guardado);
        assertEquals("Manzana", guardado.getNombre());
        verify(productoRepository).save(producto); // Verificamos que se llamó al repositorio
    }

    @Test
    void testBuscarProductoPorNombre() {
        Producto producto = new Producto();
        producto.setNombre("Manzana");
        producto.setPrecio(1.50);

        when(productoRepository.findByNombre("Manzana")).thenReturn(Optional.of(producto));

        Optional<Producto> encontrado = productoService.buscarProductoPorNombre("Manzana");

        assertTrue(encontrado.isPresent());
        assertEquals("Manzana", encontrado.get().getNombre());
        verify(productoRepository).findByNombre("Manzana"); // Verificamos que se llamó al método
    }

    @Test
    void testEliminarProducto() {
        Long productoId = 1L;

        doNothing().when(productoRepository).deleteById(productoId);

        productoService.eliminarProducto(productoId);

        verify(productoRepository).deleteById(productoId); // Verificamos que se llamó al repositorio
    }
}
```

## Test de integración
Para hacer los test de integration podemos usar solo JUnit con las clases con sus respectivas dependencias reales. Sin embargo, si queremos hacer los test de integración usando el contexto Spring Boot, debemos usar la anotación `@SpringBootTest` en la clase de test. De esta manera Spring Boot se encargará de inicializar el contexto de la aplicación y de inyectar las dependencias que necesitemos con `@Autowired`.

Entidad:
```java
@Entity
public class Usuario {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String nombre;
    private String email;
}
```
Repositorio:
```java
public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    Optional<Usuario> findByEmail(String email);
}
```
Test de integración:
```java
@SpringBootTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY) // Usar H2
class UsuarioRepositoryTest {
    @Autowired
    private UsuarioRepository usuarioRepository;

    @Test
    void testGuardarYBuscarUsuario() {
        Usuario usuario = new Usuario();
        usuario.setNombre("María");
        usuario.setEmail("maria@example.com");

        Usuario guardado = usuarioRepository.save(usuario);
        Optional<Usuario> encontrado = usuarioRepository.findByEmail("maria@example.com");

        assertTrue(encontrado.isPresent());
        assertEquals("María", encontrado.get().getNombre());
    }
}
```

## Testeando los controladores
Aunque podemos testear los controladores como una clase más, mockeando o integrando, tal y como hemos visto en los apartados anteriores, Spring Boot nos proporciona una serie de herramientas para testear los controladores de una manera más sencilla y se simula a lo que hemos hecho con Postman.

Para ello debemos usar la anotación `@AutoConfigureMockMvc` en la clase de test. De esta manera Spring Boot se encargará de inicializar el contexto de la aplicación y de inyectar las dependencias que necesitemos. Además, nos proporciona un objeto `MockMvc` que nos permite [simular las peticiones HTTP](https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework.html) y con ello testear la [capa HTTP](https://spring.io/guides/gs/testing-web/) o realizar un test de [integración completo ](https://www.baeldung.com/integration-testing-in-spring)si no usamos los mocks . Usaremos ObjectMapper para mapear los objetos a JSON y poder testear los controladores.

Controlador:
```java
@RestController
@RequestMapping("/api/usuarios")
public class UsuarioController {
    @Autowired
    private UsuarioRepository usuarioRepository;

    @PostMapping
    public Usuario create(@RequestBody Usuario usuario) {
        return usuarioRepository.save(usuario);
    }

    @GetMapping("/{id}")
    public Usuario findById(@PathVariable Long id) {
        return usuarioRepository.findById(id)
                .orElseThrow(() -> new ResponseStatusException(HttpStatus.NOT_FOUND, "Usuario no encontrado"));
    }
}
```
Test del controlador:
```java
@SpringBootTest
@AutoConfigureMockMvc
class UsuarioControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    void testCrearUsuario() throws Exception {
        String nuevoUsuario = "{ \"nombre\": \"Juan\", \"email\": \"juan@example.com\" }";

        mockMvc.perform(post("/api/usuarios")
                .contentType(MediaType.APPLICATION_JSON)
                .content(nuevoUsuario))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.nombre").value("Juan"))
                .andExpect(jsonPath("$.email").value("juan@example.com"));
    }

    @Test
    void testUsuarioNoEncontrado() throws Exception {
        mockMvc.perform(get("/api/usuarios/999"))
                .andExpect(status().isNotFound());
    }
}
```

De la misma manera que en apartado anterior, podemos mockear las dependencias de los controladores para que no se conecten a la base de datos o servicios y ser solo unitario. Para ello debemos usar la anotación `@MockBean` en la clase de test. De esta manera Spring Boot se encargará de inicializar el contexto de la aplicación y de inyectar las dependencias que necesitemos. 


Servicio:
```java
@Service
public class PedidoService {

    public Pedido calcularTotal(Pedido pedido) {
        pedido.setTotal(pedido.getCantidad() * 10.0); // Supongamos un precio fijo por unidad
        return pedido;
    }
}
```
Controlador:
```java
@RestController
@RequestMapping("/api/pedidos")
public class PedidoController {

    @Autowired
    private PedidoService pedidoService;

    @PostMapping
    public Pedido crearPedido(@RequestBody Pedido pedido) {
        return pedidoService.calcularTotal(pedido);
    }
}
```
Test del controlador con MockBean:
```java
@SpringBootTest
@AutoConfigureMockMvc
public class PedidoControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PedidoService pedidoService;

    @Test
    void testCrearPedido() throws Exception {
        // Datos de entrada
        Pedido pedido = new Pedido();
        pedido.setProducto("Libro");
        pedido.setCantidad(2);

        // Datos simulados de salida
        Pedido pedidoConTotal = new Pedido();
        pedidoConTotal.setProducto("Libro");
        pedidoConTotal.setCantidad(2);
        pedidoConTotal.setTotal(20.0);

        // Simulamos el comportamiento del servicio
        when(pedidoService.calcularTotal(any(Pedido.class))).thenReturn(pedidoConTotal);

        // Realizamos la petición POST
        mockMvc.perform(post("/api/pedidos")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"producto\": \"Libro\", \"cantidad\": 2}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.producto").value("Libro"))
                .andExpect(jsonPath("$.cantidad").value(2))
                .andExpect(jsonPath("$.total").value(20.0));

        // Verificamos que se llamó al servicio
        verify(pedidoService).calcularTotal(any(Pedido.class));
    }
}
```
**Explicación:**
1. `@MockBean`: Simula el comportamiento de la dependencia `PedidoService` dentro del controlador.
2. `MockMvc`: Nos permite realizar peticiones HTTP simuladas y comprobar las respuestas.
3. `jsonPath`: Verifica que el JSON de respuesta contiene los valores esperados.
4. **Ventaja**: Este enfoque permite probar exclusivamente la lógica HTTP del controlador, aislándolo del comportamiento de otros componentes.

---

# Prácticas de Testing en Spring Boot

## **Práctica 1: Pruebas unitarias con JUnit 5**
**Objetivo:** Crear y ejecutar pruebas unitarias para métodos de servicio sin dependencias externas.

### **Descripción:**
Se les proporciona una clase `CalculadoraService` con métodos básicos (suma, resta, multiplicación, división). Deben escribir pruebas unitarias para validar el comportamiento esperado de cada método.

### **Pasos:**
1. Crear una clase `CalculadoraService` con los métodos:
   ```java
   public class CalculadoraService {
       public int sumar(int a, int b) {
           return a + b;
       }

       public int restar(int a, int b) {
           return a - b;
       }

       public int multiplicar(int a, int b) {
           return a * b;
       }

       public int dividir(int a, int b) {
           if (b == 0) throw new IllegalArgumentException("División por cero no permitida");
           return a / b;
       }
   }
   ```

2. Crear una clase de prueba `CalculadoraServiceTest` y escribir casos de prueba para:
   - Validar que los métodos devuelven los resultados correctos.
   - Comprobar que lanzar una excepción al dividir por cero.

   Ejemplo de prueba:
   ```java
   @Test
   void testDividirPorCero() {
       var exception = assertThrows(IllegalArgumentException.class, () -> {
           service.dividir(10, 0);
       });
       assertEquals("División por cero no permitida", exception.getMessage());
   }
   ```

3. Ejecutar las pruebas con `mvn test` o desde el IDE.

---

## Práctica 2: Pruebas unitarias con Mockito

**Objetivo**: Aprender a utilizar Mockito para escribir pruebas unitarias, simulando dependencias y verificando interacciones entre un servicio y un repositorio.

---

### Descripción:
Se proporciona una entidad `Cliente`, un repositorio `ClienteRepository` y un servicio `ClienteService`. Debes usar Mockito para:
1. Simular el comportamiento del repositorio.
2. Probar los métodos del servicio.
3. Verificar que las interacciones entre el servicio y el repositorio se realizan correctamente.

---

### Entidad: Cliente
```java
@Entity
public class Cliente {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;
    private String email;

    // Getters y setters
}
```

---

### Repositorio: ClienteRepository
```java
public interface ClienteRepository extends JpaRepository<Cliente, Long> {
    Optional<Cliente> findByEmail(String email);
}
```

---

### Servicio: ClienteService
```java
@Service
public class ClienteService {

    private final ClienteRepository clienteRepository;

    public ClienteService(ClienteRepository clienteRepository) {
        this.clienteRepository = clienteRepository;
    }

    public Cliente guardarCliente(Cliente cliente) {
        return clienteRepository.save(cliente);
    }

    public Optional<Cliente> buscarClientePorEmail(String email) {
        return clienteRepository.findByEmail(email);
    }

    public void eliminarCliente(Long id) {
        clienteRepository.deleteById(id);
    }
}
```

---

### Tarea:
Escribir pruebas unitarias para `ClienteService` utilizando **Mockito**.

1. **Configurar las pruebas**:
   - Usa las anotaciones `@ExtendWith(MockitoExtension.class)` y `@Mock` para inyectar un mock de `ClienteRepository`.
   - Usa `@InjectMocks` para crear una instancia de `ClienteService`.

2. **Escribir pruebas para los métodos del servicio**:
   - **Guardar un cliente**:
     - Simular que el método `save` del repositorio guarda un cliente y devolver un cliente ficticio.
     - Verificar que el repositorio fue llamado con el cliente correcto.
   - **Buscar un cliente por email**:
     - Simular que el método `findByEmail` del repositorio devuelve un cliente ficticio.
     - Verificar que el resultado del servicio coincide con el cliente esperado.
   - **Eliminar un cliente**:
     - Simular la eliminación de un cliente por su ID.
     - Verificar que el método `deleteById` fue llamado con el ID correcto.

---

### Ejemplo de clase de prueba con Mockito:
```java
@ExtendWith(MockitoExtension.class)
class ClienteServiceTest {

    @Mock
    private ClienteRepository clienteRepository;

    @InjectMocks
    private ClienteService clienteService;

    @Test
    void testGuardarCliente() {
        Cliente cliente = new Cliente();
        cliente.setNombre("Juan");
        cliente.setEmail("juan@example.com");

        when(clienteRepository.save(any(Cliente.class))).thenReturn(cliente);

        Cliente guardado = clienteService.guardarCliente(cliente);

        assertNotNull(guardado);
        assertEquals("Juan", guardado.getNombre());
        verify(clienteRepository).save(cliente); // Verificar interacción con el mock
    }

    @Test
    void testBuscarClientePorEmail() {
        Cliente cliente = new Cliente();
        cliente.setNombre("Ana");
        cliente.setEmail("ana@example.com");

        when(clienteRepository.findByEmail("ana@example.com")).thenReturn(Optional.of(cliente));

        Optional<Cliente> encontrado = clienteService.buscarClientePorEmail("ana@example.com");

        assertTrue(encontrado.isPresent());
        assertEquals("Ana", encontrado.get().getNombre());
        verify(clienteRepository).findByEmail("ana@example.com");
    }

    @Test
    void testEliminarCliente() {
        Long clienteId = 1L;

        doNothing().when(clienteRepository).deleteById(clienteId);

        clienteService.eliminarCliente(clienteId);

        verify(clienteRepository).deleteById(clienteId); // Verificar que se llamó al método
    }
}
```

### Entregables:
1. Clase de pruebas completa para `ClienteService` usando Mockito.

---

## **Práctica 3: Pruebas de integración con H2**
**Objetivo:** Probar la integración de un repositorio con una base de datos en memoria H2.

### **Descripción:**
Se les proporciona una entidad `Producto` y un repositorio `ProductoRepository`. Deben escribir pruebas de integración para verificar que las operaciones básicas (guardar, buscar, borrar) funcionan correctamente.

### **Pasos:**
1. Crear la entidad `Producto`:
   ```java
   @Entity
   public class Producto {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;

       private String nombre;
       private double precio;

       // Getters y setters
   }
   ```

2. Crear el repositorio:
   ```java
   public interface ProductoRepository extends JpaRepository<Producto, Long> {}
   ```

3. Crear una clase de prueba de integración:
   ```java
   @SpringBootTest
   @AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY) // Usar H2
   class ProductoRepositoryTest {

       @Autowired
       private ProductoRepository productoRepository;

       @Test
       void testGuardarYBuscarProducto() {
           Producto producto = new Producto();
           producto.setNombre("Producto 1");
           producto.setPrecio(9.99);

           Producto guardado = productoRepository.save(producto);

           assertNotNull(guardado.getId());
           assertEquals("Producto 1", guardado.getNombre());
       }
   }
   ```

4. Ampliar los casos de prueba para cubrir:
   - Borrar productos.
   - Buscar productos por ID.

---

## **Práctica 4: Test de controladores con MockMvc**
**Objetivo:** Probar los endpoints de un controlador usando MockMvc.

### **Descripción:**
Se les proporciona un controlador `ProductoController` que expone endpoints CRUD para la entidad `Producto`. Deben escribir pruebas para:
- Probar respuestas exitosas (200 OK).
- Validar errores (404 Not Found, 400 Bad Request).
- Simular peticiones con datos JSON.

### **Pasos:**
1. Crear el controlador `ProductoController`:
   ```java
   @RestController
   @RequestMapping("/api/productos")
   public class ProductoController {

       @Autowired
       private ProductoRepository productoRepository;

       @GetMapping
       public List<Producto> findAll() {
           return productoRepository.findAll();
       }

       @GetMapping("/{id}")
       public Producto findById(@PathVariable Long id) {
           return productoRepository.findById(id)
                   .orElseThrow(() -> new ResponseStatusException(HttpStatus.NOT_FOUND, "Producto no encontrado"));
       }

       @PostMapping
       public Producto create(@RequestBody Producto producto) {
           return productoRepository.save(producto);
       }
   }
   ```

2. Crear la clase de prueba `ProductoControllerTest`:
   ```java
   @SpringBootTest
   @AutoConfigureMockMvc
   class ProductoControllerTest {

       @Autowired
       private MockMvc mockMvc;

       @Test
       void testFindAll() throws Exception {
           mockMvc.perform(get("/api/productos"))
                   .andExpect(status().isOk())
                   .andExpect(content().contentType(MediaType.APPLICATION_JSON));
       }

       @Test
       void testFindByIdNotFound() throws Exception {
           mockMvc.perform(get("/api/productos/999"))
                   .andExpect(status().isNotFound());
       }

       @Test
       void testCreateProducto() throws Exception {
           String nuevoProducto = "{ "nombre": "Producto 2", "precio": 19.99 }";

           mockMvc.perform(post("/api/productos")
                   .contentType(MediaType.APPLICATION_JSON)
                   .content(nuevoProducto))
                   .andExpect(status().isOk())
                   .andExpect(jsonPath("$.nombre").value("Producto 2"))
                   .andExpect(jsonPath("$.precio").value(19.99));
       }
   }
   ```

3. Ejecutar los tests y verificar el comportamiento del controlador.
