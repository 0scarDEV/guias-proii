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

En una sobreescritura real (overriding), la firma del método debe coincidir con la de la clase base: mismo nombre y mismos tipos de parámetros. El tipo de retorno debe ser igual o más específico (retorno covariante), nunca uno incompatible. Además, no se puede reducir visibilidad (por ejemplo, de `public` a `protected`) y no se pueden declarar excepciones comprobadas más generales que las del método original.

La diferencia con la sobrecarga (overloading) es que en la sobrecarga se usa el mismo nombre de método pero con lista de parámetros distinta dentro de una misma clase (o jerarquía), y la elección se resuelve en compilación. En cambio, la sobreescritura redefine un método heredado con la misma firma y su selección efectiva ocurre en ejecución por polimorfismo. Por eso overriding y overloading comparten nombre, pero no son el mismo mecanismo.

La anotación `@Override` indica explícitamente que se pretende sobreescribir un método heredado. Es muy recomendable usarla siempre porque el compilador valida la intención y detecta errores típicos (por ejemplo, un parámetro mal escrito que en realidad crea otro método y no sobreescribe nada). En consecuencia, mejora la seguridad del código y facilita el mantenimiento.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

Sí, el polimorfismo se empieza a usar prácticamente desde el inicio en Java, aunque a veces no se perciba de forma explícita. En cuanto se sobreescribe un método heredado y luego ese método se invoca sobre una referencia general (por ejemplo, de tipo `Object` o de una clase base), se está aprovechando enlace dinámico. Es decir, la versión ejecutada depende del objeto real que hay detrás de la referencia.

Por tanto, al sobreescribir `toString()` o `equals(Object)` ya se está aplicando polimorfismo. Ambos métodos están definidos en `Object`, y cada clase puede redefinirlos con su comportamiento propio. Cuando, por ejemplo, se imprime un objeto o se compara con `equals`, Java decide en tiempo de ejecución qué implementación concreta ejecutar, lo cual es exactamente el comportamiento polimórfico.

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

Una clase abstracta es una clase pensada para servir de base común, no para crear objetos directamente. Puede contener atributos, métodos concretos y también métodos abstractos. Un método abstracto es un método declarado sin implementación, dejando a las subclases la obligación de definir su comportamiento.

No se pueden crear instancias de una clase abstracta con `new`, precisamente porque podría tener métodos sin cuerpo. En Java, la palabra clave `abstract` debe ponerse en la declaración de la clase y también en cada método abstracto. Cualquier subclase concreta que herede de esa clase tendrá que implementar esos métodos abstractos.

```java
abstract class Soldado {
	public void saluda() {
		System.out.println("Soldado: saludo reglamentario.");
	}

	public abstract void atacar();
}

class Zapador extends Soldado {
	@Override
	public void atacar() {
		System.out.println("Zapador: colocando carga de demolicion.");
	}
}

class Artillero extends Soldado {
	@Override
	public void atacar() {
		System.out.println("Artillero: fuego de apoyo.");
	}
}
```

En este diseño, `abstract` aparece en `abstract class Soldado` y en `public abstract void atacar();`. Así se garantiza una interfaz común (`atacar`) para todos los tipos de soldado, manteniendo a la vez comportamiento compartido (`saluda`) en la clase base.

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

En Java, `final` aplicado a un método impide que ese método sea sobreescrito en subclases. Aplicado a una clase, impide que esa clase sea heredada. Por tanto, `final` se usa para "cerrar" comportamiento: en métodos, se garantiza que la implementación queda fija; en clases, se evita extender su diseño y modificar su contrato mediante herencia.

Su relación con el polimorfismo es que limita el polimorfismo por herencia. Un método `final` no puede participar en sobreescritura, así que no hay variación dinámica de ese método entre subclases. Una clase `final` tampoco puede tener subtipos, por lo que no se puede construir una jerarquía basada en ella. En la API estándar existen varios ejemplos de clases `final`, como `String`, `Integer` y `Math`.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

En Java, una interfaz es un contrato de comportamiento: define qué operaciones debe ofrecer una clase, sin imponer cómo deben implementarse internamente. Dicho de forma simple, una interfaz describe capacidades (por ejemplo, "comparable", "serializable", "dibujable") y las clases que la implementan se comprometen a proporcionar esos métodos. Esto permite trabajar con referencias del tipo interfaz y aplicar polimorfismo entre clases distintas que comparten el mismo contrato.

