# Apuntes sobre Listas, Mapas y Conjuntos en Java

## Introducción

Las colecciones en Java son estructuras de datos que permiten almacenar y manipular grupos de objetos de manera eficiente. Las colecciones más comunes son **Listas**, **Mapas** y **Conjuntos** (*Sets*). Estos apuntes te ayudarán a comprender cómo funcionan, sus principales implementaciones y cómo utilizarlos en tus programas.

---

## Listas en Java

### Definición

Una **Lista** es una colección ordenada que permite elementos duplicados. Los elementos se almacenan en secuencia y cada uno tiene una posición (índice) dentro de la lista.

### Implementaciones

- **ArrayList**: Implementa una lista utilizando un array dinámico. Es eficiente para acceso y modificación de elementos por índice.
- **LinkedList**: Implementa una lista doblemente enlazada. Es eficiente para inserciones y eliminaciones en cualquier posición.

### Operaciones Comunes

- `add(elemento)`: Agrega un elemento al final de la lista.
- `add(indice, elemento)`: Inserta un elemento en una posición específica.
- `get(indice)`: Obtiene el elemento en la posición indicada.
- `set(indice, elemento)`: Reemplaza el elemento en la posición especificada.
- `remove(indice)`: Elimina el elemento en la posición dada.
- `size()`: Retorna el número de elementos en la lista.
- `isEmpty()`: Verifica si la lista está vacía.

### Ejemplo de Uso

```java
import java.util.ArrayList;
import java.util.List;

public class EjemploLista {
    public static void main(String[] args) {
        List<String> nombres = new ArrayList<>();
        nombres.add("Ana");
        nombres.add("Luis");
        nombres.add("Carlos");

        // Acceder a elementos
        System.out.println(nombres.get(1)); // Luis

        // Recorrer la lista
        for (String nombre : nombres) {
            System.out.println(nombre);
        }
    }
}
```

---

## Mapas en Java

### Definición

Un **Mapa** es una colección que asocia claves a valores. Cada clave única se mapea a un valor correspondiente. No permite claves duplicadas, pero sí valores duplicados.

### Implementaciones

- **HashMap**: Basado en una tabla hash. No garantiza el orden de los elementos.
- **TreeMap**: Almacena los pares clave-valor en un árbol rojo-negro, ordenados por las claves.
- **LinkedHashMap**: Mantiene un orden de inserción o acceso.

### Operaciones Comunes

- `put(clave, valor)`: Asocia un valor a una clave.
- `get(clave)`: Obtiene el valor asociado a la clave.
- `remove(clave)`: Elimina la clave y su valor asociado.
- `containsKey(clave)`: Verifica si el mapa contiene la clave.
- `containsValue(valor)`: Verifica si el mapa contiene el valor.
- `keySet()`: Retorna un conjunto de todas las claves.
- `values()`: Retorna una colección de todos los valores.

### Ejemplo de Uso

```java
import java.util.HashMap;
import java.util.Map;

public class EjemploMapa {
    public static void main(String[] args) {
        Map<String, Integer> edades = new HashMap<>();
        edades.put("Ana", 25);
        edades.put("Luis", 30);
        edades.put("Carlos", 28);

        // Obtener edad de Luis
        System.out.println("Edad de Luis: " + edades.get("Luis"));

        // Recorrer el mapa
        for (Map.Entry<String, Integer> entrada : edades.entrySet()) {
            System.out.println(entrada.getKey() + " tiene " + entrada.getValue() + " años.");
        }
    }
}
```

---

## Conjuntos (*Sets*) en Java

### Definición

Un **Conjunto** es una colección que no permite elementos duplicados. Es ideal cuando se necesita garantizar la unicidad de los elementos.

### Implementaciones

- **HashSet**: Basado en una tabla hash. No garantiza el orden de los elementos.
- **TreeSet**: Almacena los elementos en un árbol rojo-negro, ordenados de manera natural o mediante un comparador.
- **LinkedHashSet**: Mantiene el orden de inserción.

### Operaciones Comunes

- `add(elemento)`: Agrega un elemento al conjunto.
- `remove(elemento)`: Elimina un elemento del conjunto.
- `contains(elemento)`: Verifica si el conjunto contiene el elemento.
- `size()`: Retorna el número de elementos en el conjunto.
- `isEmpty()`: Verifica si el conjunto está vacío.
- `clear()`: Elimina todos los elementos del conjunto.

