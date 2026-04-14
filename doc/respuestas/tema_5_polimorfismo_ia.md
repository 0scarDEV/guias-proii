<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

En programación orientada a objetos, el polimorfismo es la capacidad de tratar objetos de distintas clases derivadas como si fueran del tipo base común, y que aun así cada uno responda con su propio comportamiento. En términos prácticos, permite escribir código más general y reutilizable: se programa contra la clase padre (o una interfaz) y luego cada subtipo aporta su implementación concreta. Si se compara con C sin POO, evita tener que encadenar condicionales por tipo o gestionar manualmente qué función llamar en cada caso.

La sobreescritura de métodos (overriding) es el mecanismo que hace posible ese comportamiento especializado. Consiste en que una subclase redefine un método heredado manteniendo la misma firma, para adaptar su lógica a su caso particular. Cuando se invoca ese método mediante una referencia del tipo base, se ejecuta la versión redefinida del objeto real en tiempo de ejecución, no necesariamente la versión escrita en la clase padre.

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La ligadura dinámica (o enlace tardío) es el proceso por el cual la implementación concreta de un método se decide en tiempo de ejecución según el tipo real del objeto, y no solo por el tipo de la referencia. Es decir, si una variable se declara como tipo base pero contiene un objeto de una subclase, al invocar un método sobreescrito se ejecuta la versión de esa subclase. Este mecanismo es el núcleo práctico del polimorfismo en métodos de instancia.

Su relación con el polimorfismo es directa: sin enlace tardío, una referencia de tipo general no podría comportarse de forma distinta según el objeto concreto que contiene. En Java, este comportamiento es el predeterminado para métodos de instancia no `final`, no `static` y no `private`; no hace falta marcar cada método como "virtual". En C++, en cambio, hay que indicar explícitamente que se quiere despacho dinámico con la palabra clave `virtual` (y, si se quiere exigir redefinición, con `= 0` en métodos puros).

En Python también existe comportamiento equivalente al enlace tardío, porque el lenguaje resuelve los métodos en tiempo de ejecución de forma natural y no requiere declarar clases base o métodos como virtuales. Por tanto, en Python el estilo es aún más dinámico: normalmente no se señala explícitamente el polimorfismo, simplemente se define el método esperado y se invoca. La diferencia principal frente a Java y C++ está en que Python lo hace con tipado dinámico y comprobaciones en ejecución, mientras que Java y C++ combinan esto con tipado estático en compilación.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

En el siguiente ejemplo se define una clase base `Soldado` con el método `saludar`, y dos subclases (`Zapador` y `Artillero`). La clase `Zapador` sobreescribe completamente el comportamiento del método heredado, mientras que `Artillero` también ofrece su propia versión para que se observe con claridad el efecto polimórfico.

```java
class Soldado {
	public void saludar() {
		System.out.println("Soldado: saludo reglamentario.");
	}
}

class Zapador extends Soldado {
	@Override
	public void saludar() {
		System.out.println("Zapador: listo para abrir paso.");
	}
}

class Artillero extends Soldado {
	@Override
	public void saludar() {
		System.out.println("Artillero: pieza preparada.");
	}
}

public class DemoPolimorfismo {
	public static void main(String[] args) {
		Soldado[] peloton = {
			new Soldado(),
			new Zapador(),
			new Artillero(),
			new Zapador()
		};

		for (Soldado s : peloton) {
			s.saludar();
		}
	}
}
```

Al recorrer el array `Soldado[]`, la variable de control del bucle tiene tipo `Soldado`, pero cada posición puede contener un objeto de distinto subtipo. Gracias a la ligadura dinámica, la llamada `s.saludar()` ejecuta en cada iteración el método correspondiente al objeto real almacenado (`Soldado`, `Zapador` o `Artillero`). Esa es precisamente la idea práctica de polimorfismo: tratar objetos distintos con una misma referencia general y obtener comportamientos específicos sin usar condicionales por tipo.

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

Sí, al sobreescribir un método en Java se puede invocar la implementación de la clase base y después ampliar su comportamiento. Esto resulta útil cuando se quiere reutilizar la lógica común del padre y añadir una variación propia de la subclase, en lugar de reescribir todo desde cero.

```java
class Soldado {
	public void saludar() {
		System.out.println("Soldado: saludo reglamentario.");
	}
}

class Zapador extends Soldado {
	@Override
	public void saludar() {
		super.saludar(); // Invoca el método heredado de Soldado
		System.out.println("ZAPADOR A SUS ORDENES");
	}
}
```

La palabra clave utilizada para invocar el método de la clase base es `super`. Con `super.saludar()` se ejecuta primero la versión definida en `Soldado`, y a continuación se añade el mensaje específico del `Zapador`. De esta forma se mantiene la parte común y se personaliza solo lo necesario.

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta
