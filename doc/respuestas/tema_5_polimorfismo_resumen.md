# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

El objetivo del polimorfismo es permitir extender programas de forma más sencilla y segura (menos probable generar bugs o liarla). Las modificaciones vienen principalmente añadiendo nuevas clases, antes que tocando el código existente.

Mecanismos:
- Sobreescritura de métodos en clases abstractas (con métodos abstractos).
- Interfaces.

La sobreescritura de métodos permite que un método de una superclase sea modificado y cambie su comportamiento para una subclase.

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La ligadura dinámica (o enlace tardío) es el proceso por el cual la implementación concreta de un método se decide en tiempo de ejecución según el tipo real del objeto, y no solo por el tipo de la referencia. Si recorremos un array de una superclase, al ejecutar el método se llamará al de la subclase en ese momento.

Depende del lenguaje. En java es por defecto, en C++ se debe marcar con una palabra reservada (está más pensado en rendimiento). En Python también es por defecto.

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

```java
class Soldado {
	public void saludar() { System.out.println("Soldado: saludo reglamentario."); }
}
class Zapador extends Soldado {
	@Override public void saludar() {
		System.out.println("Zapador: listo para abrir paso.");
	}
}
class Artillero extends Soldado {
	@Override public void saludar() {
		System.out.println("Artillero: pieza preparada.");
	}
}
public class DemoPolimorfismo {
	static void pasarRevista(Soldado[] peloton) {
		for (Soldado s : peloton) { s.saludar(); }
	}
	public static void main(String[] args) {
		Soldado[] peloton = { 
			new Soldado(), new Zapador(),
			new Artillero(), new Zapador()
		};
		pasarRevista(peloton);
	}
}
```
Gracias a la ligadura dinámica, la llamada `s.saludar()` ejecuta en cada iteración el método correspondiente al objeto real almacenado (`Soldado`, `Zapador` o `Artillero`). 
Se añadan los soldados con subclases que se quiera, el método `pasarRevista` seguirá funcionando sin necesidad de cambios, lo que demuestra la flexibilidad que aporta el polimorfismo.

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? 
Sí.
#### Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES"
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
#### ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?
La palabra clave es `super`.

## 5. Al sobreescribir un método en Java, 
#### ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? 
En una sobreescritura real (overriding):
- La firma del método debe coincidir con la de la clase base: mismo nombre y mismos tipos de parámetros. 
- El tipo de retorno debe ser igual o más específico (retorno covariante), nunca uno incompatible. 
- No se puede reducir visibilidad (por ejemplo, de `public` a `protected`) y no se pueden declarar excepciones comprobadas más generales que las del método original.

#### ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? 
- La sobrecarga usa el mismo nombre de método pero con una lista de parámetros distinta. La elección se resuelve en compilacion.
- La sobreescritura redefine un método heredado con la misma firma. La selección ocurre en ejecución por polimorfismo.

#### ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?
La anotación `@Override` indica explícitamente que se pretende sobreescribir un método heredado. 
Es muy recomendable usarla siempre porque el compilador valida la intención y detecta errores típicos (por ejemplo, un parámetro mal escrito que en realidad crea otro método y no sobreescribe nada). 

En consecuencia, mejora la seguridad del código y facilita el mantenimiento.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? 
Sí, el polimorfismo se empieza a usar prácticamente desde el inicio en Java, aunque a veces no se perciba de forma explícita. Todos los objetos heredan de `Object`.

#### Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?
Si, ambos métodos están definidos en `Object`, y cada clase puede redefinirlos con su comportamiento propio. 
Cuando, por ejemplo, se imprime un objeto o se compara con `equals`, Java decide en tiempo de ejecución qué implementación concreta ejecutar, lo cual es exactamente el comportamiento polimórfico.

## 7. ¿Qué es una **"clase abstracta"**? 
Es un tipo especial de clase que puede tener 0 o más métodos abstractos. 

#### ¿Puedo crear instancias de una clase abstracta?
No pueden ser instanciados directamente.

#### ¿Qué es un **"método abstracto"**? 
Un método sin implementación, está declarada la firma y es **obligatorio** implementarlo en subclases pero cada una decide cómo hacerlo. Existe una excepción, si la subclase también es abstracta.

#### Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?
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

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java?
En Java, `final`:
- Aplicado a una clase impide que esa clase sea heredada. 
- Aplicado a un método impide que ese método sea sobreescrito en subclases. 

Por tanto, `final` se usa para "cerrar" comportamiento: en métodos, se garantiza que la implementación queda fija; en clases, se evita extender su diseño y modificar su contrato mediante herencia.

#### ¿Cómo se relaciona con el polimorfismo?
Su relación con el polimorfismo es que limita el polimorfismo por herencia.

#### ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?
Si, la clase String.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? 
En Java, una interfaz es mecanismo "similar" a una clase abstracta pero con una serie de diferencias:
- Una interfaz no tiene estado (atributos).
- Todos los métodos son "abstractos" (sin código). Aunque en versiones recientes de Java se permite usar la palabra clave `default` para dar código en los métodos de una interfaz.
#### ¿Una clase puede implementar más de una interfaz?
- Una clase puede implementar varias interfaces a la vez, mientras que solo puede heredar de una clase (abstracta o no).

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. 
#### Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. 
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
```
#### Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).
```java
class Linea {
	private final Punto origen;
	private final Punto destino;
	public Linea(Punto origen, Punto destino) {
		this.origen = origen;
		this.destino = destino;
	}
	public double longitud() { return origen.calcularDistanciaA(destino); }
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

## 11. ¿Qué es la **"herencia de interfaces"** en Java? 

La herencia de interfaces en Java consiste en que una interfaz puede extender otra para reutilizar y ampliar su contrato.

#### ¿Existe **"herencia múltiple de interfaces"**? 
Sí existe herencia múltiple de interfaces: una interfaz puede extender varias interfaces a la vez, y una clase puede implementar una o varias interfaces según las capacidades que deba ofrecer.

#### Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.
```java
interface Fichero {
	String leerContenido();
}
interface FicheroEscribible extends Fichero {
	void escribirContenido(String contenido);
	boolean eliminar();
}

// Obligado a implementar los métodos de ambas interfaces
class FicheroMemoria implements FicheroEscribible {
	private String contenido = "";

	@Override public String leerContenido() 
	{ return contenido; }
	@Override public void escribirContenido(String contenido) 
	{ this.contenido = contenido; }
	@Override public boolean eliminar() 
	{ this.contenido = ""; return true; }
}
```