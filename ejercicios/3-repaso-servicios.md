
# Ejercicios de Servicios en Spring Boot

Propuestas para practicar la creación de servicios en Spring Boot. Cada ejercicio incluye entidades y servicios relacionados.

---

## 1. Sistema de Gestión de Tareas Colaborativas

### Entidades:
- **Usuario**: `id`, `nombre`, `email`
- **Proyecto**: `id`, `nombre`, `descripcion`
- **Tarea**: `id`, `titulo`, `descripcion`, `estado` (Pendiente, En progreso, Completada), `usuarioAsignado`, `proyecto`

### Servicios:
#### 1. Crear Proyecto
- **Descripción:** Crear un proyecto único.  
- **Parámetros:** `(Proyecto proyecto)`  
- **Devuelve:** `Proyecto`  
- **Validación:** Nombre único; lanzar excepción si ya existe.

---

#### 2. Añadir Usuario a Proyecto
- **Descripción:** Asignar usuario a proyecto.  
- **Parámetros:** `(Long proyectoId, Long usuarioId)`  
- **Devuelve:** `Proyecto`  
- **Validación:** Evitar duplicados.

---

#### 3. Crear Tarea
- **Descripción:** Crear tarea y asignar a usuario en proyecto.  
- **Parámetros:** `(Long proyectoId, Long usuarioId, String titulo, String descripcion)`  
- **Devuelve:** `Tarea`  
- **Validación:** Usuario debe pertenecer al proyecto.

---

#### 4. Actualizar Estado de Tarea
- **Descripción:** Cambiar estado de tarea (`Pendiente → En progreso → Completada`).  
- **Parámetros:** `(Long tareaId, Estado nuevoEstado)`  
- **Devuelve:** `Tarea`

---

#### 5. Listar Tareas por Proyecto y Estado
- **Descripción:** Filtrar tareas por estado en un proyecto.  
- **Parámetros:** `(Long proyectoId, Estado estado)`  
- **Devuelve:** `List<Tarea>`

---

## 2. Sistema de Reserva de Eventos

### Entidades:
- **Usuario**: `id`, `nombre`, `email`
- **Evento**: `id`, `nombre`, `fecha`, `capacidadMaxima`, `usuariosReservados`
- **Reserva**: `id`, `usuario`, `evento`

### Servicios:
#### 1. Crear Evento
- **Descripción:** Crear un nuevo evento con una capacidad máxima de asistentes.  
- **Parámetros:** `(Evento evento)`  
- **Devuelve:** `Evento`  

---

#### 2. Hacer Reserva
- **Descripción:** Permitir que un usuario reserve un evento.  
- **Parámetros:** `(Long eventoId, Long usuarioId)`  
- **Devuelve:** `Reserva`  
- **Validación:** Comprobar que no supere la capacidad máxima y que el usuario no haya reservado previamente.

---

#### 3. Cancelar Reserva
- **Descripción:** Permitir a un usuario cancelar su reserva.  
- **Parámetros:** `(Long reservaId)`  
- **Devuelve:** `void`  
- **Validación:** Devolver la plaza al evento.

---

#### 4. Listar Eventos Disponibles
- **Descripción:** Mostrar eventos con plazas libres y su capacidad restante.  
- **Parámetros:** `()`  
- **Devuelve:** `List<Evento>`

---

#### 5. Obtener Lista de Asistentes por Evento
- **Descripción:** Mostrar los usuarios que tienen reserva para un evento específico.  
- **Parámetros:** `(Long eventoId)`  
- **Devuelve:** `List<Usuario>`

---

## 3. Sistema de Inventario para una Librería

### Entidades:
- **Libro**: `id`, `titulo`, `autor`, `precio`, `stock`
- **Usuario**: `id`, `nombre`, `email`, `rol` (Administrador o Cliente)
- **Venta**: `id`, `libro`, `usuario`, `cantidad`, `total`

### Servicios:
### 1. Añadir Stock de un Libro
- **Descripción:** Incrementar el stock de un libro existente.  
- **Parámetros:**  
  - `Long libroId`: ID del libro.  
  - `int cantidad`: Cantidad a añadir al stock.  
- **Devuelve:**  
  - `Libro`: El libro actualizado con el nuevo stock.  
- **Validación:** Solo usuarios con rol de administrador pueden realizar esta acción.

---

### 2. Vender Libro
- **Descripción:** Reducir el stock de un libro en función de la cantidad comprada y registrar la venta.  
- **Parámetros:**  
  - `Long libroId`: ID del libro.  
  - `Long usuarioId`: ID del usuario que realiza la compra.  
  - `int cantidad`: Cantidad de libros a vender.  
- **Devuelve:**  
  - `Venta`: La venta registrada, con detalles del libro, cantidad, y total.  
- **Validación:**  
  - Validar que hay suficiente stock disponible.  
  - Registrar la venta correctamente.

---

### 3. Listar Libros por Autor o Rango de Precio
- **Descripción:** Devolver una lista de libros filtrados por autor o precio.  
- **Parámetros:**  
  - `String autor`: Nombre del autor (opcional).  
  - `Double minPrecio`: Precio mínimo (opcional).  
  - `Double maxPrecio`: Precio máximo (opcional).  
- **Devuelve:**  
  - `List<Libro>`: Lista de libros que cumplen los criterios de búsqueda.  

