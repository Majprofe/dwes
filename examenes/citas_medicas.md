# 📝 Examen de Spring Boot – Gestión de Citas Médicas

## ⏳ Duración
⏰ **2 horas**

---

## 📂 1. Modelado de Datos (3 entidades)

| Entidad   | Atributos                                                                                                      | Notas                                             |
|-----------|----------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| **Paciente** | `id: Long` (PK, autogenerado) <br> `nombre: String` (no nulo, mínimo 3 caracteres) <br> `dni: String` (único, no nulo) | Un paciente puede tener muchas citas              |
| **Medico** | `id: Long` (PK, autogenerado) <br> `nombre: String` (no nulo) <br> `especialidad: String` (no nulo) | Un médico puede tener muchas citas                |
| **Cita** | `id: Long` (PK, autogenerado) <br> `paciente: Paciente` (ManyToOne) <br> `medico: Medico` (ManyToOne) <br> `fechaHora: LocalDateTime` (no nulo) <br> `motivo: String` | Validar que la fecha sea futura                   |

---

## 📡 2. Repositorios

Extiende `JpaRepository`:

- `PacienteRepository`
- `MedicoRepository`
- `CitaRepository`

---

## ⚙️ 3. Servicios

| Servicio         | Funcionalidades obligatorias                                                                                 |
|------------------|--------------------------------------------------------------------------------------------------------------|
| **PacienteService** | Registrar paciente y listar todos                                                                            |
| **MedicoService** | Registrar médico y buscar por especialidad                                                                   |
| **CitaService** | Agendar cita (validar que el médico no tenga otra cita a esa misma hora) <br> Cancelar cita (borrar) <br> Listar citas de un paciente <br> Actualizar motivo de cita |

---

## 🌍 4. Endpoints REST

| Método / URL                         | Descripción                            | Devuelve      |
|--------------------------------------|----------------------------------------|---------------|
| `POST /pacientes`                    | Registrar paciente                     | `Paciente`    |
| `GET  /pacientes`                    | Listar todos los pacientes             | `[Paciente]`  |
| `POST /medicos`                      | Registrar médico                       | `Medico`      |
| `GET  /medicos/especialidad/{tipo}`  | Buscar médicos por especialidad        | `[Medico]`    |
| `POST /citas`                        | Agendar una nueva cita                 | `Cita`        |
| `DELETE /citas/{id}`                 | Cancelar una cita                      | `void`        |
| `PUT  /citas/{id}/motivo`            | Actualizar motivo de la cita           | `Cita`        |
| `GET  /citas/paciente/{pacienteId}`  | Ver historial de citas de un paciente  | `[Cita]`      |

---

## 🧪 5. Pruebas Unitarias (2)

Se deben implementar **pruebas unitarias** utilizando JUnit y Mockito para validar la lógica de negocio.

Las pruebas mínimas requeridas incluyen:

- **CitaService**
  1. **Agendar cita válida:** Verificar que se guarda correctamente una cita cuando los datos son válidos.
  2. **Listar citas de paciente:** Verificar que el método devuelve correctamente la lista de citas asociadas a un paciente específico.

---

## 🌐 6. Swagger

Configura Swagger/OpenAPI para documentar la API.
- **Guarda una captura** de pantalla de la interfaz de Swagger con los endpoints desplegados en `/src/main/resources/capturas`.

---

## 📊 7. Criterios de Calificación (10 puntos)

| # | Criterio                                                  | Pts |
|---|-----------------------------------------------------------|-----|
| 1 | `POST /pacientes` valida datos (nombre, DNI)              | 1.5 |
| 2 | `POST /medicos` y búsqueda por especialidad funciona      | 1.5 |
| 3 | `POST /citas` valida fecha futura y disponibilidad médico | 2.0 |
| 4 | `DELETE /citas/{id}` elimina correctamente                | 1.0 |
| 5 | `GET /citas/paciente/{id}` lista correctamente            | 1.0 |
| 6 | `PUT /citas/{id}/motivo` actualiza correctamente          | 1.0 |
| 7 | Pruebas unitarias (JUnit/Mockito) pasan correctamente     | 1.0 |
| 8 | Swagger configurado y captura entregada                   | 1.0 |

**Reglas de corrección**  
- Endpoint que no compila → 0 pts en ese ítem.  
- Errores de lógica menores → ≤ 50% del ítem.  
- Validaciones y códigos de estado HTTP correctos (201 Created, 404 Not Found, etc.) → nota completa.
