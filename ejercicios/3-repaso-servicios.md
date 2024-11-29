
# Ejercicios de Servicios en Spring Boot

Propuestas para practicar la creación de servicios en Spring Boot. Cada ejercicio incluye entidades y servicios relacionados.

---

## 1. Sistema de Gestión de Tareas Colaborativas

### Entidades:
- **Usuario**: `id`, `nombre`, `email`
- **Proyecto**: `id`, `nombre`, `descripcion`
- **Tarea**: `id`, `titulo`, `descripcion`, `estado` (Pendiente, En progreso, Completada), `usuarioAsignado`, `proyecto`

### Servicios:
1. **Crear Proyecto:** Crear un nuevo proyecto con un nombre único. Si el nombre ya existe, lanzar una excepción personalizada.
2. **Añadir Usuario a Proyecto:** Asignar un usuario a un proyecto existente. Si el usuario ya pertenece al proyecto, no volver a asignarlo.
3. **Crear Tarea:** Crear una tarea dentro de un proyecto y asignarla a un usuario. Validar que el usuario pertenece al proyecto.
4. **Actualizar Estado de Tarea:** Cambiar el estado de una tarea (Pendiente → En progreso → Completada).
5. **Listar Tareas por Proyecto y Estado:** Devolver todas las tareas de un proyecto filtradas por estado (Pendiente, En progreso, Completada).

---

## 2. Sistema de Reserva de Eventos

### Entidades:
- **Usuario**: `id`, `nombre`, `email`
- **Evento**: `id`, `nombre`, `fecha`, `capacidadMaxima`, `usuariosReservados`
- **Reserva**: `id`, `usuario`, `evento`

### Servicios:
1. **Crear Evento:** Crear un nuevo evento con una capacidad máxima de asistentes.
2. **Hacer Reserva:** Permitir que un usuario reserve un evento. Comprobar que no supere la capacidad máxima y que el usuario no haya reservado previamente.
3. **Cancelar Reserva:** Permitir a un usuario cancelar su reserva. Devolver la plaza al evento.
4. **Listar Eventos Disponibles:** Mostrar eventos con plazas libres y su capacidad restante.
5. **Obtener Lista de Asistentes por Evento:** Mostrar los usuarios que tienen reserva para un evento específico.

---

## 3. Sistema de Inventario para una Librería

### Entidades:
- **Libro**: `id`, `titulo`, `autor`, `precio`, `stock`
- **Usuario**: `id`, `nombre`, `email`, `rol` (Administrador o Cliente)
- **Venta**: `id`, `libro`, `usuario`, `cantidad`, `total`

### Servicios:
1. **Añadir Stock de un Libro:** Solo para administradores. Incrementar el stock de un libro existente.
2. **Vender Libro:** Reducir el stock de un libro en función de la cantidad comprada y registrar la venta. Validar que hay suficiente stock disponible.
3. **Listar Libros por Autor o Rango de Precio:** Devolver una lista de libros filtrados por autor o precio.
4. **Obtener Ventas Totales por Usuario:** Calcular el total gastado por un cliente en todas sus compras.
5. **Obtener Reporte de Ventas:** Solo para administradores. Mostrar un listado de todas las ventas, con información detallada de cada una.

---

## 4. Sistema de Gestión de Torneos Deportivos

### Entidades:
- **Equipo**: `id`, `nombre`, `puntos`, `partidosGanados`, `partidosPerdidos`, `partidosEmpatados`
- **Partido**: `id`, `equipoLocal`, `equipoVisitante`, `golesLocal`, `golesVisitante`, `estado` (Pendiente, Finalizado)

### Servicios:
1. **Crear Equipo:** Registrar un nuevo equipo en el torneo. Validar que el nombre no se repita.
2. **Registrar Resultado de Partido:** Actualizar los puntos de los equipos en función del resultado (Ganador +3, Empate +1, Perdedor +0).
3. **Obtener Clasificación:** Listar los equipos ordenados por puntos, con desempates por diferencia de goles.
4. **Obtener Historial de Partidos por Equipo:** Mostrar todos los partidos jugados por un equipo.
5. **Listar Próximos Partidos:** Mostrar los partidos pendientes con fecha y hora.

---

## 5. Sistema de Gestión de Reservas en un Restaurante

### Entidades:
- **Cliente**: `id`, `nombre`, `email`
- **Mesa**: `id`, `numero`, `capacidad`, `disponibilidad` (Disponible/Ocupada)
- **Reserva**: `id`, `mesa`, `cliente`, `fecha`, `hora`

### Servicios:
1. **Hacer Reserva:** Reservar una mesa para un cliente en una fecha y hora específicas. Validar que la mesa esté disponible.
2. **Cancelar Reserva:** Permitir a un cliente cancelar su reserva. Cambiar el estado de la mesa a disponible.
3. **Listar Mesas Disponibles:** Mostrar mesas disponibles para una fecha y hora específicas.
4. **Obtener Historial de Reservas por Cliente:** Mostrar todas las reservas pasadas y futuras de un cliente.
5. **Generar Reporte Diario de Reservas:** Mostrar un listado de todas las reservas para un día específico.
