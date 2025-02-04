# PRUEBA TÉCNICA: GESTIÓN DE PEDIDOS EN SPRING BOOT

## 1. DESCRIPCIÓN

En esta prueba técnica, se debe desarrollar un **sistema de gestión de pedidos** para una tienda online de productos electrónicos. La aplicación debe ser un **API REST** desarrollada en **Spring Boot**, aplicando buenas prácticas de desarrollo y asegurando una estructura modular y organizada.

**Duración:** 2 horas y 30 minutos.

## 2. MODELADO DE DATOS

El sistema contará con **dos entidades principales:** `Pedido` y `Factura`, las cuales deben ser modeladas con anotaciones de JPA.

### **Entidad Pedido**

| Atributo         | Tipo            | Restricciones                 |
|----------------|--------------|----------------------------|
| `id`          | `Long`        | Clave primaria, autogenerado |
| `cliente`     | `String`      | No vacío                     |
| `producto`    | `String`      | No nulo                      |
| `cantidad`    | `int`         | Mayor que 0                  |
| `precioUnitario` | `double`   | Mayor que 0                  |
| `total`       | `double`      | Se calcula automáticamente   |
| `estado`      | `Enum`      | Valores permitidos: PENDIENTE, PAGADO, CANCELADO |
| `fechaPedido` | `LocalDateTime` | Se asigna automáticamente |

### **Entidad Factura**

| Atributo         | Tipo            | Restricciones                 |
|----------------|--------------|----------------------------|
| `id`          | `Long`        | Clave primaria, autogenerado |
| `pedido`      | `Pedido`      | Relación OneToOne           |
| `fechaEmision` | `LocalDateTime` | Se asigna automáticamente |
| `total`       | `double`      | Se asigna desde el pedido   |

## 3. REPOSITORIOS

Se deben crear los siguientes repositorios extendiendo `JpaRepository`:

- `PedidoRepository`: Contiene métodos para listar pedidos por estado y cliente.
- `FacturaRepository`: Manejo de facturas generadas.

## 4. SERVICIOS Y LÓGICA DE NEGOCIO

### **Servicio PedidoService**

Debe implementar:

1. Creación de un pedido con las validaciones correspondientes.
2. Cambio de estado de un pedido (PENDIENTE, PAGADO, CANCELADO).
3. Cancelación de un pedido (no debe cancelarse si ya ha sido pagado).
4. Listado de pedidos por cliente.
5. Implementación de **caché** en la consulta de pedidos.

### **Servicio FacturaService**

Debe implementar:

1. Generación automática de una factura cuando un pedido se marca como pagado.
2. Cálculo del total desde el pedido.
3. Obtención de una factura por ID.

## 5. ENDPOINTS REST

### **Endpoints del PedidoController**

| Método HTTP | Endpoint                 | Descripción                         |
|-------------|------------------------|---------------------------------|
| `POST`     | `/pedidos`              | Crear un pedido                |
| `PUT`      | `/pedidos/{id}/pagar`   | Marcar un pedido como pagado y generar factura |
| `PUT`      | `/pedidos/{id}/cancelar` | Cancelar un pedido (si no está pagado) |
| `GET`      | `/pedidos`              | Listar todos los pedidos        |
| `GET`      | `/pedidos/cliente/{nombre}` | Listar pedidos por cliente |

### **Endpoints del FacturaController**

| Método HTTP | Endpoint            | Descripción                |
|-------------|-------------------|-------------------------|
| `GET`      | `/facturas/{id}`   | Obtener una factura por ID |

## 6. DOCUMENTACIÓN DE LA API

Se debe implementar **documentación de la API con Swagger** para facilitar la prueba y exploración de los endpoints.
Crea una carpeta llamada **capturas** en resource y realiza llamadas a cada uno de los endpoints, guarda una captura de esas llamadas en el swagger dentro de la carpeta capturas.

## 7. PRUEBAS UNITARIAS

Se deben implementar **pruebas unitarias** utilizando JUnit y Mockito para validar la lógica de negocio en los servicios.

Las pruebas mínimas requeridas incluyen:

- **PedidoService**
  1. Creación de un pedido con valores correctos.
  2. Busqueda de un pedido por nombre de cliente.

## 8. CRITERIOS DE CALIFICACIÓN

La calificación total será sobre **10 puntos** distribuidos de la siguiente manera:

| Criterio                         | Puntos | Requisitos para obtener la puntuación completa |
|---------------------------------|--------|------------------------------------------------|
| **Modelado de datos y proyecto**           | 2      | Las entidades deben estar correctamente definidas con las anotaciones adecuadas y el proyecto con las dependencias adecuadas. |
| **Servicios y lógica de negocio** | 3.5    | Implementación correcta de la lógica de negocio. |
| **Uso de caché** | 0.5    | Uso de caché en la consulta de pedidos. |
| **Validaciones** | 0.5    | Realización correcta de las validaciones de las entidades y gestión adecuada de errores en los controladores |
| **Endpoints REST**               | 1.5    | Los endpoints deben estar correctamente implementados y funcionales. |
| **Documentación con Swagger**    | 1      | La API debe estar documentada y accesible a través de Swagger. |
| **Pruebas unitarias**            | 1      | Implementación de pruebas unitarias con JUnit y Mockito para validar los métodos indicados. |

