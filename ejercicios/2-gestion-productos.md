
# **Práctica: Sistema de Gestión de Productos, Pedidos y Clientes**
**Duración:** 2 horas  
**Objetivo:** Aplicar los conceptos fundamentales de Spring Boot, como configuración, controladores, servicios, repositorios, JPA, y manejo de rutas.

---

## **Enunciado**

Vas a desarrollar un sistema básico para gestionar **productos**, **pedidos** y **clientes** en una tienda online. El sistema permitirá:  
1. Crear, listar, y eliminar productos.  
2. Crear y listar pedidos.  
3. Crear y listar clientes.  
4. Asociar pedidos a clientes.

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
     - `total`: Double (se calcula automáticamente).

  3. **Cliente**:
     - `id`: Long (autogenerado).
     - `nombre`: String.
     - `email`: String.

- **Relaciones:**  
  - Un cliente puede tener múltiples pedidos.
  - Un pedido puede contener múltiples productos.
  - Un producto puede estar en varios pedidos.

---

### **Pasos a Seguir**

#### **1. Configuración Inicial (10 minutos)**
1. Crea un proyecto nuevo de Spring Boot con las siguientes dependencias:
   - Spring Web.
   - Spring Data JPA.
   - MySQL Driver.
2. Configura el archivo `application.properties` para usar la base de datos y habilitar la consola web.

#### **2. Crear las Entidades y Relaciones (25 minutos)**
1. Define las clases `Producto`, `Pedido` y `Cliente` con sus relaciones.
2. Usa las anotaciones de JPA adecuadas:
   - `@Entity`, `@Id`, `@GeneratedValue`.
   - Establece las relaciones necesarias.

---

#### **3. Crear Repositorios y Servicios (25 minutos)**
1. Crea interfaces `ProductoRepository`, `PedidoRepository` y `ClienteRepository` que extiendan `JpaRepository`.
2. Implementa un servicio `ProductoService` con métodos para:
   - Guardar un producto.
   - Buscar productos por categoría o rango de precios.
3. Implementa un servicio `PedidoService` con métodos para:
   - Crear un pedido (recibe una lista de IDs de productos y un ID de cliente).
   - Calcular el total de un pedido.
4. Implementa un servicio `ClienteService` con métodos para:
   - Guardar un cliente.
   - Obtener la lista de pedidos de un cliente.

---

#### **4. Crear Controladores REST (35 minutos)**
1. **ProductoController**:
   - Endpoint para crear un producto: `POST /productos`.
   - Endpoint para listar todos los productos: `GET /productos`.
   - Endpoint para buscar productos por categoría: `GET /productos/categoria/{categoria}`.
   - Endpoint para buscar productos por rango de precios: `GET /productos/precio?min=10&max=50`.

2. **PedidoController**:
   - Endpoint para crear un pedido: `POST /pedidos`.
     - Recibe un array de IDs de productos y un ID de cliente.
   - Endpoint para listar todos los pedidos: `GET /pedidos`.

3. **ClienteController**:
   - Endpoint para crear un cliente: `POST /clientes`.
   - Endpoint para listar todos los clientes: `GET /clientes`.
   - Endpoint para listar los pedidos de un cliente: `GET /clientes/{id}/pedidos`.

---

#### **5. Pruebas y Validaciones (25 minutos)**
1. Usa Postman o cURL para probar los endpoints:
   - Crea varios productos, clientes y pedidos.
   - Verifica que los pedidos se asocian correctamente a los clientes y que el total se calcula bien.
   - Busca productos por categoría y rango de precios.
   - Lista los pedidos de un cliente específico.
2. Asegúrate de manejar errores con mensajes claros si se buscan productos, pedidos o clientes inexistentes.
