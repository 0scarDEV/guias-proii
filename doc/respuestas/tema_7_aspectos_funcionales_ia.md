<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a función es una variable que almacena la dirección de una función, permitiendo invocar la función a través de esa variable. Se emplea para elegir dinámicamente la función a ejecutar, pasar funciones como parámetros a otros procedimientos o almacenar referencias a funciones con la misma firma (mismos tipos de parámetros y valor de retorno).

En C esto se traduce en declarar una variable cuyo tipo indica la firma de la función apuntada. A continuación se muestra un ejemplo que define una función que recibe una cadena (`const char *`) y devuelve una nueva cadena en mayúsculas. En `main` se crea una variable local `aMayusculas` que apunta a la función y se invoca mediante ese puntero. La función devuelve memoria dinámica que el llamador debe liberar con `free`.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

char* a_mayusculas(const char* s) {
	if (!s) return NULL;
	size_t n = strlen(s);
	char* out = malloc(n + 1);
	if (!out) return NULL;
	for (size_t i = 0; i < n; ++i) {
		out[i] = (char) toupper((unsigned char) s[i]);
	}
	out[n] = '\0';
	return out;
}

int main(void) {
	/* Definición del puntero a función con la misma firma */
	char* (*aMayusculas)(const char*) = a_mayusculas;

	const char* texto = "Hola Mundo";
	char* resultado = aMayusculas(texto); /* Invocación vía puntero */

	if (resultado) {
		printf("%s\n", resultado);
		free(resultado); /* Liberar la memoria devuelta por la función */
	}
	return 0;
}
```

En este ejemplo se separa la lógica de transformación de la invocación mediante el puntero `aMayusculas`. Al devolver memoria asignada dentro de la función, corresponde al llamador liberarla con `free` para evitar fugas de memoria.

## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima y concisa que se puede definir en línea donde se necesita comportamiento funcional. Se utiliza para crear bloques de código reutilizables sin declarar un método con nombre, facilitando el paso de comportamiento (funciones) como valores, la programación basada en callbacks y estilos funcionales en lenguajes imperativos.

Ejemplo en JavaScript: se define una función flecha que toma una cadena y devuelve la misma en mayúsculas; se asigna a la variable local `aMayusculas` y se invoca como cualquier otra función. Este estilo es familiar para quien haya usado funciones como punteros, pero con sintaxis más directa y sin manejar direcciones de memoria.

```javascript
// JavaScript
const aMayusculas = s => s.toUpperCase();

const texto = 'Hola Mundo';
console.log(aMayusculas(texto)); // HOLA MUNDO
```

Ejemplo en Java: empleando la interfaz funcional `Function<String, String>` se puede asignar una lambda a una variable local `aMayusculas`. La invocación se realiza con `apply`, que es la convención de `Function` en la API de Java.

```java
import java.util.function.Function;

public class Main {
	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();

