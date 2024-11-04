# Anexo: Guía Completa de Git, GitHub y GitFlow para Trabajo en Equipo con Scrum

## Introducción

En el desarrollo de software moderno, **Git** y **GitHub** se han convertido en herramientas indispensables para el control de versiones y la colaboración en equipo. Combinadas con metodologías ágiles como **Scrum**, permiten gestionar proyectos de manera eficiente y organizada. Este anexo tiene como objetivo proporcionar una guía detallada sobre cómo utilizar Git, GitHub y GitFlow para facilitar el trabajo en equipo, incluyendo comandos básicos y un ejercicio práctico para aplicar los conceptos aprendidos.

---

## ¿Qué es Git?

**Git** es un sistema de control de versiones distribuido que permite a múltiples desarrolladores trabajar en el mismo proyecto sin sobrescribir los cambios de otros. Facilita el seguimiento de los cambios en el código fuente, la colaboración y la gestión de versiones.

### Características Clave de Git

- **Distribuido**: Cada desarrollador tiene una copia completa del repositorio, incluyendo el historial.
- **Ramas Ligeras**: Crear y cambiar entre ramas es rápido y sencillo.
- **Integridad**: Git garantiza la integridad del código y su historial mediante SHA-1.

---

## ¿Qué es GitHub?

**GitHub** es una plataforma basada en la nube que hospeda repositorios Git y ofrece herramientas para la colaboración, como seguimiento de problemas, revisión de código y gestión de proyectos.

### Beneficios de Usar GitHub

- **Colaboración**: Facilita el trabajo en equipo mediante Pull Requests y revisiones de código.
- **Integraciones**: Compatible con herramientas de CI/CD, seguimiento de tareas y más.
- **Comunicación**: Proporciona espacios para discutir problemas y mejoras.

---

## Comandos Básicos de Git

A continuación, se presentan los comandos esenciales que todo desarrollador debe conocer:

- **Configuración Inicial**:

  ```bash
  git config --global user.name "Tu Nombre"
  git config --global user.email "tuemail@example.com"
  ```

- **Inicializar un Repositorio**:

  ```bash
  git init
  ```

- **Clonar un Repositorio**:

  ```bash
  git clone [URL]
  ```

- **Estado del Repositorio**:

  ```bash
  git status
  ```

- **Añadir Cambios al Área de Preparación**:

  ```bash
  git add [archivo]
  git add .  # Añade todos los cambios
  ```

- **Crear un Commit**:

  ```bash
  git commit -m "Mensaje descriptivo"
  ```

- **Ver Historial de Commits**:

  ```bash
  git log
  ```

- **Enviar Cambios al Repositorio Remoto**:

  ```bash
  git push origin [rama]
  ```

- **Actualizar Repositorio Local**:

  ```bash
  git pull origin [rama]
  ```

- **Gestionar Ramas**:

  - **Crear una Nueva Rama**:

    ```bash
    git branch [nombre-de-la-rama]
    ```

  - **Cambiar de Rama**:

    ```bash
    git checkout [nombre-de-la-rama]
    ```

  - **Crear y Cambiar a una Nueva Rama**:

    ```bash
    git checkout -b [nombre-de-la-rama]
    ```

  - **Fusionar Ramas**:

    ```bash
    git merge [rama-a-fusionar]
    ```

---

## GitFlow: Modelo de Ramificación

**GitFlow** es una estrategia de ramificación que define cómo estructurar las ramas en un proyecto para facilitar el desarrollo colaborativo y la entrega continua.

### Estructura de GitFlow

1. **Ramas Principales**:

   - **main**: Contiene el código en producción.
   - **develop**: Rama de integración para el próximo lanzamiento.