Se parece a una clase abstracta en que ambas pueden participar en diseños polimórficos, pero no son equivalentes. Una clase abstracta modela una base común con estado y comportamiento compartido; una interfaz modela principalmente un rol o capacidad. Además, una clase en Java solo puede heredar de una clase (abstracta o no), pero sí puede implementar varias interfaces a la vez. Por tanto, la implementación múltiple de interfaces es el mecanismo estándar para combinar varios contratos en una misma clase.

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

Se puede resolver definiendo una jerarquía con una clase abstracta `Punto` que declare el método `calcularDistanciaA`. Después, cada subtipo (`Punto2D` y `Punto3D`) implementa su fórmula concreta. Para forzar compatibilidad entre dimensiones, en cada implementación se valida el tipo recibido con `instanceof` y se hace *downcasting* al tipo correcto antes de operar.

```java
abstract class Punto {
	public abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
	private final double x;
	private final double y;

	public Punto2D(double x, double y) {
		this.x = x;
		this.y = y;
	}

	@Override
	public double calcularDistanciaA(Punto otro) {
		if (!(otro instanceof Punto2D)) {
			throw new IllegalArgumentException("Se esperaba un Punto2D");
		}
		Punto2D p = (Punto2D) otro; // downcasting seguro tras instanceof
		double dx = this.x - p.x;
		double dy = this.y - p.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

class Punto3D extends Punto {
	private final double x;
	private final double y;
	private final double z;

	public Punto3D(double x, double y, double z) {
		this.x = x;
		this.y = y;
		this.z = z;
	}

	@Override
	public double calcularDistanciaA(Punto otro) {
		if (!(otro instanceof Punto3D)) {
			throw new IllegalArgumentException("Se esperaba un Punto3D");
		}
		Punto3D p = (Punto3D) otro; // downcasting seguro tras instanceof
		double dx = this.x - p.x;
		double dy = this.y - p.y;
		double dz = this.z - p.z;
		return Math.sqrt(dx * dx + dy * dy + dz * dz);
	}
}

class Linea {
	private final Punto origen;
	private final Punto destino;

	public Linea(Punto origen, Punto destino) {
		this.origen = origen;
		this.destino = destino;
	}

	public double longitud() {
		return origen.calcularDistanciaA(destino);
	}
}

public class DemoPuntos {
	public static void main(String[] args) {
		Linea l2d = new Linea(new Punto2D(1, 1), new Punto2D(4, 5));
		System.out.println("Longitud 2D = " + l2d.longitud());

		Linea l3d = new Linea(new Punto3D(0, 0, 0), new Punto3D(1, 2, 2));
		System.out.println("Longitud 3D = " + l3d.longitud());
	}
}
```

Con este diseño, `Linea` solo conoce el tipo abstracto `Punto`, por lo que trabaja de forma polimórfica y no necesita saber si son puntos 2D o 3D. La decisión de qué cálculo aplicar queda encapsulada en cada subclase de `Punto`, y la verificación con `instanceof` evita mezclar dimensiones incompatibles en tiempo de ejecución.

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

La herencia de interfaces en Java consiste en que una interfaz puede extender otra para reutilizar y ampliar su contrato. De esta forma, una interfaz hija hereda todos los métodos de la interfaz padre y puede añadir nuevos. Sí existe herencia múltiple de interfaces: una interfaz puede extender varias interfaces a la vez, y una clase puede implementar una o varias interfaces según las capacidades que deba ofrecer.

```java
interface Fichero {
	String leerContenido();
}

interface FicheroEscribible extends Fichero {
	void escribirContenido(String contenido);
	boolean eliminar();
}

class FicheroMemoria implements FicheroEscribible {
	private String contenido = "";

	@Override
	public String leerContenido() {
		return contenido;
	}

	@Override
	public void escribirContenido(String contenido) {
		this.contenido = contenido;
	}

	@Override
	public boolean eliminar() {
		this.contenido = "";
		return true;
	}
}
```

En este ejemplo, `FicheroEscribible` hereda el método de lectura desde `Fichero` y añade escritura y borrado. Cualquier clase que implemente `FicheroEscribible` queda obligada a implementar los tres métodos, lo que permite trabajar con referencias al contrato más general (`Fichero`) o al más específico (`FicheroEscribible`) según convenga.