		String texto = "Hola Mundo";
		System.out.println(aMayusculas.apply(texto)); // HOLA MUNDO
	}
}
```

En ambos ejemplos la función lambda permite tratar la transformación como un valor asignable a `aMayusculas`, simplificando el paso y la composición de comportamiento en tiempo de ejecución.

## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación centrado en el uso de funciones puras, inmutabilidad de datos y la composición de funciones para construir programas. Se evita, en la medida de lo posible, el uso de estado mutable y efectos colaterales; en su lugar se prefieren transformaciones de datos mediante funciones que siempre devuelven el mismo resultado para las mismas entradas.

Lenguajes como Java 8 se describen como multi-paradigma porque mantienen sus capacidades orientadas a objetos pero incorporan características propias del paradigma funcional (por ejemplo, expresiones lambda, funciones como valores e API de streams). Esto permite usar estilos imperativos/OO junto con estilos funcionales según convenga, facilitando migraciones parciales y patrones híbridos.

Decir que las funciones son "ciudadanos de primera clase" significa que las funciones pueden ser tratadas como cualquier otro valor: se pueden asignar a variables, pasarse como argumentos, devolver desde otras funciones y almacenarse en estructuras de datos. Esta propiedad habilita patrones funcionales como callbacks, composición y creación dinámica de comportamientos.

## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis básica de una función lambda en Java se escribe con parámetros, la flecha `->` y el cuerpo de la función. Para expresiones simples se puede usar una forma concisa `param -> expresion`, y para cuerpos más largos se emplea un bloque `{ ... }` con declaraciones y `return` si hace falta. Los tipos de los parámetros suelen inferirse a partir del contexto (la interfaz funcional objetivo), por lo que no es necesario declararlos explícitamente.

Por ejemplo, asignando una lambda a una variable de tipo `Function<String, String>` se puede escribir `s -> s.toUpperCase()`. Para múltiples parámetros se usan paréntesis: `(a, b) -> a + b`. Para una lambda sin parámetros se usan paréntesis vacíos: `() -> 42`. Cuando el cuerpo es un bloque que contiene varias instrucciones, debe envolverse entre llaves y usar `return` para devolver valor.

Reglas importantes: la lambda debe ser compatible con una interfaz funcional (una interfaz con un único método abstracto), puede capturar variables locales siempre que sean *effectively final*, y la referencia `this` dentro de una lambda se refiere al objeto que la contiene, no a una instancia creada por la lambda.

Ejemplo completo de sintaxis:

```java
import java.util.function.Function;
import java.util.function.BiFunction;
import java.util.function.Supplier;

public class LambdaSyntax {
	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();
		System.out.println(aMayusculas.apply("Hola Mundo")); // HOLA MUNDO

		BiFunction<Integer, Integer, Integer> sumar = (x, y) -> x + y;
		System.out.println(sumar.apply(2, 3)); // 5

		Supplier<String> saludo = () -> "Hola";
		System.out.println(saludo.get()); // Hola

		Function<String, String> ejemploBloque = s -> {
			String r = s.trim().toUpperCase();
			return r + "!";
		};
		System.out.println(ejemploBloque.apply("  hola  ")); // HOLA!
	}
}
```

Las referencias a métodos (`Clase::metodo`) ofrecen una forma más compacta cuando la lambda simplemente delega a un método existente.

## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Un método `transformar` es un ejemplo de función de orden superior: recibe un valor (la cadena) y otra función que indica cómo transformar ese valor, y luego invoca la función recibida desde su cuerpo. Este patrón favorece la separación de la lógica de control (cuándo se transforma) y la lógica de transformación (cómo se transforma), facilitando la reutilización y la composición de comportamientos.

Ejemplo en JavaScript: se define `transformar` que acepta el texto y un transformador (función) y devuelve el resultado de invocar dicho transformador. Se muestra el uso pasando la variable `aMayusculas` previamente definida.

```javascript
// JavaScript
const aMayusculas = s => s.toUpperCase();

function transformar(texto, transformador) {
	return transformador(texto);
}

const entrada = 'Hola Mundo';
console.log(transformar(entrada, aMayusculas)); // HOLA MUNDO
```

Ejemplo en Java: se define `transformar` como un método que recibe un `String` y una `Function<String,String>`; dentro se llama a `apply` sobre la función recibida. En `main` se pasa la variable `aMayusculas` (lambda) como argumento.

```java
import java.util.function.Function;

public class TransformadorEjemplo {
	public static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();
		String entrada = "Hola Mundo";
		System.out.println(transformar(entrada, aMayusculas)); // HOLA MUNDO
	}
}
```

En ambos casos `transformar` no conoce los detalles de la transformación; simplemente aplica la función que se le pasa, lo que permite reutilizar `transformar` con distintas funciones (mayúsculas, recorte, inversión, etc.) sin modificar su implementación.

## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Se puede pasar una lambda directamente en la llamada a `transformar` sin crear una variable intermedia. Esto es útil cuando la función transformadora se usa una sola vez o se desea definirla de forma concisa junto al punto de uso. A continuación se muestran ejemplos en JavaScript y Java que invocan `transformar` pasando una lambda inline que invierte la cadena.

Ejemplo en JavaScript, usando una función flecha que invierte la cadena con `split`, `reverse` y `join`.

```javascript
const transformar = (texto, transformador) => transformador(texto);

