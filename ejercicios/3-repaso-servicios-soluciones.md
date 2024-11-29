
# Soluciones Completas a los Ejercicios de Servicios en Spring Boot
---

## 1. Sistema de Gestión de Tareas Colaborativas

### Servicios:
1. **Crear Proyecto**
```java
public Proyecto crearProyecto(String nombre, String descripcion) {
    if (proyectoRepository.existsByNombre(nombre)) {
        throw new ProyectoYaExisteException("El proyecto ya existe.");
    }
    Proyecto proyecto = new Proyecto(nombre, descripcion);
    return proyectoRepository.save(proyecto);
}
```

2. **Añadir Usuario a Proyecto**
```java
public Proyecto añadirUsuarioAProyecto(Long proyectoId, Long usuarioId) {
    Proyecto proyecto = proyectoRepository.findById(proyectoId)
                          .orElseThrow(() -> new ProyectoNoEncontradoException());
    Usuario usuario = usuarioRepository.findById(usuarioId)
                        .orElseThrow(() -> new UsuarioNoEncontradoException());

    if (!proyecto.getUsuarios().contains(usuario)) {
        proyecto.getUsuarios().add(usuario);
    }
    return proyectoRepository.save(proyecto);
}
```

3. **Crear Tarea**
```java
public Tarea crearTarea(Long proyectoId, Long usuarioId, String titulo, String descripcion) {
    Proyecto proyecto = proyectoRepository.findById(proyectoId)
                          .orElseThrow(() -> new ProyectoNoEncontradoException());
    Usuario usuario = usuarioRepository.findById(usuarioId)
                        .orElseThrow(() -> new UsuarioNoEncontradoException());

    if (!proyecto.getUsuarios().contains(usuario)) {
        throw new UsuarioNoAsignadoException("El usuario no pertenece al proyecto.");
    }

    Tarea tarea = new Tarea(titulo, descripcion, Estado.PENDIENTE, usuario, proyecto);
    return tareaRepository.save(tarea);
}
```

4. **Actualizar Estado de Tarea**
```java
public Tarea actualizarEstadoTarea(Long tareaId, Estado nuevoEstado) {
    Tarea tarea = tareaRepository.findById(tareaId)
                   .orElseThrow(() -> new TareaNoEncontradaException());
    tarea.setEstado(nuevoEstado);
    return tareaRepository.save(tarea);
}
```

5. **Listar Tareas por Proyecto y Estado**
```java
public List<Tarea> listarTareasPorProyectoYEstado(Long proyectoId, Estado estado) {
    return tareaRepository.findByProyectoIdAndEstado(proyectoId, estado);
}
```

---

## 2. Sistema de Reserva de Eventos

### Servicios:
1. **Crear Evento**
```java
public Evento crearEvento(String nombre, LocalDate fecha, int capacidadMaxima) {
    if (eventoRepository.existsByNombre(nombre)) {
        throw new EventoYaExisteException("El evento ya existe.");
    }
    Evento evento = new Evento(nombre, fecha, capacidadMaxima);
    return eventoRepository.save(evento);
}
```

2. **Hacer Reserva**
```java
public Reserva hacerReserva(Long eventoId, Long usuarioId) {
    Evento evento = eventoRepository.findById(eventoId)
                    .orElseThrow(() -> new EventoNoEncontradoException());
    Usuario usuario = usuarioRepository.findById(usuarioId)
                      .orElseThrow(() -> new UsuarioNoEncontradoException());

    if (evento.getUsuariosReservados().size() >= evento.getCapacidadMaxima()) {
        throw new EventoCompletoException("El evento ya no tiene plazas disponibles.");
    }

    Reserva reserva = new Reserva(usuario, evento);
    evento.getUsuariosReservados().add(usuario);
    reservaRepository.save(reserva);
    eventoRepository.save(evento);

    return reserva;
}
```

3. **Cancelar Reserva**
```java
public void cancelarReserva(Long reservaId) {
    Reserva reserva = reservaRepository.findById(reservaId)
                      .orElseThrow(() -> new ReservaNoEncontradaException());

    Evento evento = reserva.getEvento();
    evento.getUsuariosReservados().remove(reserva.getUsuario());
    eventoRepository.save(evento);

    reservaRepository.delete(reserva);
}
```

4. **Listar Eventos Disponibles**
```java
public List<Evento> listarEventosDisponibles() {
    return eventoRepository.findByUsuariosReservadosCountLessThanCapacidadMaxima();
}
```

5. **Obtener Lista de Asistentes por Evento**
```java
public List<Usuario> obtenerAsistentesPorEvento(Long eventoId) {
    Evento evento = eventoRepository.findById(eventoId)
                    .orElseThrow(() -> new EventoNoEncontradoException());
    return evento.getUsuariosReservados();
}
```

---

## 3. Sistema de Inventario para una Librería

### Servicios:
1. **Añadir Stock de un Libro**
```java
public Libro añadirStock(Long libroId, int cantidad) {
    Libro libro = libroRepository.findById(libroId)
                 .orElseThrow(() -> new LibroNoEncontradoException());
    libro.setStock(libro.getStock() + cantidad);
    return libroRepository.save(libro);
}
```

