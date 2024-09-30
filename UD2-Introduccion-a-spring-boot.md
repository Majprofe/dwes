# U2: Introducción a Spring Boot

## Índice

1. [¿Qué es Spring Boot?](#qué-es-spring-boot)
2. [Ventajas de Usar Spring Boot](#ventajas-de-usar-spring-boot)
3. [Creación de un Proyecto Spring Boot](#creación-de-un-proyecto-spring-boot)
   - [3.1 Usando Spring Initializr](#31-usando-spring-initializr)
   - [3.2 Estructura de un Proyecto Spring Boot](#32-estructura-de-un-proyecto-spring-boot)
4. [Anatomía de una Aplicación Spring Boot](#anatomía-de-una-aplicación-spring-boot)
   - [4.1 Ficheros Importantes en un Proyecto Spring Boot](#41-ficheros-importantes-en-un-proyecto-spring-boot)
   - [4.2 Clase Principal](#42-clase-principal)
   - [4.3 Archivo `application.properties`](#43-archivo-applicationproperties)
5. [Ejecutando la Aplicación](#ejecutando-la-aplicación)
6. [Ejercicios](#ejercicios)

---

## ¿Qué es Spring Boot?

**Spring Boot** es un framework basado en Spring que simplifica la creación de aplicaciones Java al proporcionar configuraciones automáticas y convenciones preestablecidas. Permite desarrollar aplicaciones independientes y listas para producción con mínima configuración.

<img src="imagenes/UD2/spring_boot_intro.jpg" alt="Spring Boot" width="600"/>

---

## Ventajas de Usar Spring Boot

- **Configuración Automática (Auto-Configuration):** Spring Boot configura automáticamente los componentes según las dependencias presentes en el proyecto.
- **Servidor Embebido:** Incluye servidores como Tomcat, Jetty o Undertow, lo que permite ejecutar la aplicación sin necesidad de desplegarla en un servidor externo.
- **Inicio Rápido de Proyectos:** Con herramientas como Spring Initializr, se pueden generar proyectos base en cuestión de minutos.
- **Actuadores (Actuators):** Proporciona endpoints para monitoreo y gestión de la aplicación.
- **Mínima Configuración XML:** Se enfoca en convenciones y anotaciones, reduciendo la necesidad de archivos XML de configuración.

---

## Creación de un Proyecto Spring Boot

### 3.1 Usando Spring Initializr

Spring Initializr es una herramienta web que permite generar proyectos Spring Boot con las dependencias necesarias.

**Pasos:**

1. **Accede a:** [https://start.spring.io/](https://start.spring.io/)
2. **Configura el proyecto:**
   - **Project:** Maven Project
   - **Language:** Java
   - **Spring Boot:** Versión estable actual
   - **Project Metadata:**
     - **Group:** com.ejemplo
     - **Artifact:** demo
     - **Name:** demo
     - **Package Name:** com.ejemplo.demo
   - **Packaging:** Jar
   - **Java Version:** 11 o superior
3. **Añade Dependencias:**
   - **Spring Web:** Para construir aplicaciones web RESTful.
   - **Spring Data JPA:** Para interactuar con bases de datos utilizando JPA.
   - **Spring Security:** Para añadir seguridad a la aplicación.
   - **Thymeleaf:** Para generar vistas HTML desde el backend.
   - **H2 Database:** Base de datos en memoria para pruebas rápidas.
4. **Genera el proyecto** y descarga el archivo ZIP.
5. **Importa el proyecto** en tu IDE preferido (IntelliJ IDEA, Eclipse, etc.).

<iframe width="560" height="315" src="https://www.youtube.com/embed/h5HHguyCii8" frameborder="0" allow="accelerometer; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



### 3.2 Estructura de un Proyecto Spring Boot

Al descomprimir y abrir el proyecto, encontrarás la siguiente estructura:

```
demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── ejemplo/
│   │   │           └── demo/
│   │   │               └── DemoApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── static/
│   │       └── templates/
│   └── test/
│       └── java/
│           └── com/
│               └── ejemplo/
│                   └── demo/
│                       └── DemoApplicationTests.java
├── pom.xml
```

---

## Anatomía de una Aplicación Spring Boot

### 4.1 Ficheros Importantes en un Proyecto Spring Boot

`pom.xml` (o `build.gradle`)
Este archivo gestiona las dependencias y el ciclo de vida del proyecto. En el caso de Maven, `pom.xml` define las dependencias, como Spring Web, así como plugins para construir y ejecutar la aplicación.

`application.properties` (o `application.yml`)
Este archivo contiene la configuración de la aplicación, como el puerto del servidor, detalles de la base de datos, y ajustes de logs.

`src/main/java`
Aquí reside el código fuente de la aplicación, incluyendo controladores, servicios y repositorios. La clase principal con la anotación @SpringBootApplication también se encuentra aquí.

`src/main/resources`
Contiene recursos como plantillas Thymeleaf, archivos estáticos (CSS, JS), y `application.properties`.

`src/test/java`
Esta carpeta alberga las pruebas unitarias y de integración, permitiendo verificar el correcto funcionamiento de la aplicación.

### 4.2 Clase Principal

La clase `DemoApplication.java` es el punto de entrada de la aplicación.

```java
package com.ejemplo.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### 4.3 Archivo `application.properties`

Este archivo se utiliza para configurar propiedades de la aplicación.

**Ejemplo:**

```properties
# Configuracion para el acceso a la Base de Datos
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.globally_quoted_identifiers=true
 
# Puerto donde escucha el servidor una vez se inicie
server.port=8081
 
# Datos de conexion con la base de datos MySQL
spring.datasource.url=jdbc:mysql://localhost:3306/myshoponline
spring.datasource.username=myshopuser
spring.datasource.password=mypassword
spring.datasource.driverClassName=com.mysql.jdbc.Driver
```
Hay que tener en cuenta que la propiedad spring.jpa.hibernate.ddl-auto se utiliza para que la base de datos se genere automáticamente en cada arranque de la aplicación. Esto nos interesará cuando estemos en desarrollo pero no cuando queramos desplegarla en producción. Por lo que tendremos que tener cuidado y controlar el valor de dicha propiedad.

none: Para indicar que no queremos que genere la base de datos
update: Si queremos que la genere de nuevo en cada arranque
create: Si queremos que la cree pero que no la genere de nuevo si ya existe

Cambia el puerto por defecto de 8080 a 8081.

Por último, establecemos la conexión con nuestra base de datos

---

## Ejecutando la Aplicación

Para ejecutar la aplicación, puedes:

- **Desde el IDE:** Ejecutar la clase `DemoApplication.java`.
- **Desde la línea de comandos:**

Navega al directorio del proyecto y ejecuta:

```bash
./mvnw spring-boot:run
```

O si estás usando Gradle:

```bash
./gradlew bootRun
```

**Verificación:**

Abre un navegador web y accede a [http://localhost:8080](http://localhost:8080). Deberías ver un error 404, lo cual es normal ya que no hemos definido ningún controlador aún.

---

## Ejercicios

1. **Personaliza el Mensaje de Inicio:**

   - Modifica el archivo `application.properties` para añadir un banner personalizado al iniciar la aplicación.
   - **Pista:** Usa la propiedad `spring.banner.location`.

2. **Cambio de Puerto:**

   - Cambia el puerto de la aplicación a `9090` y verifica que la aplicación responde en el nuevo puerto.

3. **Creación de un Controlador Simple:**

   - Crea una clase `ControladorSaludo` en el paquete `com.ejemplo.demo`.
   - Define un método que responda a la ruta `/saludo` y que retorne el texto "¡Hola, Spring Boot!".

   **Código de Ejemplo:**

   ```java
   package com.ejemplo.demo;

   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   public class ControladorSaludo {

       @GetMapping("/saludo")
       public String saludo() {
           return "¡Hola, Spring Boot!";
       }
   }
   ```

   - Ejecuta la aplicación y verifica en el navegador accediendo a [http://localhost:9090/saludo](http://localhost:9090/saludo).

---
