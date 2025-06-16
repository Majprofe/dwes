
# 📝 Examen recuperación junio de Spring Boot – Gestión de Biblioteca Sencilla

## ⏳ Duración
⏰ **2 horas**

---

## 📂 1. Modelado de Datos (3 entidades)

| Entidad   | Atributos                                                                                                      | Notas                                             |
|-----------|----------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| **Autor** | `id: Long` (PK, autogenerado) <br> `nombre: String` (no nulo, mínimo 3 caracteres)                             | —                                                 |
| **Libro** | `id: Long` (PK, autogenerado) <br> `titulo: String` (no nulo, mínimo 3 caracteres) <br> `autor: Autor` (ManyToOne) <br> `anioPublicacion: int` (≥ 1900) <br> `devuelto: boolean` (por defecto `false`) | Un libro pertenece a un único autor y un autor puede tener muchos libros             |
| **Prestamo** | `id: Long` (PK, autogenerado) <br> `libro: Libro` (ManyToOne) <br> `lector: String` (no nulo) <br> `fechaPrestamo: LocalDate` (no nulo) | Un lector es solo un String (simplificado)        |

---

## 📡 2. Repositorios

Extiende `JpaRepository`:

- `AutorRepository`
- `LibroRepository`
- `PrestamoRepository`

---

## ⚙️ 3. Servicios

| Servicio         | Funcionalidades obligatorias                                                                                 |
|------------------|--------------------------------------------------------------------------------------------------------------|
| **AutorService** | Crear autor y listar todos                                                                                   |
| **LibroService** | Crear libro y listar libros de un autor                                                                      |
| **PrestamoService** | Registrar préstamo (solo si el libro no está ya prestado) <br> Marcar préstamo como devuelto <br> Listar préstamos pendientes |

---

## 🌍 4. Endpoints REST

| Método / URL                         | Descripción                            | Devuelve      |
|--------------------------------------|----------------------------------------|---------------|
| `GET  /autores`                      | Listar autores                         | `[Autor]`     |
| `POST /autores`                      | Crear autor                            | `Autor`       |
| `GET  /libros/autor/{autorId}`       | Listar libros de un autor              | `[Libro]`     |
| `POST /libros`                       | Crear libro                            | `Libro`       |
| `POST /prestamos`                    | Registrar préstamo                     | `Prestamo`    |
| `PUT  /prestamos/{id}/devolver`      | Marcar como devuelto                   | `Prestamo`    |
| `GET  /prestamos/pendientes`         | Préstamos sin devolver                 | `[Prestamo]`  |

---

## 🧪 5. Pruebas Unitarias (2)

Se deben implementar **pruebas unitarias** utilizando JUnit y Mockito para validar la lógica de negocio en los servicios de libros.

Las pruebas mínimas requeridas incluyen:

- **LibroService**
  1. Creación de un libro con valores correctos.
  2. Busqueda de un libro por nombre de autor.


---

## 🌐 6. Swagger

Configura Swagger/OpenAPI y **guarda una captura** de cada endpoint funcionando en  
`/src/main/resources/capturas`.

---

## 📊 7. Criterios de Calificación (10 puntos)

| # | Criterio                                                  | Pts |
|---|-----------------------------------------------------------|-----|
| 1 | `POST y GET /autores` funciona y valida                   | 1.0 |
| 2 | `POST /libros` valida año ≥ 1900 y funciona               | 1.5 |
| 3 | `GET /libros/autor/{id}` devuelve solo libros del autor   | 1.5 |
| 4 | `POST /prestamos` impide doble préstamo                   | 1.5 |
| 5 | `PUT /prestamos/{id}/devolver` cambia el estado           | 1.5 |
| 6 | `GET /prestamos/pendientes` lista correctos               | 1.0 |
| 7 | Pruebas unitarias pasan                                   | 1.0 |
| 8 | Capturas Swagger correctas                                | 1.0 |

**Reglas de corrección**  
- Endpoint que no compila → 0 pts en ese ítem.  
- Errores menores → ≤ 50 % del ítem.  
- Validaciones correctas → nota completa.

---
