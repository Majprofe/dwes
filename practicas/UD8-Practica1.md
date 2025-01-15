# Guía para Crear una Aplicación Segura básica con Spring Boot

## **1. Configuración del Proyecto**

1. Crea un proyecto Spring Boot en [Spring Initializr](https://start.spring.io/):
   - **Dependencias necesarias**:
     - **Spring Web**
     - **Spring Security**
     - **Spring Data JPA**
     - **MySQL Database**

2. Descarga y abre el proyecto.

3. Configura el archivo `application.properties`:
   ```properties
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=prueba
spring.datasource.password=prueba
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Si quieres que Spring cree/actualice las tablas automáticamente
spring.jpa.hibernate.ddl-auto=update

# Mostrar las sentencias SQL en el log
spring.jpa.show-sql=true
   ```

---

## **2. Modelo de Usuario y Roles**

### **2.1. Entidad Usuario**
Crea una clase `Usuario`:
```java
package com.ejemplo.seguridad.models;

import jakarta.persistence.*;
import lombok.Data;

@Entity
@Data
public class Usuario {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false)
    private String username;

    @Column(unique = true, nullable = false)
    private String email;

    @Column(nullable = false)
    private String password;

    @Enumerated(EnumType.STRING)
    private Rol rol;
}
```

### **2.2. Enumeración Rol**
Crea una clase para representar los roles:
```java
package com.ejemplo.seguridad.models;

public enum Rol {
    ROLE_USER, ROLE_ADMIN
}
```

### **2.3. Clase RegistroRequest**
Define una clase para manejar el registro de usuarios:
```java
package com.ejemplo.seguridad.models;

import lombok.Data;

@Data
public class RegistroRequest {
    private String username;
    private String email;
    private String password;
}
```

---

## **3. Repositorio**

Crea un repositorio para gestionar los usuarios:
```java
package com.ejemplo.seguridad.repositories;

import com.ejemplo.seguridad.models.Usuario;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    Optional<Usuario> findByUsername(String username);
}
```

---

## **4. Servicios**

### **4.1. AuthService**
Implementa un servicio para manejar el registro de usuarios:
```java
package com.ejemplo.seguridad.services;

import com.ejemplo.seguridad.models.Rol;
import com.ejemplo.seguridad.models.Usuario;
import com.ejemplo.seguridad.repositories.UsuarioRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

@Service
public class AuthService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    public Usuario registrarUsuario(String username, String email, String password) {
        Usuario usuario = new Usuario();
        usuario.setUsername(username);
        usuario.setEmail(email);
        usuario.setPassword(passwordEncoder.encode(password));
        usuario.setRol(Rol.ROLE_USER);
        return usuarioRepository.save(usuario);
    }
}
```

### **4.2. CustomUserDetailsService**
Implementa un servicio para cargar los detalles del usuario:
```java
package com.ejemplo.seguridad.services;

import com.ejemplo.seguridad.models.Usuario;
import com.ejemplo.seguridad.repositories.UsuarioRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.Collections;

@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Usuario usuario = usuarioRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("Usuario no encontrado: " + username));
        return new User(
                usuario.getUsername(),
                usuario.getPassword(),
                Collections.singleton(() -> usuario.getRol().name())
        );
    }
}
```

---

## **5. Controladores**

### **5.1. AuthController**
Crea un controlador para manejar el registro y login de usuarios:
```java
package com.ejemplo.seguridad.controllers;

import es.iesjandula.security1.dto.RegistroRequest;
import es.iesjandula.security1.services.AuthService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.AuthenticationException;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/auth")
public class AuthController {

    @Autowired
    private AuthService authService;

    @Autowired
    private AuthenticationManager authenticationManager;

    @PostMapping("/register")
    public ResponseEntity<String> registrar(@RequestBody RegistroRequest request) {
        authService.registrarUsuario(request.getUsername(), request.getEmail(), request.getPassword());
        return ResponseEntity.ok("Usuario registrado exitosamente");
    }

    @PostMapping("/login")
    public ResponseEntity<String> login(@RequestBody RegistroRequest request) {
        try {
            // Intenta autenticar al usuario
            Authentication authentication = authenticationManager.authenticate(
                    new UsernamePasswordAuthenticationToken(request.getUsername(), request.getPassword())
            );

            // Si la autenticación es exitosa, puedes devolver un token JWT (opcional)
            return ResponseEntity.ok("Login exitoso");
        } catch (AuthenticationException e) {
            return ResponseEntity.status(401).body("Usuario o contraseña incorrectos");
        }
    }
}
```

---

## **6. Configuración de Seguridad**

Configura Spring Security:
```java
package com.ejemplo.seguridad.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.config.http.SessionCreationPolicy;

@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers("/api/auth/**").permitAll()
                    .anyRequest().authenticated()
            )
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .httpBasic();
        return http.build();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {
        return configuration.getAuthenticationManager();
    }
}
```

---

## **7. Prueba la Aplicación**

### **7.1. Registrar un usuario**
- Usa Postman o cualquier cliente HTTP para enviar una solicitud `POST` a `http://localhost:8080/api/auth/register` con el siguiente cuerpo JSON:
  ```json
  {
      "username": "usuario1",
      "email": "usuario1@example.com",
      "password": "password123"
  }
  ```

- Respuesta esperada:
  ```json
  {
      "message": "Usuario registrado exitosamente"
  }
  ```

### **7.2. Verificar login**
- Usa el endpoint `POST /api/auth/login`:
  - **Autorización**: Basic Auth
    - Username: `usuario1`
    - Password: `password123`

- Respuesta esperada:
  ```json
  {
      "message": "Login exitoso"
  }
  ```

### **7.3. Acceder a rutas protegidas**
- Usa un endpoint protegido como `GET /api/tareas`.
- Asegúrate de enviar las credenciales en el encabezado de autorización (Basic Auth).

---

Con estos pasos, puedes implementar y probar un sistema básico de seguridad en Spring Boot. ¡Si necesitas ayuda adicional, no dudes en pedirla! 😊