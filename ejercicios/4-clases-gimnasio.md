# **Examen Práctico de Spring Boot – Gestión de Clases en un Gimnasio**  

📅 **Duración:** 2.5 horas  
📌 **Objetivo:** Evaluar la capacidad de desarrollar una API REST con buenas prácticas y conceptos avanzados de Spring Boot.

## **📝 Contexto**
El gimnasio "Best Studio" necesita una API REST para gestionar sus **clases dirigidas**. A través de esta API, los clientes podrán inscribirse en clases, los administradores podrán gestionar la oferta de clases y se implementará un sistema de caché para mejorar el rendimiento.

El objetivo es desarrollar esta API aplicando las mejores prácticas de **validaciones, uso de Lombok, caché en los servicios, documentación con Swagger y pruebas con JUnit y Mockito**.

---

## **📌 Requisitos del sistema**

### **1️⃣ Configuración inicial**
1. Configura un **proyecto Spring Boot** con las dependencias necesarias:
   - Spring Web
   - Spring Data JPA
   - Lombok
   - Spring Boot Validation
   - Spring Cache
   - Swagger (Springdoc OpenAPI)
   - Spring Boot Starter Test
   - Base de datos en memoria H2 o MySQL
2. Configura la aplicación para que funcione en el puerto **8081** y añade un mensaje de bienvenida personalizado.

---

### **2️⃣ Modelado de entidades**
Crea las siguientes entidades asegurando un correcto uso de **Lombok y validaciones**:

1. **ClaseDirigida**
   - Identificador autogenerado.
   - Nombre de la clase (**obligatorio**).
   - Nombre del instructor (**obligatorio**).
   - Fecha de la clase (**debe ser futura o actual**).
   - Número máximo de participantes (**mínimo 1, máximo 20**).

2. **Cliente**
   - Identificador autogenerado.
   - Nombre (**obligatorio**).
   - Correo electrónico (**debe ser válido**).

3. **Reserva**
   - Identificador autogenerado.
   - Cliente (**relación ManyToOne**).
   - ClaseDirigida (**relación ManyToOne**).

---

### **3️⃣ Implementación de la lógica del negocio**
1. **Servicio de Clases Dirigidas**:
   - Implementar un método para obtener todas las clases dirigidas **con caché** para mejorar el rendimiento.

2. **Controladores REST**:
   - Crear los endpoints necesarios para:
     - Obtener todas las clases dirigidas.
     - Registrar nuevos clientes.
     - Reservar plazas en clases dirigidas.

---

### **4️⃣ Documentación con Swagger**
- Implementa **Swagger** para documentar los endpoints.
- Explica en qué **URL** se puede acceder a la documentación generada.

---

### **5️⃣ Pruebas unitarias y de integración**
1. Implementa **pruebas unitarias con JUnit y Mockito** para el **servicio de clases dirigidas**.
2. Implementa un test para el **controlador que registra nuevos clientes**.
3. Asegura que las pruebas verifican correctamente el comportamiento esperado:
   - Ejemplo de caso de prueba: El servicio debe retornar una lista vacía si no hay clases registradas.
   - Otro ejemplo: El servicio debe lanzar un error si se quiere realizar una reserva en una clase que está llena.

---

## **📌 Entrega**
1. Sube el código a un **repositorio en GitHub**.
2. Incluye un **README.md** con instrucciones para ejecutar el proyecto.
3. Añade una **colección de Postman** para probar la API.
4. Proporciona una **captura de pantalla de Swagger** mostrando los endpoints disponibles y añadelo en el README.md.

---
