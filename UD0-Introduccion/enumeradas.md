# Clases Enumeradas en Java (`enum`)

Las **clases enumeradas** o **`enum`** en Java permiten definir un conjunto fijo de constantes de manera segura y legible. Cada valor definido en un `enum` es, en realidad, una instancia de la clase, lo que permite incluso añadir métodos y atributos propios.

## Ventajas de utilizar `enum`:

- **Seguridad de tipos:** Solo se pueden asignar a una variable los valores definidos en el `enum`.
- **Legibilidad:** El código es más expresivo y fácil de entender.
- **Extensibilidad:** Se pueden agregar métodos, constructores y campos para ampliar su funcionalidad.

## Ejemplo Básico

Definición de un `enum` para representar los días de la semana:

```java
public enum Dia {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO;
}
```
Uso de una clase enumerada:
```java
public class EjemploEnum {
    public static void main(String[] args) {
        Dia hoy = Dia.LUNES;

        switch (hoy) {
            case LUNES:
                System.out.println("Hoy es lunes");
                break;
            case MARTES:
                System.out.println("Hoy es martes");
                break;
            // Añadir otros casos según se requiera...
            default:
                System.out.println("Otro día de la semana");
                break;
        }
    }
}
```
En este ejemplo, la variable __hoy__ solo puede tener uno de los valores definidos en __Dia__, lo que reduce errores y mejora la claridad del código.