### Ejemplo de Uso

```java
import java.util.HashSet;
import java.util.Set;

public class EjemploConjunto {
    public static void main(String[] args) {
        Set<String> colores = new HashSet<>();
        colores.add("Rojo");
        colores.add("Azul");
        colores.add("Verde");
        colores.add("Rojo"); // No se agrega porque ya existe

        // Recorrer el conjunto
        for (String color : colores) {
            System.out.println(color);
        }
    }
}
```

---

## Ejercicios Propuestos

### Ejercicio 1

**Descripción:** Crea una **ArrayList** de números enteros del 1 al 10 y muestra la suma total de estos números.

**Puntos a considerar:**

- Utilizar un bucle para sumar los elementos.
- Imprimir el resultado final.

### Ejercicio 2

**Descripción:** Utiliza un **HashMap** para almacenar nombres de países y sus capitales. Solicita al usuario un país y muestra su capital correspondiente.

**Puntos a considerar:**

- Manejar el caso en que el país no esté en el mapa.
- Utilizar `Scanner` para la entrada del usuario.

### Ejercicio 3

**Descripción:** Crea un programa que lea una frase del usuario y utilice un **HashSet** para mostrar todas las palabras únicas que contiene la frase.

**Puntos a considerar:**

- Dividir la frase en palabras.
- Convertir todas las palabras a minúsculas para evitar duplicados por mayúsculas.

### Ejercicio 4

**Descripción:** Implementa un programa que gestione una lista de tareas utilizando una **LinkedList**. Debe permitir agregar, eliminar y mostrar tareas. Las tareas deben tener una prioridad, y al mostrarlas, deben ordenarse por prioridad.

**Puntos a considerar:**

- Crear una clase `Tarea` con atributos `descripcion` y `prioridad`.
- Utilizar `Collections.sort()` con un comparador personalizado.

### Ejercicio 5

**Descripción:** Desarrolla un programa que lea un texto y cuente la frecuencia de cada palabra utilizando un **TreeMap**. Luego, muestra las palabras en orden alfabético junto con su frecuencia.

**Puntos a considerar:**

- Eliminar signos de puntuación.
- Ignorar diferencias entre mayúsculas y minúsculas.

### Ejercicio 6

**Descripción:** Crea un **HashSet** de números enteros y encuentra la intersección y unión con otro **HashSet** dado.

**Puntos a considerar:**

- Utilizar métodos `addAll()`, `retainAll()` y `removeAll()`.
- Mostrar los resultados de la unión e intersección.

### Ejercicio 7

**Descripción:** Utiliza un **LinkedHashMap** para mantener el orden de inserción de elementos. Almacena productos y sus precios. Luego, muestra los productos en el orden en que fueron agregados.

**Puntos a considerar:**

- Añadir varios productos con sus precios.
- Recorrer el mapa y mostrar los productos y precios.

### Ejercicio 8

**Descripción:** Crea una aplicación que gestione una lista de estudiantes y sus notas utilizando una **ArrayList** de objetos `Estudiante`. Calcula la nota media de la clase y muestra los estudiantes cuya nota está por encima de la media.

**Puntos a considerar:**

- Crear la clase `Estudiante` con atributos `nombre` y `nota`.
- Calcular la media recorriendo la lista.

### Ejercicio 9

**Descripción:** Implementa un programa que lea múltiples líneas de texto y utilice un **TreeSet** para almacenar y mostrar todas las palabras únicas ordenadas alfabéticamente.

**Puntos a considerar:**

- Continuar leyendo líneas hasta que el usuario indique fin.
- Utilizar el método `compareTo` para el ordenamiento natural.

### Ejercicio 10

**Descripción:** Desarrolla un sistema de gestión de inventario utilizando un **HashMap** donde las claves sean códigos de producto y los valores sean objetos `Producto` que incluyan nombre, precio y cantidad. Implementa funcionalidades para agregar, eliminar, actualizar y buscar productos.

**Puntos a considerar:**

- Crear la clase `Producto` con los atributos mencionados.
- Manejar las opciones mediante un menú interactivo.
- Asegurar que los códigos de producto sean únicos.

---

¡Estos ejercicios te ayudarán a practicar y consolidar tus conocimientos sobre listas, mapas y conjuntos en Java! Asegúrate de intentar resolverlos por tu cuenta antes de consultar soluciones.
