# Apuntes sobre Listas, Mapas y Conjuntos en Java

## Introducción

Las colecciones en Java son estructuras de datos que permiten almacenar y manipular grupos de objetos de manera eficiente. Las colecciones más comunes son **Listas**, **Mapas** y **Conjuntos** (*Sets*). 

---

## Listas en Java

### Definición

Una **Lista** es una colección ordenada que permite elementos duplicados. Los elementos se almacenan en secuencia y cada uno tiene una posición (índice) dentro de la lista.

### Implementaciones

- **ArrayList**: Implementa una lista utilizando un array dinámico. Es eficiente para acceso y modificación de elementos por índice.
- **LinkedList**: Implementa una lista doblemente enlazada. Es eficiente para inserciones y eliminaciones en cualquier posición.

Declaración de una Lista (ArrayList) con tipo "String"
List<String> nombreLista = new ArrayList<String>();

### Operaciones Comunes

- `add(elemento)`: Agrega un elemento al final de la lista.
- `add(indice, elemento)`: Inserta un elemento en una posición específica.
- `get(indice)`: Obtiene el elemento en la posición indicada.
- `set(indice, elemento)`: Reemplaza el elemento en la posición especificada.
- `remove(indice)`: Elimina el elemento en la posición dada.
- `size()`: Retorna el número de elementos en la lista.
- `isEmpty()`: Verifica si la lista está vacía.
- `clear()`: Elimina todos los elementos de la lista.
- `contains(elemento)`: Verifica si la lista contiene el elemento.
- `indexOf(elemento)`: Retorna el índice del primer elemento coincidente, o -1 si no lo encuentra.
- `lastIndexOf(elemento)`: Retorna el índice del último elemento coincidente, o -1 si no lo encuentra.

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

Declaración de un Map (HashMap) con clave "Integer" y valor "String"
Map<Integer, String> nombreMap = new HashMap<Integer, String>();

### Operaciones Comunes

- `put(clave, valor)`: Asocia un valor a una clave. Si la clave ya existe, sobrescribe el valor anterior.
- `get(clave)`: Devuelve el valor asociado a la clave especificada, o `null` si la clave no está presente.
- `remove(clave)`: Elimina el par clave-valor asociado a la clave.
- `containsKey(clave)`: Verifica si el mapa contiene la clave especificada.
- `containsValue(valor)`: Verifica si el mapa contiene el valor especificado.
- `keySet()`: Devuelve un conjunto (`Set`) con todas las claves del mapa.
- `values()`: Devuelve una colección con todos los valores del mapa.
- `entrySet()`: Devuelve un conjunto de pares clave-valor (`Map.Entry`).
- `size()`: Retorna el número de elementos en el mapa.
- `isEmpty()`: Verifica si el mapa está vacío.
- `clear()`: Elimina todos los elementos del mapa.

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
[Más información](https://jarroba.com/map-en-java-con-ejemplos/)

---

## Conjuntos (*Sets*) en Java

### Definición

Un **Conjunto** es una colección que no permite elementos duplicados. Es ideal cuando se necesita garantizar la unicidad de los elementos.

### Implementaciones

- **HashSet**: Basado en una tabla hash. No garantiza el orden de los elementos.
- **TreeSet**: Almacena los elementos en un árbol rojo-negro, ordenados de manera natural o mediante un comparador.
- **LinkedHashSet**: Mantiene el orden de inserción.

Declaración de un Conjunto (HashSet) con tipo "String"
Set<String> nombreSet = new HashSet<String>();

### Operaciones Comunes

- `add(elemento)`: Agrega un elemento al conjunto si no está presente. No permite duplicados.
- `remove(elemento)`: Elimina el elemento del conjunto.
- `contains(elemento)`: Verifica si el conjunto contiene el elemento.
- `size()`: Retorna el número de elementos en el conjunto.
- `isEmpty()`: Verifica si el conjunto está vacío.
- `clear()`: Elimina todos los elementos del conjunto.
- `iterator()`: Devuelve un iterador para recorrer los elementos del conjunto.


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

- Elimina todos los signos de puntuación (".",",","¡","!").
- Dividir la frase en palabras.
- Convertir todas las palabras a minúsculas para evitar duplicados por mayúsculas.
- Introducir cada palabra en el conjunto Set.

### Ejercicio 4

**Descripción:** Utiliza un **LinkedHashMap** para mantener el orden de inserción de elementos. Almacena productos y sus precios. Luego, muestra los productos en el orden en que fueron agregados.

**Puntos a considerar:**

- Añadir varios productos con sus precios Ejemplo("Manzana", 0.50).
- Recorrer el mapa con un iterador y mostrar los productos y precios.

### Ejercicio 5

**Descripción:** Crea una aplicación que gestione una lista de estudiantes y sus notas utilizando una **ArrayList** de objetos `Estudiante`. Calcula la nota media de la clase y muestra los estudiantes cuya nota está por encima de la media.

**Puntos a considerar:**

- Crear la clase `Estudiante` con atributos `nombre` y `nota`.
- Calcular la media recorriendo la lista.

### Ejercicio 6

**Descripción:** Implementa un programa que lea múltiples líneas de texto y utilice un **TreeSet** para almacenar y mostrar todas las palabras únicas ordenadas alfabéticamente.

**Puntos a considerar:**

- Continuar leyendo líneas hasta que el usuario indique fin.
- Utilizar el método `compareTo` para el ordenamiento natural.

### Ejercicio 7

**Descripción:** Desarrolla un sistema de gestión de inventario utilizando un **HashMap** donde las claves sean códigos de producto y los valores sean objetos `Producto` que incluyan nombre, precio y cantidad. Implementa funcionalidades para agregar, eliminar, actualizar y buscar productos.

**Puntos a considerar:**

- Crear la clase `Producto` con los atributos mencionados.
- Manejar las opciones mediante un menú interactivo.
- Asegurar que los códigos de producto sean únicos.

