# UD5: Documentación de APIs con Swagger en Spring Boot

## Índice

- [¿Qué es Swagger?](#qué-es-swagger)
- [Ventajas de Utilizar Swagger](#ventajas-de-utilizar-swagger)
- [Integración de Swagger en un Proyecto Spring Boot](#integración-de-swagger-en-un-proyecto-spring-boot)
  - [Dependencias Necesarias](#dependencias-necesarias)
  - [Configuración Básica](#configuración-básica)
  - [Accediendo a Swagger UI](#accediendo-a-swagger-ui)
- [Personalización de la Documentación](#personalización-de-la-documentación)
  - [Anotaciones Comunes](#anotaciones-comunes)
  - [Ejemplo de Uso](#ejemplo-de-uso)
- [Swagger UI](#swagger-ui)
  - [Características](#características)
- [Buenas Prácticas](#buenas-prácticas)
- [Ejercicios Propuestos](#ejercicios-propuestos)
  - [Ejercicio 1: Integrar Swagger en un Proyecto Existente](#ejercicio-1-integrar-swagger-en-un-proyecto-existente)
  - [Ejercicio 2: Personalizar la Documentación](#ejercicio-2-personalizar-la-documentación)
  - [Ejercicio 3: Probar Endpoints con Swagger UI](#ejercicio-3-probar-endpoints-con-swagger-ui)
- [Referencias](#referencias)

---
## ¿Qué es Swagger?

Swagger es una especificación y un conjunto de herramientas para documentar APIs RESTful. Permite describir la estructura de las APIs para que las máquinas puedan leerlas. Con Swagger, puedes generar una interfaz de usuario que permite a los usuarios interactuar con tu API sin necesidad de escribir código adicional.

## Ventajas de Utilizar Swagger

- **Documentación Automática**: Genera documentación actualizada automáticamente a medida que desarrollas tu API.
- **Interfaz Interactiva**: Proporciona una interfaz web donde los usuarios pueden probar los endpoints.
- **Estandarización**: Facilita la adopción de estándares en la definición de APIs.
- **Comunicación**: Mejora la comunicación entre equipos de desarrollo y otros stakeholders.

## Integración de Swagger en un Proyecto Spring Boot

### Dependencias Necesarias

Para integrar Swagger en tu proyecto Spring Boot, añade las siguientes dependencias en tu archivo `pom.xml`:

    ```xml
    <dependencies>
        <!-- Otras dependencias -->

        <!-- Springdoc OpenAPI UI -->
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-ui</artifactId>
            <version>1.6.14</version>
        </dependency>
    </dependencies>
    ```

### Configuración Básica

En la mayoría de los casos, no es necesaria una configuración adicional. Springdoc OpenAPI escaneará automáticamente tus controladores y generará la documentación.

### Accediendo a Swagger UI

Una vez configurado, puedes acceder a la interfaz de Swagger UI en:
http://localhost:8080/swagger-ui.html


## Personalización de la Documentación

Puedes añadir información adicional a tu documentación usando anotaciones.

### Anotaciones Comunes

- `@Tag`: Anota la clase del controlador para agrupar endpoints.
- `@Operation`: Describe un método específico en el controlador.
- `@Parameter`: Describe los parámetros de entrada.
- `@ApiResponses`: Describe las posibles respuestas.

### Ejemplo de Uso

    ```java
    import io.swagger.v3.oas.annotations.*;
    import io.swagger.v3.oas.annotations.responses.*;
    import io.swagger.v3.oas.annotations.parameters.*;
    import org.springframework.web.bind.annotation.*;

    @RestController
    @RequestMapping("/usuarios")
    @Tag(name = "Usuarios", description = "Gestión de Usuarios")
    public class UsuarioController {

        @Operation(summary = "Obtener lista de usuarios", description = "Devuelve una lista de usuarios")
        @GetMapping
        public List<Usuario> obtenerUsuarios() {
            // Lógica para obtener usuarios
            return new ArrayList<>();
        }

        @Operation(summary = "Crear un nuevo usuario", description = "Crea un nuevo usuario en el sistema")
        @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Usuario creado exitosamente"),
            @ApiResponse(responseCode = "400", description = "Solicitud inválida")
        })
        @PostMapping
        public ResponseEntity<Usuario> crearUsuario(
                @Parameter(description = "Información del nuevo usuario", required = true)
                @RequestBody Usuario usuario) {
            // Lógica para crear usuario
            return ResponseEntity.status(HttpStatus.CREATED).body(usuario);
        }
    }
    ```

## Swagger UI

Swagger UI es una aplicación web que muestra la documentación generada de manera interactiva. Permite probar los endpoints directamente desde el navegador.

### Características

- Visualización de todos los endpoints disponibles.
- Descripción detallada de cada endpoint y sus parámetros.
- Capacidad para enviar solicitudes y ver las respuestas.

## Buenas Prácticas

- **Mantener Actualizada la Documentación**: Asegúrate de que la documentación refleja los cambios en la API.
- **Uso de Anotaciones**: Proporciona descripciones claras utilizando las anotaciones de Swagger.
- **Seguridad**: Configura Swagger para que no exponga información sensible en entornos de producción.

## Ejercicios Propuestos

### Ejercicio 1: Integrar Swagger en un Proyecto Existente

**Tareas:**

1. Añade las dependencias de Swagger a tus proyectos Spring Boot existentes.
2. Verifica que Swagger UI muestra correctamente tus endpoints.

### Ejercicio 2: Personalizar la Documentación

**Tareas:**

1. Utiliza las anotaciones de Swagger para añadir descripciones a tus controladores y métodos.
2. Añade información sobre los parámetros y las respuestas esperadas.
3. Observa cómo estos cambios se reflejan en Swagger UI.

### Ejercicio 3: Probar Endpoints con Swagger UI

**Tareas:**

1. Utiliza Swagger UI para enviar solicitudes a tu API.
2. Observa las respuestas y verifica que son las esperadas.
3. Experimenta con diferentes parámetros y analiza los resultados.


## Referencias

- [Documentación Oficial de Swagger](https://swagger.io/)
- [Springdoc OpenAPI para Spring Boot](https://springdoc.org/)

