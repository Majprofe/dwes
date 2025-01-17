
# Práctica Guiada: Autenticación y Autorización con Firebase en un Proyecto Spring Boot

## Objetivo
Desarrollar un sistema completo con un **frontend** que permita registrar e iniciar sesión en Firebase, y un **backend** en Spring Boot que valide los tokens de Firebase, implemente control de acceso basado en roles y exponga endpoints protegidos.

---

## Descripción General

1. **Frontend**:
   - **Registro**: Formulario para registrar un usuario con email, contraseña y rol (`USER` o `ADMIN`) en Firebase.
   - **Inicio de sesión**: Formulario para autenticar al usuario en Firebase, obtener un token JWT y usarlo en las peticiones al backend.

2. **Backend**:
   - Validación de los tokens JWT de Firebase.
   - Implementación de control de acceso con Spring Security basado en los roles definidos durante el registro.
   - Exposición de varios endpoints protegidos según los roles.

---

## Endpoints del Backend

### 1. Públicos (sin autenticación)
- **`GET /public/info`**: Devuelve información pública.  
  **Ejemplo de respuesta:**
  ```json
  { "message": "Este es un endpoint público accesible para cualquiera." }
  ```

### 2. Protegidos (requieren autenticación)
- **`GET /user/profile`**:  
  - Accesible para usuarios con rol `USER` o `ADMIN`.  
  - Devuelve los datos del usuario autenticado extraídos del token JWT.  
  **Ejemplo de respuesta:**
  ```json
  {
    "email": "usuario@ejemplo.com",
    "role": "USER",
    "message": "Perfil del usuario autenticado."
  }
  ```

- **`GET /admin/dashboard`**:  
  - Accesible solo para usuarios con rol `ADMIN`.  
  - Devuelve un mensaje exclusivo para administradores.  
  **Ejemplo de respuesta:**
  ```json
  {
    "email": "admin@ejemplo.com",
    "role": "ADMIN",
    "message": "Bienvenido al dashboard del administrador."
  }
  ```

- **`POST /user/data`**:  
  - Accesible para usuarios con rol `USER` o `ADMIN`.  
  - Permite enviar datos específicos que se procesarán en el backend.  
  **Ejemplo de body:**
  ```json
  {
    "data": "Ejemplo de datos enviados por el usuario"
  }
  ```
  **Ejemplo de respuesta:**
  ```json
  {
    "message": "Datos recibidos correctamente.",
    "data": "Ejemplo de datos enviados por el usuario"
  }
  ```

- **`DELETE /admin/remove`**:  
  - Accesible solo para administradores (`ADMIN`).  
  - Simula la eliminación de un recurso del sistema.  
  **Ejemplo de body:**
  ```json
  {
    "resourceId": "12345"
  }
  ```
  **Ejemplo de respuesta:**
  ```json
  {
    "message": "Recurso eliminado con éxito.",
    "resourceId": "12345"
  }
  ```

---

## Criterios de Evaluación
1. Los endpoints deben funcionar correctamente y validar los roles de usuario según el token JWT.
2. El frontend debe registrar e iniciar sesión en Firebase correctamente y enviar el token en las peticiones al backend.
3. El backend debe validar los tokens de Firebase, manejar los roles y restringir el acceso según corresponda.

---

