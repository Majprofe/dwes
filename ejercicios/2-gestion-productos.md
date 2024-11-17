
# **Práctica: Sistema de Gestión de Productos y Pedidos**
**Duración:** 2 horas  
**Objetivo:** Aplicar los conceptos fundamentales de Spring Boot, como configuración, controladores, servicios, repositorios, JPA, y manejo de rutas.

---

## **Enunciado**

Vas a desarrollar un sistema básico para gestionar **productos** y **pedidos** en una tienda online. El sistema permitirá:  
1. Crear, listar, y eliminar productos.  
2. Crear y listar pedidos.  
3. Buscar productos por categoría y precio.  

---

### **Requisitos**
- **Entidades:**  
  1. **Producto**:
     - `id`: Long (autogenerado).
     - `nombre`: String.
     - `precio`: Double.
     - `categoria`: String.

  2. **Pedido**:
     - `id`: Long (autogenerado).
     - `fecha`: LocalDate.
     - `productos`: Relación ManyToMany con `Producto`.
     - `total`: Double (se calcula automáticamente).

- **Relaciones:**  
  - Un pedido puede contener múltiples productos, y un producto puede estar en varios pedidos.

---

### **Pasos a Seguir**

#### **1. Configuración Inicial (10 minutos)**
1. Crea un proyecto nuevo de Spring Boot con las siguientes dependencias:
   - Spring Web.
   - Spring Data JPA.
   - H2 Database.
2. Configura el archivo `application.properties` para usar H2 y habilitar la consola web.

#### **2. Crear las Entidades y Relaciones (20 minutos)**
1. Define las clases `Producto` y `Pedido` con sus relaciones.
2. Usa las anotaciones de JPA adecuadas:
   - `@Entity`, `@Id`, `@GeneratedValue`.
   - Establece las relaciones necesarias.

---

#### **3. Crear Repositorios y Servicios (20 minutos)**
1. Crea interfaces `ProductoRepository` y `PedidoRepository` que extiendan `JpaRepository`.
2. Implementa un servicio `ProductoService` con métodos para:
   - Guardar un producto.
   - Buscar productos por categoría o rango de precios.
3. Implementa un servicio `PedidoService` con métodos para:
   - Crear un pedido (recibe una lista de IDs de productos).
   - Calcular el total de un pedido.

---

#### **4. Crear Controladores REST (30 minutos)**
1. **ProductoController**:
   - Endpoint para crear un producto: `POST /productos`.
   - Endpoint para listar todos los productos: `GET /productos`.
   - Endpoint para buscar productos por categoría: `GET /productos/categoria/{categoria}`.
   - Endpoint para buscar productos por rango de precios: `GET /productos/precio?min=10&max=50`.

2. **PedidoController**:
   - Endpoint para crear un pedido: `POST /pedidos`.
     - Recibe un array de IDs de productos.
   - Endpoint para listar todos los pedidos: `GET /pedidos`.

---

#### **5. Pruebas y Validaciones (20 minutos)**
1. Usa Postman o cURL para probar los endpoints:
   - Crea varios productos y verifica que se almacenan correctamente.
   - Crea un pedido con productos y verifica que el total se calcula bien.
   - Busca productos por categoría y rango de precios.
2. Asegúrate de manejar errores con mensajes claros si se buscan productos o pedidos inexistentes.
