## **Proyecto: Servicio de Reservas de Pistas Deportivas**

### **Descripción del Proyecto**

El objetivo es desarrollar una aplicación para gestionar reservas de pistas deportivas, enfocada en un sistema de pádel. Los usuarios podrán registrarse y reservar pistas en horarios establecidos. Los horarios disponibles serán en bloques de **1 hora y media**, comenzando a las **9:00 AM** y terminando a las **13:30 PM** por la mañana, y desde las **17:00 PM** hasta las **21:30 PM** por la tarde. Los usuarios podrán ver la disponibilidad y gestionar sus reservas.

---

## **Requisitos del Proyecto**

### **Entidades Principales**

1. **Usuario**
   
   - **Campos:**
     - `nombre`: String
     - `email`: String - Primary Key
     - `password`: String
     - `fechaRegistro`: LocalDateTime
   - **Relaciones:**
     - One-to-Many con `Reserva` (un usuario puede tener muchas reservas).

2. **Pista**
   
   - **Campos:**
     - `nombre`: String (por ejemplo, "Pista 1", "Pista 2") - Primary key
     - `ubicacion`: String
     - `tipo`: String (por ejemplo, "Pádel", "Tenis")
   - **Relaciones:**
     - One-to-Many con `Reserva` (una pista puede tener muchas reservas).

3. **Reserva**
   
   - **Campos:**
     - `fechaReserva`: LocalDate (fecha de la reserva).
     - `horaInicio`: LocalTime (hora de inicio de la reserva, como 9:00, 10:30, etc.).
     - `horaFin`: LocalTime (hora de fin de la reserva, como 10:30, 12:00, etc.).
     - Primary key: Email usuario y nombre de pista
   - **Relaciones:**
     - Many-to-One con `Usuario` (usuario que reserva la pista).
     - Many-to-One con `Pista` (pista que se reserva).

---

## **Endpoints de la Aplicación**

### **1. Usuario**

- **Registro de usuario**
  - Método: `POST`
  - URL: `/usuarios/registro`
  - Descripción: Registra un nuevo usuario.
  - Cuerpo de la solicitud (JSON):
    ```json
    {
      "nombre": "Nombre del usuario",
      "email": "usuario@example.com",
      "password": "contraseña123"
    }
    ```
  - Respuesta exitosa (JSON):
    ```json
    {
      "nombre": "Nombre del usuario",
      "email": "usuario@example.com",
      "fechaRegistro": "2024-10-09T12:34:56"
    }
    ```

### **2. Pista**

- **Obtener todas las pistas**
  - Método: `GET`
  - URL: `/pistas`
  - Descripción: Devuelve una lista de todas las pistas disponibles.
  - Respuesta exitosa (JSON):
    ```json
    [
      {
        "nombre": "Pista 1",
        "ubicacion": "Complejo Deportivo A",
        "tipo": "Pádel"
      },
      {
        "nombre": "Pista 2",
        "ubicacion": "Complejo Deportivo B",
        "tipo": "Pádel"
      }
    ]
    ```

- **Crear una nueva pista**
  - Método: `POST`
  - URL: `/pistas`
  - Descripción: Crea una nueva pista.
  - Cuerpo de la solicitud (JSON):
    ```json
    {
      "nombre": "Pista 1",
      "ubicacion": "Complejo Deportivo A",
      "tipo": "Pádel"
    }
    ```
  - Respuesta exitosa (JSON):
    ```json
    {
      "nombre": "Pista 1",
      "ubicacion": "Complejo Deportivo A",
      "tipo": "Pádel"
    }
    ```

### **3. Reserva**

- **Reservar una pista**
  - Método: `POST`
  - URL: `/reservas`
  - Descripción: Crea una nueva reserva para una pista en un horario específico.
  - Parámetros de consulta:
    - `usuarioEmail`: Email del usuario que hace la reserva.
    - `pistaNombre`: Nombre de la pista a reservar.
    - `fecha`: Fecha de la reserva (formato `yyyy-MM-dd`).
    - `horaInicio`: Hora de inicio de la reserva (formato `HH:mm`).
    - `horaFin`: Hora de fin de la reserva (formato `HH:mm`).
  - Ejemplo de URL: 
    ```
    /reservas?usuarioId=1&pistaId=2&fecha=2024-10-10&horaInicio=09:00&horaFin=10:30
    ```
  - Respuesta exitosa (JSON):
    ```json
    {
      "fechaReserva": "2024-10-10",
      "horaInicio": "09:00",
      "horaFin": "10:30",
      "usuario": {
        "nombre": "Nombre del usuario",
        "email": "usuario@example.com"
      },
      "pista": {
        "nombre": "Pista 2",
        "ubicacion": "Complejo Deportivo B"
      }
    }
    ```

- **Consultar reservas por pista y fecha**
  - Método: `GET`
  - URL: `/reservas/pista/{pistaNombre}`
  - Descripción: Devuelve todas las reservas de una pista para una fecha específica.
  - Parámetro de consulta:
    - `fecha`: Fecha de las reservas (formato `yyyy-MM-dd`).
  - Ejemplo de URL:
    ```
    /reservas/pista/Pista2?fecha=2024-10-10
    ```
  - Respuesta exitosa (JSON):
    ```json
    [
      {
        "fechaReserva": "2024-10-10",
        "horaInicio": "09:00",
        "horaFin": "10:30",
        "usuario": {
          "nombre": "Nombre del usuario",
          "email": "usuario@example.com"
        }
      },
      {
        "fechaReserva": "2024-10-10",
        "horaInicio": "10:30",
        "horaFin": "12:00",
        "usuario": {
          "nombre": "Otro usuario",
          "email": "otro@example.com"
        }
      }
    ]
    ```

---

## **Consideraciones Técnicas**

### **Base de Datos**

- Utilizar **MySQL** como sistema de gestión de base de datos.
- Configurar la base de datos en el archivo `application.properties`.

### **ORM**

- Utilizar **Spring Data JPA** para el mapeo objeto-relacional.

### **Transacciones**

- Gestionar transacciones en las operaciones críticas (por ejemplo, al crear una reserva).

### **Validaciones**

- Asegurarse de que los tramos horarios para la reserva sean de 1 hora y media.
- Validar que no haya reservas superpuestas para la misma pista y horario.

### **Manejo de Excepciones**

- Manejar adecuadamente las excepciones como:
  - Usuario no encontrado.
  - Pista no encontrada.
  - Pista no disponible en el horario solicitado.

---

## **Entrega del Proyecto**

1. **Código Fuente:**
   - Subir el código a un repositorio en GitHub y compartirlo con el profesor.
2. **Instrucciones:**
   - Proporcionar instrucciones claras sobre cómo ejecutar la aplicación.
3. **Documentación:**
   - Explicar las decisiones de diseño y realizar la documentación necesaria para que se entienda que hace cada cosa.
4. **Demostración:**
   - Preparar un pdf realizando capturas del postman comprobando que funcionan todos los endpoints.