const entrada = 'Hola Mundo';
const resultado = transformar(entrada, s => s.split('').reverse().join(''));
console.log(resultado); // odnuM aloH
```

Ejemplo en Java, pasando la lambda inline que invierte la cadena mediante `StringBuilder`.

```java
import java.util.function.Function;

public class Main {
	public static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		String entrada = "Hola Mundo";
		String resultado = transformar(entrada, s -> new StringBuilder(s).reverse().toString());
		System.out.println(resultado); // odnuM aloH
	}
}
```

Definir la lambda en la llamada mejora la legibilidad cuando la transformación es simple y local, evitando declarar variables auxiliares innecesarias.

## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre (closure) es una función junto con el entorno léxico en el que fue creada: la lambda puede acceder a variables locales del ámbito donde se definió, aun cuando se invoque fuera de ese ámbito. Esto permite encapsular comportamiento que depende de datos del contexto sin necesidad de pasarlos explícitamente en cada llamada.

En Java las lambdas pueden capturar variables locales siempre que sean *effectively final* (no se reasignen después de su inicialización). La lambda retiene acceso al valor de la variable del entorno, lo que posibilita construir funciones parametrizadas en el momento de su creación.

Ejemplo en Java: se reutiliza el método `transformar` y se crea una variable local `sufijo` fuera de la lambda; la lambda concatenará ese `sufijo` a la cadena de entrada.

```java
import java.util.function.Function;

public class ClosureEjemplo {
	public static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		final String sufijo = "!!!"; // variable local capturada por la lambda (effectively final)

		Function<String, String> añadirSufijo = s -> s + sufijo;

		String entrada = "Hola Mundo";
		System.out.println(transformar(entrada, añadirSufijo)); // Hola Mundo!!!

		// También se puede pasar la lambda inline usando la misma variable del entorno:
		System.out.println(transformar("Adiós", s -> s + sufijo)); // Adiós!!!
	}
}
```

En el ejemplo la lambda accede a `sufijo` definido fuera de ella; si se intentara reasignar `sufijo` después de su inicialización, el compilador de Java rechazará el código por no ser *effectively final*.

## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Aunque a primera vista las lambdas y los punteros a función permiten invocar código de forma indirecta, difieren en varias propiedades importantes. Un puntero a función en C es simplemente una dirección a código con una firma concreta: no captura entorno, no lleva información sobre el estado y su seguridad de tipos depende del programador. Las lambdas, en cambio, suelen ser objetos (o instancias de una interfaz funcional) que pueden portar estado y comportarse como cierres, accediendo a variables del entorno donde se definieron.

Otra diferencia práctica es la ergonomía y el sistema de tipos. En C se trabaja con sintaxis explícita para declarar y usar punteros a función y el manejo de contexto (si se necesita) requiere estructuras adicionales (`struct` con función + puntero a datos). En lenguajes modernos (JavaScript, Java) la lambda se integra con el sistema de tipos y con la biblioteca estándar: en Java una lambda implementa una interfaz funcional y su invocación y compabilidad están comprobadas en tiempo de compilación, además de poder combinarse con referencias a métodos y la API de streams.

Finalmente, en tiempo de ejecución las lambdas pueden tener una representación con cierre (captura de variables, ejecución segura), y el runtime/compilador gestiona su implementación (a menudo como objetos o métodos generados). Los punteros a función son más cercanos al nivel de máquina y ofrecen menos abstracción: son más ligeros pero requieren al programador gestionar manualmente detalles como contexto y compatibilidad de tipos.

## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Se puede crear una función que devuelva otras funciones configuradas con un parámetro (el porcentaje). En Java esto se implementa retornando una `Function<Double, Double>` donde la lambda resultante aplica el porcentaje capturado al importe recibido. Esta técnica permite generar funciones de descuento parametrizadas sin repetir la lógica de cálculo.

Ejemplo en Java: la función `crearDescuento` acepta un `double porcentaje` y devuelve una `Function<Double, Double>` que aplica ese porcentaje cada vez que se invoque con una cantidad. A continuación se crean dos descuentos distintos y se aplican a una misma cantidad.

```java
import java.util.function.Function;