2. **Vender Libro**
```java
public Venta venderLibro(Long libroId, Long usuarioId, int cantidad) {
    Libro libro = libroRepository.findById(libroId)
                 .orElseThrow(() -> new LibroNoEncontradoException());

    if (libro.getStock() < cantidad) {
        throw new StockInsuficienteException("No hay suficiente stock disponible.");
    }

    Usuario usuario = usuarioRepository.findById(usuarioId)
                     .orElseThrow(() -> new UsuarioNoEncontradoException());

    libro.setStock(libro.getStock() - cantidad);
    libroRepository.save(libro);

    Venta venta = new Venta(libro, usuario, cantidad, libro.getPrecio() * cantidad);
    return ventaRepository.save(venta);
}
```

3. **Listar Libros por Autor o Rango de Precio**
```java
public List<Libro> listarLibros(String autor, Double precioMin, Double precioMax) {
    return libroRepository.findByAutorAndPrecioBetween(autor, precioMin, precioMax);
}
```

4. **Obtener Ventas Totales por Usuario**
```java
public double obtenerVentasTotalesPorUsuario(Long usuarioId) {
    return ventaRepository.findByUsuarioId(usuarioId)
           .stream()
           .mapToDouble(Venta::getTotal)
           .sum();
}
```

5. **Obtener Reporte de Ventas**
```java
public List<Venta> obtenerReporteDeVentas() {
    return ventaRepository.findAll();
}
```

---

## 4. Sistema de Gestión de Torneos Deportivos

### Servicios:
1. **Crear Equipo**
```java
public Equipo crearEquipo(String nombre) {
    if (equipoRepository.existsByNombre(nombre)) {
        throw new EquipoYaExisteException("El equipo ya existe.");
    }
    Equipo equipo = new Equipo(nombre, 0, 0, 0, 0);
    return equipoRepository.save(equipo);
}
```

2. **Registrar Resultado de Partido**
```java
public Partido registrarResultado(Long partidoId, int golesLocal, int golesVisitante) {
    Partido partido = partidoRepository.findById(partidoId)
                     .orElseThrow(() -> new PartidoNoEncontradoException());

    partido.setGolesLocal(golesLocal);
    partido.setGolesVisitante(golesVisitante);
    partido.setEstado(Estado.FINALIZADO);

    Equipo local = partido.getEquipoLocal();
    Equipo visitante = partido.getEquipoVisitante();

    if (golesLocal > golesVisitante) {
        local.incrementarPuntos(3);
        visitante.incrementarPartidosPerdidos();
    } else if (golesLocal < golesVisitante) {
        visitante.incrementarPuntos(3);
        local.incrementarPartidosPerdidos();
    } else {
        local.incrementarPuntos(1);
        visitante.incrementarPuntos(1);
    }

    equipoRepository.save(local);
    equipoRepository.save(visitante);

    return partidoRepository.save(partido);
}
```

3. **Obtener Clasificación**
```java
public List<Equipo> obtenerClasificacion() {
    return equipoRepository.findAll(Sort.by(Sort.Direction.DESC, "puntos"));
}
```

4. **Obtener Historial de Partidos por Equipo**
```java
public List<Partido> obtenerHistorialPorEquipo(Long equipoId) {
    return partidoRepository.findByEquipoLocalIdOrEquipoVisitanteId(equipoId, equipoId);
}
```

5. **Listar Próximos Partidos**
```java
public List<Partido> listarProximosPartidos() {
    return partidoRepository.findByEstado(Estado.PENDIENTE);
}
```

---

## 5. Sistema de Gestión de Reservas en un Restaurante

### Servicios:
1. **Hacer Reserva**
```java
public Reserva hacerReserva(Long mesaId, Long clienteId, LocalDate fecha, LocalTime hora) {
    Mesa mesa = mesaRepository.findById(mesaId)
               .orElseThrow(() -> new MesaNoEncontradaException());

    if (!mesa.isDisponible(fecha, hora)) {
        throw new MesaNoDisponibleException("La mesa no está disponible en esta fecha y hora.");
    }

    Cliente cliente = clienteRepository.findById(clienteId)
                     .orElseThrow(() -> new ClienteNoEncontradoException());

    Reserva reserva = new Reserva(mesa, cliente, fecha, hora);
    mesa.reservar(fecha, hora);
    mesaRepository.save(mesa);

    return reservaRepository.save(reserva);
}
```

2. **Cancelar Reserva**
```java
public void cancelarReserva(Long reservaId) {
    Reserva reserva = reservaRepository.findById(reservaId)
                      .orElseThrow(() -> new ReservaNoEncontradaException());

    Mesa mesa = reserva.getMesa();
    mesa.cancelarReserva(reserva.getFecha(), reserva.getHora());
    mesaRepository.save(mesa);

    reservaRepository.delete(reserva);
}
```

3. **Listar Mesas Disponibles**
```java
public List<Mesa> listarMesasDisponibles(LocalDate fecha, LocalTime hora) {
    return mesaRepository.findByDisponibilidad(fecha, hora);
}
```

4. **Obtener Historial de Reservas por Cliente**
```java
public List<Reserva> obtenerHistorialDeReservasPorCliente(Long clienteId) {
    Cliente cliente = clienteRepository.findById(clienteId)
                     .orElseThrow(() -> new ClienteNoEncontradoException());
    return reservaRepository.findByClienteId(cliente.getId());
}
```

5. **Generar Reporte Diario de Reservas**
```java
public List<Reserva> generarReporteDiarioReservas(LocalDate fecha) {
    return reservaRepository.findByFecha(fecha);
}
```

---
