# Práctica: Autenticación y Autorización con Firebase y Spring Boot

## Objetivo de la Práctica
Implementar un sistema de autenticación y autorización en un backend desarrollado con Spring Boot, integrando Firebase Authentication. La aplicación permitirá la gestión de tareas para usuarios con diferentes roles (“user” y “admin”).

---

## Descripción
Crearás una API REST para una aplicación de gestión de tareas, donde:

- **Los usuarios** podrán registrarse e iniciar sesión mediante Firebase Authentication.
- **El backend** verificará los tokens de Firebase para autenticar a los usuarios.
- **Los roles** determinarán el acceso a diferentes funcionalidades:
  - **User**: Podrá crear, ver y editar sus propias tareas.
  - **Admin**: Podrá ver y gestionar todas las tareas.

---

## Requisitos
1. **Firebase**: Crear un proyecto en Firebase Console.
   - Habilitar el método de autenticación (correo electrónico y contraseña, o Google).
   - Descargar el archivo `serviceAccountKey.json` para integrarlo en Spring Boot.

2. **Backend (Spring Boot)**:
   - Configurar Firebase SDK en Spring Boot.
   - Crear un filtro de Spring Security para validar tokens de Firebase.
   - Implementar endpoints para gestionar tareas y controlar el acceso basado en roles.

3. **Frontend o Cliente REST**:
   - Crear un frontend simple con HTML y JavaScript para consumir la API.
   - Gestionar el inicio de sesión con Firebase Authentication.
   - Enviar el token de autenticación al backend en cada petición.

---

## Entregables
- **Código fuente** del backend Spring Boot.
- **Código fuente del cliente frontend** con integración de Firebase Authentication.
- Documentación de la API (puedes usar Swagger o Postman).

---

## Detalle de la Implementación

### 1. Crear Proyecto en Firebase
- Habilita Firebase Authentication en tu proyecto.
- Configura al menos un método de autenticación (correo electrónico y contraseña).
- Descarga el archivo `serviceAccountKey.json` desde la configuración de Firebase.

### 2. Configurar Spring Boot
1. **Dependencias necesarias**:
   - Spring Security.
   - Spring Web.
   - Firebase Admin SDK.
   - MySQL (para almacenar las tareas).

2. **Configuración de Firebase**:
   - Implementar la inicialización de Firebase en una clase de configuración.

3. **Endpoints REST**:
   - `/tasks`:
     - `GET /tasks`: Obtener todas las tareas (solo para admin).
     - `POST /tasks`: Crear una tarea (para usuarios autenticados).
     - `GET /tasks/user`: Obtener las tareas propias del usuario autenticado (para usuarios con rol USER).

### 3. Roles de Usuario
- Los usuarios deberán tener un campo “role” en Firebase Firestore o un token personalizado.

### 4. Frontend
- Crear un frontend simple que permita:
  - Registro e inicio de sesión mediante Firebase.
  - Consumo de la API REST del backend.
  - Visualización de tareas propias (para usuarios).
  - Visualización y gestión de todas las tareas (para administradores).

---

## Evaluación
Se evaluará:
1. Correcta integración de Firebase Authentication con Spring Boot.
2. Uso de roles para autorizar las rutas de la API.
3. Funcionamiento de los endpoints REST.
4. Implementación y diseño del frontend.

---

## Recomendaciones
- Usa Postman para probar la API antes de crear un cliente frontend.
- Si implementas un frontend, utiliza Firebase SDK para gestionar el inicio de sesión y enviar el token en cada petición al backend.

---

# Rúbrica de Evaluación: Práctica de Autenticación y Autorización con Firebase y Spring Boot

## Criterios de Evaluación

### 1. **Configuración del Proyecto Firebase** (20 puntos)
| Indicador                                      | Puntos |
|-----------------------------------------------|--------|
| Se ha creado correctamente el proyecto en Firebase y habilitado un método de autenticación. | 5      |
| El archivo `serviceAccountKey.json` está configurado correctamente en Spring Boot. | 5      |
| Se ha implementado la inicialización de Firebase en el backend.             | 10     |

### 2. **Implementación del Backend con Spring Boot** (40 puntos)
| Indicador                                      | Puntos |
|-----------------------------------------------|--------|
| El filtro de autenticación para validar tokens de Firebase está correctamente implementado. | 15     |
| Los endpoints están implementados y funcionan según lo especificado:       |        |
| - `POST /tasks`: Crear tareas (autenticación requerida).                | 10     |
| - `GET /tasks`: Obtener todas las tareas (solo admin).                  | 10     |
| - `GET /tasks/user`: Obtener las tareas propias del usuario autenticado. | 5      |

### 3. **Gestor de Roles** (15 puntos)
| Indicador                                      | Puntos |
|-----------------------------------------------|--------|
| Los usuarios tienen roles definidos (“user” y “admin”).              | 5      |
| Los roles están correctamente utilizados para autorizar rutas y acciones.   | 10     |

### 4. **Frontend** (20 puntos)
| Indicador                                      | Puntos |
|-----------------------------------------------|--------|
| Se ha creado un formulario de registro e inicio de sesión funcional con Firebase. | 10     |
| El frontend consume correctamente los endpoints del backend.             | 10     |

### 5. **Documentación** (5 puntos)
| Indicador                                      | Puntos |
|-----------------------------------------------|--------|
| La API está documentada con Swagger o Postman, incluyendo ejemplos de peticiones. | 5      |

---
