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


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta
