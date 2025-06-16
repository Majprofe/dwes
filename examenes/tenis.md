# 📝 Prueba Técnica de Spring Boot - Gestión de Torneos de Tenis

## 📌 Descripción
En esta prueba técnica desarrollarás un sistema de **gestión de torneos de tenis**. Se requiere una API REST con **Spring Boot**, que permita:
- **Registrar jugadores y partidos**.
- **Actualizar rankings de jugadores**.
- **Consultar partidos jugados por cada jugador**.
- **Probar los endpoints con Swagger y realizar una captura de pantalla.**
- **Implementar dos pruebas unitarias.**

---

## ⏳ Duración
⏰ **2 horas y 15 minutos**

## 📂 1. Modelado de Datos (2 entidades principales)

### **🎾 Entidad `Jugador`**
| Atributo       | Tipo        | Restricciones |
|---------------|------------|--------------|
| `id`         | `Long`      | Autogenerado (PK) |
| `nombre`     | `String`    | No nulo, mínimo 3 caracteres |
| `ranking`    | `int`       | Mayor que 0, menor que 1000 |
| `edad`       | `int`       | Mayor que 15 |
| `equipo`     | `String`    | No nulo |

---

### **🏆 Entidad `Partido`**
| Atributo       | Tipo        | Restricciones |
|---------------|------------|--------------|
| `id`         | `Long`      | Autogenerado (PK) |
| `jugador1`   | `Jugador`   | ManyToOne |
| `jugador2`   | `Jugador`   | ManyToOne |
| `fecha`      | `LocalDateTime` | No nulo |
| `resultado`  | `String`    | Puede ser `null` hasta que se juegue |

---

## **📡 2. Repositorios**
Crea los repositorios extendiendo `JpaRepository`:
- `JugadorRepository` (con métodos para buscar jugadores por ranking)
- `PartidoRepository` (con métodos para listar partidos de un jugador)

---

## **⚙️ 3. Servicios y Lógica de Negocio**

### **📌 Servicio `JugadorService`**
Debe implementar:
1. **Listar jugadores ordenados por ranking.**
2. **Buscar jugadores por nombre.**
3. **Actualizar el ranking de un jugador.**

### **📌 Servicio `PartidoService`**
Debe implementar:
1. **Registrar un partido entre dos jugadores** (validando que no jueguen contra sí mismos).
2. **Actualizar resultado de un partido.**
3. **Listar partidos de un jugador.**

---

## **🌍 4. Documentación con Swagger**
1. Instala swagger en el proyecto
2. Realiza capturas de pantalla del funcionamiento de los endpoints y guardalo en /resources/capturas

---

## **🌍 5. Endpoints REST**
### **Endpoints del `JugadorController`**
| Método HTTP | Endpoint               | Descripción |
|------------|------------------------|-------------|
| `GET`      | `/jugadores`           | Listar jugadores ordenados por ranking |
| `GET`      | `/jugadores/{id}`       | Obtener información de un jugador |
| `POST`     | `/jugadores`           | Agregar un nuevo jugador |
| `PUT`      | `/jugadores/{id}/ranking` | Actualizar ranking de un jugador |

### **Endpoints del `PartidoController`**
| Método HTTP | Endpoint               | Descripción |
|------------|------------------------|-------------|
| `POST`     | `/partidos`            | Registrar un nuevo partido |
| `PUT`      | `/partidos/{id}/resultado` | Actualizar resultado de un partido |
| `GET`      | `/partidos/jugador/{id}` | Listar partidos de un jugador |

---

## **🧪 6. Pruebas Unitarias**
Se deben implementar **2 pruebas unitarias**:

1. **JugadorService:** Verificar que se pueda crear un jugador y que su ranking sea válido.
2. **PartidoService:** Verificar que no se pueda registrar un partido con el mismo jugador dos veces.

---

## **📊 7. Criterios de Calificación (sobre 10 puntos)**

| **Criterio** | **Puntuación** | **Descripción** |
|-------------|---------------|----------------|
| **1️⃣ Endpoint `GET /jugadores` (listar jugadores ordenados por ranking)** | **1.5 puntos** | ¿Funciona correctamente? ¿Devuelve los jugadores en orden correcto? |
| **2️⃣ Endpoint `POST /jugadores` (crear jugador)** | **1.5 puntos** | ¿Se pueden registrar jugadores? ¿Las validaciones de nombre, ranking y edad son correctas? |
| **3️⃣ Endpoint `PUT /jugadores/{id}/ranking` (actualizar ranking de un jugador)** | **1.5 puntos** | ¿Permite actualizar el ranking correctamente? |
| **4️⃣ Endpoint `POST /partidos` (registrar un partido)** | **1.5 puntos** | ¿Se pueden registrar partidos entre jugadores distintos? ¿Evita errores como partidos entre el mismo jugador? |
| **5️⃣ Endpoint `PUT /partidos/{id}/resultado` (actualizar resultado del partido)** | **1.5 puntos** | ¿Se pueden actualizar los resultados correctamente? |
| **6️⃣ Endpoint `GET /partidos/jugador/{id}` (listar partidos de un jugador)** | **1.5 puntos** | ¿Devuelve correctamente los partidos en los que ha participado un jugador? |
| **7️⃣ Pruebas unitarias correctas** | **1 punto** | ¿Se han implementado correctamente las dos pruebas requeridas? |

✅ **Corrección:** Se validarán los endpoints en Swagger y la captura de pantalla. 

**Consideraciones:**
- Si un endpoint no compila o da errores graves, su puntuación se reduce a 0 puntos.
- Si un endpoint está presente pero con errores menores, se otorga hasta el 50% de la puntuación.
- Si usa validaciones adecuadas (por ejemplo, no permitir nombres vacíos o números negativos en ranking), obtiene la puntuación completa.
---