2. **Ramas de Soporte**:

   - **feature/**: Para desarrollar nuevas funcionalidades.
   - **release/**: Preparación de una nueva versión.
   - **hotfix/**: Correcciones urgentes en producción.

### Flujo de Trabajo con GitFlow

- **Desarrollo de Funcionalidades**:

  - Crear una rama `feature/` desde `develop`.
  - Una vez completada, fusionarla de vuelta en `develop`.

- **Preparación de Lanzamientos**:

  - Crear una rama `release/` desde `develop`.
  - Realizar pruebas y ajustes finales.
  - Fusionar en `main` y `develop`, etiquetar la versión.

- **Correcciones Urgentes**:

  - Crear una rama `hotfix/` desde `main`.
  - Aplicar la corrección.
  - Fusionar en `main` y `develop`.

---

## Trabajo en Equipo con GitHub y Scrum

Al integrar GitHub con **Scrum**, los equipos pueden gestionar sprints, tareas y colaboraciones de manera eficiente.

### Pasos para un Trabajo Colaborativo Efectivo

1. **Planificación del Sprint**:

   - Definir las tareas a realizar en el Sprint Planing y crearla en un tablero Scrum.

2. **Asignación de Tareas**:

   - Cada desarrollador se asigna una tarea y crea una rama `feature/` correspondiente.

3. **Desarrollo Individual**:

   - Trabajar en la rama `feature/`, realizando commits frecuentes.
    ```bash
    git add .
    git commit -m "Descripción del cambio"
    git push origin feature/nombre-de-la-tarea
    ```

4. **Integración Continua**:

   - Actualizar la rama `feature/` con cambios de `develop` para evitar conflictos.

     ```bash
     git checkout develop
     git pull origin develop
     git checkout feature/nombre-de-la-tarea
     git merge develop
     ```

5. **Revisión y Validación**:

   - Crear un Pull Request hacia `develop`.
   - Realizar revisiones de código entre pares.
   - Ejecutar pruebas automatizadas si están configuradas.

6. **Fusión y Despliegue**:

   - Una vez aprobado, fusionar el Pull Request.
   - Eliminar la rama `feature/`.

7. **Retrospectiva**:

   - Al final del sprint, analizar el proceso y buscar mejoras.

---

## Ejercicio Práctico: Aplicando Git, GitHub y GitFlow

### Objetivo

Practicar el flujo de trabajo completo de GitFlow en un entorno colaborativo utilizando GitHub y aplicando conceptos de Scrum.

### Descripción del Ejercicio

Formarás parte de un equipo de desarrollo que está creando una aplicación web sencilla. Tu tarea es implementar una nueva funcionalidad: un sistema de gestión de tareas.

### Pasos a Seguir

1. **Configuración Inicial**

   - Crea una cuenta en GitHub si no la tienes.
   - El instructor proporcionará un repositorio base en GitHub.
   - Clona el repositorio base:

     ```bash
     git clone [URL del repositorio base]
     ```

   - Configura tus credenciales de Git:

     ```bash
     git config --global user.name "Tu Nombre"
     git config --global user.email "tuemail@example.com"
     ```

2. **Crear y Sincronizar la Rama `develop`**

   - Asegúrate de tener la rama `develop`:

     ```bash
     git checkout main
     git pull origin main
     git checkout -b develop
     git push -u origin develop
     ```

3. **Planificación de Tareas**

   - En el tablero de proyectos de GitHub, el equipo define las tareas necesarias para implementar el sistema de gestión de tareas.
   - Cada miembro del equipo se asigna una tarea específica.

4. **Crear una Rama de Feature**

   - Crea una nueva rama para tu tarea asignada desde `develop`:

     ```bash
     git checkout develop
     git pull origin develop
     git checkout -b feature/tu-tarea
     ```

5. **Desarrollar la Funcionalidad**

   - Implementa la funcionalidad según los requisitos de la tarea.
   - Realiza commits frecuentes con mensajes claros:

     ```bash
     git add .
     git commit -m "Implementar la creación de tareas"
     ```

6. **Mantener la Rama Actualizada**

   - Antes de finalizar, integra los últimos cambios de `develop`:

     ```bash
     git checkout develop
     git pull origin develop
     git checkout feature/tu-tarea
     git merge develop
     ```

   - Resuelve cualquier conflicto que pueda surgir.

7. **Preparar el Pull Request**

   - Empuja tu rama al repositorio remoto:

     ```bash
     git push -u origin feature/tu-tarea
     ```

   - En GitHub, crea un Pull Request desde tu rama `feature/tu-tarea` hacia `develop`.
   - Incluye una descripción detallada de los cambios y referencias a la tarea o historia de usuario.

8. **Revisión de Código**

   - Notifica a tus compañeros para que revisen tu Pull Request.
   - Participa en la revisión de los Pull Requests de otros miembros.
   - Si se requieren cambios, realízalos y actualiza el Pull Request.

9. **Fusionar el Pull Request**

   - Una vez aprobado, fusiona el Pull Request en `develop`.
   - En tu terminal, actualiza `develop`:

     ```bash
     git checkout develop
     git pull origin develop
     ```

10. **Eliminar la Rama de Feature**

    - Borra la rama remota y localmente:

      ```bash
      git push origin --delete feature/tu-tarea
      git branch -d feature/tu-tarea
      ```

11. **Preparar una Versión de Lanzamiento**

    - Cuando todas las tareas del sprint estén completadas, el equipo crea una rama `release/` desde `develop`:

      ```bash
      git checkout develop
      git checkout -b release/v1.0.0
      ```

    - Realizan pruebas integrales y solucionan cualquier problema.
    - Fusionan la rama `release/` en `main` y etiquetan la versión:

      ```bash
      git checkout main
      git merge release/v1.0.0
      git tag -a v1.0.0 -m "Lanzamiento de la versión 1.0.0"
      git push origin main --tags
      ```

    - Fusionan los cambios de vuelta en `develop`:

      ```bash
      git checkout develop
      git merge release/v1.0.0
      ```

12. **Simular un Hotfix**

    - Supongamos que se detecta un error crítico en producción.
    - Crear una rama `hotfix/` desde `main`:

      ```bash
      git checkout main
      git checkout -b hotfix/corregir-error-critico
      ```

    - Aplicar la corrección necesaria y realizar commits.
    - Fusionar en `main` y `develop`, y etiquetar la versión de hotfix:

      ```bash
      git commit -m "Corregir error crítico"
      git checkout main
      git merge hotfix/corregir-error-critico
      git tag -a v1.0.1 -m "Hotfix v1.0.1"
      git push origin main --tags
      git checkout develop
      git merge hotfix/corregir-error-critico
      git push origin develop
      ```

    - Eliminar la rama de hotfix:

      ```bash
      git branch -d hotfix/corregir-error-critico
      git push origin --delete hotfix/corregir-error-critico
      ```

13. **Retrospectiva**

    - Al final del ejercicio, el equipo se reúne para discutir:
      - ¿Qué funcionó bien en el flujo de trabajo?
      - ¿Qué desafíos enfrentaron?
      - ¿Cómo podrían mejorar en el próximo sprint?

### Resultado Esperado

Al completar este ejercicio, habrás:

- Practicado la creación y gestión de ramas según GitFlow.
- Colaborado con compañeros utilizando Pull Requests y revisiones de código.
- Experimentado la resolución de conflictos y la integración de cambios.
- Comprendido cómo Git, GitHub y GitFlow facilitan el trabajo en equipo dentro de un marco Scrum.

---

## Recursos Adicionales

- [Documentación Oficial de Git](https://git-scm.com/doc)
- [Guía de GitHub](https://guides.github.com/)
- [Explicación Detallada de GitFlow](https://nvie.com/posts/a-successful-git-branching-model/)
- [Scrum Guide](https://scrumguides.org/)

---