---

### 4. Obtener Ventas Totales por Usuario
- **Descripción:** Calcular el total gastado por un cliente en todas sus compras.  
- **Parámetros:**  
  - `Long usuarioId`: ID del usuario.  
- **Devuelve:**  
  - `Double`: El total gastado por el usuario.  

---

### 5. Obtener Reporte de Ventas
- **Descripción:** Mostrar un listado de todas las ventas con información detallada (solo para administradores).  
- **Parámetros:**  
  - Ninguno.  
- **Devuelve:**  
  - `List<Venta>`: Listado de todas las ventas, con información detallada sobre libros, cantidades, precios, y compradores.  
- **Validación:** Solo usuarios con rol de administrador pueden acceder al reporte.


---

## 4. Sistema de Gestión de Torneos Deportivos

### Entidades:
- **Equipo**: `id`, `nombre`, `puntos`, `partidosGanados`, `partidosPerdidos`, `partidosEmpatados`
- **Partido**: `id`, `equipoLocal`, `equipoVisitante`, `golesLocal`, `golesVisitante`, `estado` (Pendiente, Finalizado)

### Servicios:
#### 1. Crear Equipo
- **Descripción:** Registrar un nuevo equipo en el torneo.  
- **Entrada:**  
  - `Equipo equipo`: equipo.  
- **Salida:**  
  - `Equipo`: El equipo registrado.  
- **Validación:**  
  - Comprobar que el nombre del equipo no se repita.

---

#### 2. Registrar Resultado de Partido
- **Descripción:** Actualizar los puntos de los equipos en función del resultado del partido.  
- **Entrada:**  
  - `Long partidoId`: ID del partido.  
  - `int golesLocal`: Goles del equipo local.  
  - `int golesVisitante`: Goles del equipo visitante.  
- **Salida:**  
  - `Partido`: El partido actualizado con los resultados y puntos asignados.  
- **Reglas de Puntos:**  
  - **Ganador:** +3 puntos.  
  - **Empate:** +1 punto.  
  - **Perdedor:** +0 puntos.  

---

#### 3. Obtener Clasificación
- **Descripción:** Listar los equipos ordenados por puntos, con desempates por diferencia de goles.  
- **Entrada:**  
  - Ninguna.  
- **Salida:**  
  - `List<Equipo>`: Lista de equipos ordenados por puntos y desempates.  

---

#### 4. Obtener Historial de Partidos por Equipo
- **Descripción:** Mostrar todos los partidos jugados por un equipo específico.  
- **Entrada:**  
  - `Long equipoId`: ID del equipo.  
- **Salida:**  
  - `List<Partido>`: Lista de partidos jugados por el equipo.  

---

#### 5. Listar Próximos Partidos
- **Descripción:** Mostrar los partidos pendientes con fecha y hora.  
- **Entrada:**  
  - Ninguna.  
- **Salida:**  
  - `List<Partido>`: Lista de partidos próximos con fecha y hora programadas. 

---

## 5. Sistema de Gestión de Reservas en un Restaurante

### Entidades:
- **Cliente**: `id`, `nombre`, `email`
- **Mesa**: `id`, `numero`, `capacidad`, `disponibilidad` (Disponible/Ocupada)
- **Reserva**: `id`, `mesa`, `cliente`, `fecha`, `hora`

### Servicios:
#### 1. Hacer Reserva
- **Descripción:** Reservar una mesa para un cliente en una fecha y hora específicas.  
- **Entrada:**  
  - `Long mesaId`: ID de la mesa a reservar.  
  - `Long clienteId`: ID del cliente que realiza la reserva.  
  - `LocalDate fecha`: Fecha de la reserva.  
  - `LocalTime hora`: Hora de la reserva.  
- **Salida:**  
  - `Reserva`: La reserva creada con los detalles correspondientes.  
- **Validación:**  
  - Comprobar que la mesa esté disponible en la fecha y hora indicadas.

---

#### 2. Cancelar Reserva
- **Descripción:** Permitir a un cliente cancelar su reserva.  
- **Entrada:**  
  - `Long reservaId`: ID de la reserva a cancelar.  
- **Salida:**  
  - `void`  
- **Validación:**  
  - Cambiar el estado de la mesa asociada a `Disponible`.

---

#### 3. Listar Mesas Disponibles
- **Descripción:** Mostrar mesas disponibles para una fecha y hora específicas.  
- **Entrada:**  
  - `LocalDate fecha`: Fecha de la consulta.  
  - `LocalTime hora`: Hora de la consulta.  
- **Salida:**  
  - `List<Mesa>`: Lista de mesas disponibles en la fecha y hora indicadas.  

---

#### 4. Obtener Historial de Reservas por Cliente
- **Descripción:** Mostrar todas las reservas pasadas y futuras de un cliente.  
- **Entrada:**  
  - `Long clienteId`: ID del cliente.  
- **Salida:**  
  - `List<Reserva>`: Lista de reservas asociadas al cliente.  

---

#### 5. Generar Reporte Diario de Reservas
- **Descripción:** Mostrar un listado de todas las reservas para un día específico.  
- **Entrada:**  
  - `LocalDate fecha`: Fecha del reporte.  
- **Salida:**  
  - `List<Reserva>`: Listado de reservas realizadas en esa fecha. 