public class Descuentos {
	public static Function<Double, Double> crearDescuento(double porcentaje) {
		return amount -> amount * (1.0 - porcentaje / 100.0);
	}

	public static void main(String[] args) {
		Function<Double, Double> desc10 = crearDescuento(10.0); // 10% de descuento
		Function<Double, Double> desc25 = crearDescuento(25.0); // 25% de descuento

		double precio = 200.0;
		System.out.println(desc10.apply(precio)); // 180.0
		System.out.println(desc25.apply(precio)); // 150.0
	}
}
```

La closure ocurre porque la lambda devuelta captura el valor de `porcentaje` del entorno donde fue creada. Aunque `crearDescuento` haya terminado su ejecución, las funciones `desc10` y `desc25` retienen el valor de `porcentaje` con el que se crearon, permitiendo aplicarlo posteriormente. En Java la variable capturada debe ser *effectively final*, pero la lambda conserva su valor, lo que hace posible crear funciones específicas a partir de parámetros en tiempo de creación.

## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional es una interfaz que declara exactamente un único método abstracto (Single Abstract Method, SAM); ese método es la "firma" que las lambdas y referencias a métodos implementan. Las lambdas en Java no introducen nuevos tipos: se asignan a una interfaz funcional existente, y el compilador verifica la compatibilidad entre la firma de la lambda y la del método abstracto objetivo.

Requisitos y reglas principales: la interfaz debe tener un único método abstracto (métodos `default` o `static` están permitidos y no cuentan como abstractos). Los métodos heredados de `Object` (como `equals`, `hashCode` o `toString`) no se consideran al contar métodos abstractos. Es recomendable anotar la interfaz con `@FunctionalInterface` para dejar explícita la intención y que el compilador genere un error si se añaden accidentalmente más métodos abstractos.

Las interfaces funcionales pueden ser genéricas y soportan la inferencia de tipos cuando se usan con lambdas. Además, pueden contener métodos `default` y `static` para proporcionar comportamiento complementario, pero la compatibilidad de una lambda con una interfaz funcional depende únicamente de su único método abstracto.

## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Una interfaz funcional hecha a mano es simplemente una interfaz con un único método abstracto cuya firma define la forma de las lambdas que se le pueden asignar. Definiendo `Transformador` se establece explícitamente la operación que transforma un `String` en otro `String` y se permite asignar lambdas o referencias a métodos compatibles.

Ejemplo de definición de la interfaz `Transformador` y uso práctico con una lambda que convierte a mayúsculas:

```java
@FunctionalInterface
public interface Transformador {
	String transformar(String s);
}

// Uso de la interfaz
public class DemoTransformador {
	public static void main(String[] args) {
		Transformador aMayusculas = s -> s.toUpperCase();
		System.out.println(aMayusculas.transformar("Hola Mundo")); // HOLA MUNDO
	}
}
```

Anotar la interfaz con `@FunctionalInterface` no es obligatorio pero ayuda a documentar la intención y obliga al compilador a verificar que la interfaz posee exactamente un método abstracto, evitando errores accidentales.

## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

La versión genérica de `Transformador` permite expresar la transformación entre dos tipos arbitrarios, `T` y `R`, haciendo la interfaz reutilizable en muchos contextos. Esto se logra declarando la interfaz con parámetros de tipo genéricos y definiendo el método abstracto usando esos parámetros, de forma similar a `Function<T,R>` en la API estándar de Java.

A continuación se muestra la definición genérica y un ejemplo práctico donde se crea un `Transformador<Double, Integer>` que redondea un `Double` al `Integer` más cercano mediante `Math.round`.

```java
@FunctionalInterface
public interface Transformador<T, R> {
	R transformar(T t);
}

// Ejemplo de uso: transformar Double a Integer redondeando
public class DemoTransformadorGenerico {
	public static void main(String[] args) {
		Transformador<Double, Integer> redondear = d -> (int) Math.round(d);

		Double valor = 3.7;
		Integer resultado = redondear.transformar(valor);
		System.out.println(resultado); // 4
	}
}
```

La ventaja de la versión genérica es que la misma interfaz sirve para cualquier par de tipos, manteniendo la seguridad de tipos en tiempo de compilación y mejorando la reutilización sin declarar interfaces específicas para cada caso.

## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java incluye en `java.util.function` un conjunto de interfaces funcionales predefinidas que cubren los casos más comunes, de modo que no es necesario declarar nuevas interfaces para operaciones frecuentes. Estas interfaces proporcionan nomenclatura y métodos estándar (`apply`, `accept`, `get`, `test`, etc.), y muchas están parametrizadas con genéricos para mantener la seguridad de tipos.

Las principales interfaces son:

- `Function<T,R>`: representa una función que recibe `T` y devuelve `R` (método `R apply(T)`).
- `Consumer<T>`: representa una operación que consume un `T` y no devuelve nada (método `void accept(T)`).
- `Supplier<T>`: no recibe parámetros y suministra un `T` (método `T get()`).
- `Predicate<T>`: recibe `T` y devuelve `boolean` (método `boolean test(T)`), útil para filtros.
- `BiFunction<T,U,R>`: función que acepta dos argumentos y devuelve un resultado.
- `UnaryOperator<T>`: una `Function<T,T>` especializada donde entrada y salida son del mismo tipo.
- `BinaryOperator<T>`: una `BiFunction<T,T,T>` especializada para operaciones binarias donde ambos operandos y el resultado comparten tipo.

Además existen variantes primitivas para evitar autoboxing (`IntFunction`, `ToDoubleFunction`, `IntPredicate`, `LongUnaryOperator`, etc.) y combinadores útiles (por ejemplo, `Predicate.and`, `Function.compose`, `Consumer.andThen`). A continuación un ejemplo breve que muestra `Function` y `Consumer`:

```java
import java.util.function.Function;
import java.util.function.Consumer;

public class PredefinidasDemo {
	public static void main(String[] args) {
		Function<String, Integer> longitud = s -> s.length();
		Consumer<Integer> imprimir = n -> System.out.println("Longitud: " + n);

		String texto = "Hola";
		imprimir.accept(longitud.apply(texto)); // Longitud: 4
	}
}
```

Usar estas interfaces predefinidas favorece la interoperabilidad con la API de Streams y utilidades estándar, y permite aprovechar la inferencia de tipos y los métodos por defecto que ofrecen composiciones y combinadores.

## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

`List.forEach` es la versión orientada a funciones del bucle `for` para colecciones: recibe un `Consumer<? super T>` y ejecuta la operación dada sobre cada elemento de la lista. Su uso promueve un estilo declarativo donde se describe la operación a aplicar en lugar de gestionar explícitamente el índice o el iterador.

Ejemplo que recorre una lista de `Integer` y muestra un mensaje solo cuando el entero es positivo:

```java
import java.util.Arrays;
import java.util.List;

public class ForEachDemo {
	public static void main(String[] args) {
		List<Integer> nums = Arrays.asList(-2, 0, 3, 7, -1);

		nums.forEach(n -> {
			if (n > 0) {
				System.out.println("Es positivo: " + n);
			}
		});
	}
}
```

Para casos más complejos puede combinarse `forEach` con streams (`filter`, `map`, etc.) para componer el flujo de transformación y filtrado antes de la acción final.

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

La firma `forEach(Consumer<? super T> action)` usa `? super T` porque el `Consumer` es un consumidor de elementos: `forEach` proporciona elementos del tipo `T` al `Consumer`, de modo que es seguro aceptar consumidores que trabajen con supertipos de `T`. Por ejemplo, si se tiene una `List<Integer>`, un `Consumer<Number>` o `Consumer<Object>` puede procesar esos `Integer` sin problemas; restringir la firma a `Consumer<T>` haría la API menos flexible.

PECS es una mnemotecnia que resume una regla de uso de wildcards en Java: "Producer Extends, Consumer Super". Si una estructura produce valores de tipo `T` (por ejemplo, un `Supplier` o un `Iterable` que se lee), se usa `? extends T` para permitir que se devuelvan subtipos; si una estructura consume valores `T` (por ejemplo, un `Consumer` que recibe argumentos), se usa `? super T` para aceptar manejadores de supertypos. Esto evita problemas de incompatibilidad por autoboxing y permite mayor generalidad en APIs genéricas.

En el caso del método `transformar`, la función transformadora tanto consume un `T` como produce un `R`. Aplicando PECS la firma más flexible sería `Function<? super T, ? extends R>`: el parámetro de entrada puede ser un supertipo de `T` (por ejemplo `Number`) y el resultado puede ser un subtipo de `R`. Una posible firma genérica mejorada es:

```java
public static <T, R> R transformar(T input, Function<? super T, ? extends R> transformador) {
	return transformador.apply(input);
}
```

Ejemplo de uso: se puede pasar un `Function<Number,String>` como transformador al invocar `transformar(5, numberToString)`, donde `5` es un `Integer` (subtipo de `Number`) y la función acepta `Number`. Gracias a `? super T` y `? extends R` la llamada es segura y más reutilizable en distintos contextos.

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Las referencias a métodos permiten capturar y reutilizar la lógica de un método existente sin volver a escribirlo, tratándolo como un valor. En JavaScript hay que tener cuidado con el enlace del contexto (`this`); por ello, al extraer un método de instancia se suele emplear `bind` para mantener la referencia correcta al objeto. En Java se pueden obtener referencias a métodos usando la sintaxis `instance::method`, que se ajusta automáticamente a una interfaz funcional compatible.

Ejemplo en JavaScript, donde se crea una clase `Persona`, se instancia y se obtiene una referencia al método `saludar` usando `bind` para preservar `this`:

```javascript
class Persona {
	constructor(nombre) {
		this.nombre = nombre;
	}

	saludar() {
		console.log(`Hola, soy ${this.nombre}`);
	}
}

const p = new Persona('Ana');
const saludarRef = p.saludar.bind(p); // bind para mantener `this` apuntando a `p`
saludarRef(); // Hola, soy Ana
```

Ejemplo en Java: la clase `Persona` define `saludar`. En el `main` se crea una instancia y se obtiene una referencia al método de instancia `saludar` mediante `p::saludar`; la referencia se asigna a una interfaz funcional compatible, por ejemplo `Runnable`, y se invoca con `run()`.

```java
public class Persona {
		private final String nombre;

		public Persona(String nombre) {
				this.nombre = nombre;
		}

		public void saludar() {
				System.out.println("Hola, soy " + nombre);
		}

		public static void main(String[] args) {
				Persona p = new Persona("Ana");
				Runnable saludarRef = p::saludar; // referencia al método de instancia
				saludarRef.run(); // Hola, soy Ana
		}
}
```

Ambos ejemplos muestran cómo capturar un método existente en una variable y ejecutarlo más tarde, lo que facilita pasar comportamientos como parámetros o almacenarlos para uso posterior.

## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta
