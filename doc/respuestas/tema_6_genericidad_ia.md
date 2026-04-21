# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

Una forma clásica en C consiste en usar un array de punteros genéricos `void*`. De ese modo, cada posición puede apuntar a un dato de tipo distinto, siempre que se gestione externamente qué tipo real hay en cada índice. Esto permite reutilizar una misma estructura de datos (por ejemplo, una lista dinámica basada en array) para enteros, `double`, estructuras propias, etc.

En Java, el equivalente histórico sería un array de `Object`, ya que cualquier referencia de clase hereda de `Object`. Así, un mismo contenedor puede almacenar `String`, `Integer`, `Double` u otros objetos. Esta solución es flexible, pero desplaza parte de la seguridad de tipos al programador, que deberá convertir después al tipo correcto.

```c
typedef struct {
    void** datos;
    int capacidad;
    int n;
} VectorGenerico;
```

```java
Object[] datos = new Object[10];
datos[0] = "hola";
datos[1] = 42;      // autoboxing a Integer
datos[2] = 3.14;    // autoboxing a Double
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica consiste en escribir algoritmos y estructuras de datos desacoplados de un tipo concreto, para que el mismo código pueda reutilizarse con muchos tipos distintos. La idea principal es parametrizar el tipo, de forma que la lógica sea una sola y el tipo se especifique al usarla.

El ejemplo con `void*` o `Object` puede considerarse una forma básica de genericidad, porque persigue reutilización sobre tipos variados. Sin embargo, es una genericidad con chequeo débil en compilación, ya que no impide por sí sola combinaciones incorrectas de tipos. Por eso, en lenguajes modernos se prefieren mecanismos de parámetros de tipo (`<T>`) o plantillas, que preservan mejor la seguridad estática.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El problema principal es que se pierde información de tipo en la interfaz de la estructura de datos. En C con `void*`, el compilador no puede verificar qué tipo real se está insertando o recuperando, por lo que un casteo incorrecto compila y falla en ejecución con comportamiento indefinido. Además, suele obligar a gestionar manualmente memoria y ciclo de vida, aumentando el riesgo de errores.

En Java con `Object`, aunque no hay comportamiento indefinido como en C, también se requiere casting al recuperar elementos. Si se hace una conversión incorrecta, aparece una `ClassCastException` en tiempo de ejecución. En ambos casos, se retrasa el error: en lugar de detectarse al compilar, se descubre al ejecutar, lo que dificulta depuración y mantenimiento.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son marcadores formales que se declaran en clases, interfaces o métodos para representar tipos aún no concretados. Se suelen escribir como `<T>`, `<K, V>`, etc., y se reemplazan por tipos reales en el momento de uso. Así, la estructura sigue siendo reutilizable, pero el compilador sí conoce el tipo concreto en cada instancia.

El valor práctico es que se logra genericidad con chequeo estático fuerte. Por ejemplo, una `List<String>` solo admite `String`, y al leer de ella no se necesita downcasting. De esta manera se evita gran parte de los errores que sí aparecen con `Object`, manteniendo la flexibilidad de un diseño reutilizable.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java, al usar `List<String>`, el compilador impide añadir elementos que no sean cadenas y garantiza que cada lectura produce directamente un `String`. Esto elimina conversiones manuales al recorrer la colección y evita errores típicos de casteo. El mecanismo es cómodo para trabajar con APIs de colecciones de forma segura.

En C++, `std::vector<std::string>` ofrece una idea equivalente: el contenedor queda especializado para cadenas y cada elemento obtenido en iteración tiene tipo `std::string`. También en este caso el control de tipos sucede en compilación, por lo que no se aceptan elementos incompatibles.

```java
import java.util.*;

List<String> nombres = new ArrayList<>();
nombres.add("Ana");
nombres.add("Luis");

for (String s : nombres) {
    System.out.println(s.toUpperCase()); // s es String, sin cast
}
```

```cpp
#include <iostream>
#include <string>
#include <vector>

int main() {
    std::vector<std::string> nombres;
    nombres.push_back("Ana");
    nombres.push_back("Luis");

    for (const std::string& s : nombres) {
        std::cout << s << " (longitud=" << s.size() << ")\n";
    }
}
```


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Al instanciar una clase genérica, el compilador verifica que los tipos concretos usados respetan las restricciones declaradas y que las operaciones realizadas con esos tipos son válidas. Esto permite detectar incompatibilidades antes de ejecutar. Sin embargo, Java y C++ no implementan internamente la genericidad de la misma forma.

En Java se aplica `type erasure`: durante compilación se reemplazan parámetros de tipo por su cota (o por `Object` si no hay cota) y se insertan casts donde haga falta. Por eso en runtime no existe, por ejemplo, una clase distinta para `List<String>` y `List<Integer>`. En C++, en cambio, los templates se instancian generando código específico por combinación de tipos (instanciación de plantillas), lo que mantiene tipado fuerte sin borrado y posibilita optimizaciones muy potentes, a costa de mayor coste de compilación y posible crecimiento de binario.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

Una clase `Par<A, B>` permite empaquetar dos resultados relacionados sin perder precisión de tipos. Es útil cuando una función necesita devolver dos valores distintos, evitando crear clases ad hoc para casos sencillos. El constructor inicializa ambos campos y los getters exponen cada componente con su tipo exacto.

Para el ejemplo estadístico, puede devolverse un `Par<Double, Double>` donde el primer valor sea la media y el segundo la desviación típica. Así se expresa con claridad el contrato del método y se evita usar arrays posicionales que suelen ser menos legibles.

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() { return primero; }
    public B getSegundo() { return segundo; }
}

class Estadistica {
    public static Par<Double, Double> mediaYDesviacion(double[] xs) {
        double suma = 0.0;
        for (double x : xs) suma += x;
        double media = suma / xs.length;

        double sumaCuadrados = 0.0;
        for (double x : xs) {
            double d = x - media;
            sumaCuadrados += d * d;
        }
        double desviacion = Math.sqrt(sumaCuadrados / xs.length);
        return new Par<>(media, desviacion);
    }
}
```


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Un método con `Object` acepta cualquier par de objetos, incluso de tipos distintos, y devuelve `Object`. Eso obliga a hacer downcasting al resultado y deja abierta la puerta a errores de conversión en ejecución. Además, no impone que ambos argumentos sean del mismo tipo, por lo que el contrato semántico queda débil.

Con un método genérico `<T>`, el compilador deduce `T` y exige que ambos argumentos pertenezcan al mismo tipo compatible. El valor devuelto también queda tipado como `T`, eliminando cast explícito y reforzando la seguridad estática. La diferencia clave es que las restricciones se validan antes de ejecutar.

```java
import java.util.concurrent.ThreadLocalRandom;

// Versión débil con Object
static Object seleccionaUnoObject(Object a, Object b) {
    return ThreadLocalRandom.current().nextBoolean() ? a : b;
}

// Versión genérica fuerte
static <T> T seleccionaUno(T a, T b) {
    return ThreadLocalRandom.current().nextBoolean() ? a : b;
}

// Uso
String s = seleccionaUno("hola", "adios"); // sin cast
// String x = (String) seleccionaUnoObject("hola", 3); // compila, pero es peligroso
```


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí, se pueden imponer restricciones mediante cotas superiores, por ejemplo `<T extends Number>`. Con eso se garantiza que `T` tiene al menos la interfaz de `Number` y, por tanto, permite convertir a `double` para cálculos como distancias. Esta técnica combina flexibilidad con reglas de tipado más estrictas.

Si se modela directamente con `Number`, cualquier subtipo numérico sirve, pero se pierde información específica del tipo en la API. Con `Punto<T extends Number>`, en cambio, se sabe si se está trabajando con `Integer`, `Double`, etc. Tras compilar, por `type erasure`, `T` se borra a su cota `Number`, de modo que el tipo efectivo en bytecode queda como `Number` (con casts insertados cuando proceda).

```java
class PuntoNumber {
    private final Number x;
    private final Number y;

    public PuntoNumber(Number x, Number y) { this.x = x; this.y = y; }
    public Number getX() { return x; }
    public Number getY() { return y; }

    public double calcularDistanciaA(PuntoNumber p) {
        double dx = x.doubleValue() - p.x.doubleValue();
        double dy = y.doubleValue() - p.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

class Punto<T extends Number> {
    private final T x;
    private final T y;

    public Punto(T x, T y) { this.x = x; this.y = y; }
    public T getX() { return x; }
    public T getY() { return y; }

    public double calcularDistanciaA(Punto<T> p) {
        double dx = x.doubleValue() - p.x.doubleValue();
        double dy = y.doubleValue() - p.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

Con la versión basada en `Number`, sí se permite mezclar tipos en el mismo objeto, por ejemplo `x` como `Integer` e `y` como `Double`, porque ambos campos aceptan cualquier subtipo de `Number`. Esa flexibilidad puede ser útil, pero también relaja el contrato y puede ocultar inconsistencias de modelado si se esperaba homogeneidad de coordenadas.

Con la versión genérica `Punto<T extends Number>`, si se define un único parámetro `T` para ambas coordenadas, se fuerza que ambas sean del mismo tipo concreto en cada instancia (`Punto<Integer>`, `Punto<Double>`, etc.). En la solución sin generics, `getX` devuelve `Number`; en la solución genérica, `getX` devuelve `T`, que para el cliente se traduce en el tipo concreto con el que se instanció el punto.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Para evitar `instanceof` y cast, puede aplicarse un patrón de auto-referencia genérica (a veces llamado CRTP en POO): la interfaz se declara como `Punto<T extends Punto<T>>` y su método recibe `T`. Así, cada implementación fija su propio tipo y el compilador impide comparar puntos de dimensiones distintas en la misma llamada.

Con este enfoque, `Punto2D` implementa `Punto<Punto2D>` y `Punto3D` implementa `Punto<Punto3D>`. La distancia queda tipada de forma coherente y cualquier intento de mezclar `Punto2D` con `Punto3D` produce error de compilación en lugar de excepción en runtime.

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}

public class Punto2D implements Punto<Punto2D> {
    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

No, `List<String>` no es subtipo de `List<Object>` en Java: los genéricos son invariantes por defecto. Si se permitiese esa conversión, podría insertarse un `Integer` en una lista que realmente es de `String`, rompiendo la seguridad del contenedor. Por eso el compilador la prohíbe.

En cambio, `String[]` sí es subtipo de `Object[]` porque los arrays en Java son covariantes. Esta decisión histórica permite cierta flexibilidad, pero abre una comprobación en runtime: si en un `Object[]` que realmente referencia un `String[]` se intenta guardar un `Integer`, aparece `ArrayStoreException`. En resumen: covariante significa que al variar `S <: T` también se cumple `F<S> <: F<T>`; contravariante invierte la relación (`F<T> <: F<S>`); invariante implica que no hay subtipo entre `F<S>` y `F<T>` aunque `S <: T`.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard `?` representa un tipo desconocido, útil cuando interesa expresar relaciones de subtipado sin fijar un tipo exacto. Con `? extends T` se obtiene una vista de solo lectura efectiva para productores de `T`: se puede leer como `T`, pero no añadir nuevos elementos (salvo `null`) porque se desconoce el subtipo concreto. Con `? super T` se modelan consumidores de `T`: se pueden insertar objetos `T` (o subtipos), pero al leer solo se garantiza `Object`.

La regla práctica es PECS (Producer Extends, Consumer Super): si la estructura produce datos para el algoritmo, usar `extends`; si consume datos que el algoritmo inserta, usar `super`. En suma de números interesa leer elementos, por eso `? extends Number`; para añadir enteros a una lista compatible, interesa `? super Integer`.

```java
import java.util.*;

// (i) Productor de Number -> extends
static double sumar(List<? extends Number> xs) {
    double total = 0.0;
    for (Number n : xs) total += n.doubleValue();
    return total;
}

// (ii) Consumidor de Integer -> super
static void agregarEnteros(List<? super Integer> destino) {
    destino.add(10);
    destino.add(20);
    destino.add(30);
}
```
