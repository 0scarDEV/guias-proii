<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

En C, la composición puede representarse anidando `struct`: una estructura mayor contiene otras estructuras como campos. En este caso, un `Punto` puede tener dos coordenadas (`x`, `y`) y una `Linea` puede estar formada por dos `Punto` (`inicio` y `fin`). Esta idea expresa directamente la relacion "Linea tiene dos Punto".

Para calcular distancias, se aplica la distancia euclidea entre dos puntos: `sqrt((x2 - x1)^2 + (y2 - y1)^2)`. A partir de ahi, la longitud de una linea se obtiene reutilizando la misma funcion de distancia, pero pasandole los dos puntos que contiene la `Linea`. Asi se evita duplicar logica y se mantiene el codigo mas claro.

```c
#include <stdio.h>
#include <math.h>

typedef struct {
	double x;
	double y;
} Punto;

typedef struct {
	Punto inicio;
	Punto fin;
} Linea;

double distanciaPuntos(Punto a, Punto b) {
	double dx = b.x - a.x;
	double dy = b.y - a.y;
	return sqrt(dx * dx + dy * dy);
}

double longitudLinea(Linea l) {
	return distanciaPuntos(l.inicio, l.fin);
}

int main(void) {
	Punto p1 = {0.0, 0.0};
	Punto p2 = {3.0, 4.0};
	Linea l = {p1, p2};

	printf("Distancia entre puntos: %.2f\n", distanciaPuntos(p1, p2));
	printf("Longitud de la linea: %.2f\n", longitudLinea(l));
	return 0;
}
```

En el ejemplo, se observa una composición simple y comun en C: estructuras pequenas se combinan para modelar estructuras mas ricas del dominio. Aunque C no tenga orientacion a objetos, el patron "tiene-un" ya aparece de forma natural mediante campos estructurados.

## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

En Java, la misma idea "Linea tiene dos Punto" se modela con composición entre clases. La diferencia respecto a C es que se puede proteger mejor el estado interno con encapsulacion: los atributos se declaran `private` y se exponen solo operaciones controladas. De ese modo, no se permite modificar libremente las coordenadas ni los extremos de la linea.

Para asegurar inmutabilidad, `Punto` y `Linea` pueden declararse como clases `final`, con atributos `private final`, sin setters. Asi, tras construir un objeto, su estado no cambia. El metodo `distanciaA` queda dentro de `Punto`, porque es una operacion natural del propio objeto, y `longitud` en `Linea` reutiliza `distanciaA` para evitar duplicacion.

```java
public final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double getX() {return x;}
	public double getY() {return y;}

	public double distanciaA(Punto otro) {
		double dx = otro.x - this.x;
		double dy = otro.y - this.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

public final class Linea {
	private final Punto inicio;
	private final Punto fin;

	public Linea(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser null");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public Punto getInicio() {return inicio;}
	public Punto getFin() {return fin;}
	public double longitud() {return inicio.distanciaA(fin);}
}

class Main {
	public static void main(String[] args) {
		Punto p1 = new Punto(0, 0);
		Punto p2 = new Punto(3, 4);
		Linea linea = new Linea(p1, p2);

		System.out.println("Distancia entre puntos: " + p1.distanciaA(p2));
		System.out.println("Longitud de la linea: " + linea.longitud());
	}
}
```

Con esta estructura, la composición queda explicita y segura: `Linea` depende de dos `Punto`, pero una vez creada no se puede alterar que puntos la forman. Esto mejora la robustez frente al enfoque en C, porque se mantiene la invariante del objeto desde su construccion.

## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

La multiplicidad indica cuantas instancias de una clase pueden o deben estar relacionadas con una instancia de otra clase. Es una forma de expresar restricciones estructurales del modelo: por ejemplo, "exactamente una", "cero o una", "muchas" o "al menos una". En composición, esta informacion ayuda a definir claramente la forma y los limites del objeto contenedor.

En el ejemplo de `Linea` y `Punto`, de `Linea` a `Punto` la multiplicidad es `2` (o `2..2`), porque cada linea esta formada exactamente por dos puntos: inicio y fin. No puede haber una linea con uno solo ni con tres puntos en ese modelo.

En sentido contrario, de `Punto` a `Linea`, la multiplicidad suele considerarse `0..*` en el dominio general: un punto puede no pertenecer a ninguna linea o pertenecer a varias lineas distintas.

## 4. ¿Qué significa composición **fuerte** y composición **débil**? 

La diferencia principal está en la propiedad y el ciclo de vida de los objetos;
- En una relacion fuerte, el objeto contenedor "posee" a sus partes de forma exclusiva: esas partes nacen y dejan de tener sentido con el contenedor. 
- En una relacion débil, en cambio, el contenedor solo referencia objetos que pueden existir por separado.

### ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? 
Como consecuencia;
> - En composición **fuerte** el ciclo de vida de la parte queda ligado al del todo: si desaparece el contenedor, tambien deben desaparecer sus componentes. 
> - En composición **débil** no ocurre eso: al eliminar el contenedor, las partes pueden seguir vivas y reutilizarse en otros contextos. 

Esta distincion es importante para decidir quien crea, valida y gestiona cada objeto.

### Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.
En terminologia habitual de modelado;
- La relacion débil suele llamarse **asociación** o **agregación**. 
- La relacion fuerte se denomina **composición**. 

Por eso, cuando se dice "composición" sin adjetivos, normalmente se esta aludiendo al caso fuerte.

## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

En esos casos se habla, en general, de **dependencia** y no de composición. La razon es que la clase solo "usa" temporalmente a otra para realizar una operacion, pero no la mantiene necesariamente como parte estable de su estado interno. Es una relacion de uso, no una relacion estructural de "tiene-un" persistente.

Por ejemplo, si una clase recibe un objeto por parametro, lo usa dentro del metodo y termina la llamada, existe dependencia porque el metodo depende de ese tipo para funcionar. Lo mismo ocurre si crea un objeto local con `new` o si devuelve un objeto de otro tipo: hay acoplamiento entre clases, pero no implica que una contenga a la otra como atributo propio.

Se consideraria composición cuando una clase guarda referencias a otra como atributos que forman parte de su identidad o estructura (por ejemplo, `private final Punto inicio;`). Si solo aparece en firmas de metodos, variables locales o uso puntual, el termino correcto es dependencia.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

En composición fuerte, `Linea` crea internamente sus puntos y no permite que vengan "desde fuera". Asi, los `Punto` quedan conceptualmente ligados al ciclo de vida de la `Linea`: no hay referencias externas directas a los objetos internos, y la linea controla por completo su construccion.

En composición débil, `Linea` recibe `Punto` ya existentes y los conserva como referencias. En este caso, los puntos pueden vivir independientemente de la linea y, ademas, podrian compartirse entre varias lineas. La relacion sigue siendo "tiene-un", pero sin propiedad exclusiva del ciclo de vida.

```java
final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double distanciaA(Punto otro) {
		double dx = otro.x - this.x;
		double dy = otro.y - this.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

// composición fuerte: Linea crea y controla sus puntos.
final class LineaFuerte {
	private final Punto inicio;
	private final Punto fin;

	public LineaFuerte(double x1, double y1, double x2, double y2) {
		this.inicio = new Punto(x1, y1);
		this.fin = new Punto(x2, y2);
	}

	public double longitud() {
		return inicio.distanciaA(fin);
	}
}

// composición débil (agregacion): Linea reutiliza puntos externos.
final class Lineadébil {
	private final Punto inicio;
	private final Punto fin;

	public Lineadébil(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser null");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public double longitud() {
		return inicio.distanciaA(fin);
	}
}

class Maincomposición {
	public static void main(String[] args) {
		LineaFuerte lf = new LineaFuerte(0, 0, 3, 4);

		Punto a = new Punto(0, 0);
		Punto b = new Punto(3, 4);
		Lineadébil ld = new Lineadébil(a, b);

		System.out.println("Longitud fuerte: " + lf.longitud());
		System.out.println("Longitud débil: " + ld.longitud());
	}
}
```

La diferencia clave no esta en la formula de longitud, que es la misma en ambos casos, sino en quien posee y gestiona los objetos `Punto`. Ese detalle determina si se modela composición fuerte o débil en el diseno.


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

En Java no existe destruccion manual explicita como en C++ con `delete`. En composición fuerte, se considera que las partes "mueren" con el contenedor en sentido logico: cuando el objeto contenedor deja de ser alcanzable, tambien dejan de serlo sus objetos internos (si no existen otras referencias externas).

La liberacion real de memoria no la hace `Linea` con una instruccion propia, sino el recolector de basura (Garbage Collector). El GC se ejecuta automaticamente cuando la JVM lo estima oportuno, detecta objetos inalcanzables y recupera su memoria. Por eso no se ve codigo de destruccion explicita en clases Java normales.

En consecuencia, en composición fuerte en Java la responsabilidad del disenador consiste en controlar referencias y encapsulacion para que las partes no se filtren al exterior. Si los componentes solo estan referenciados por el contenedor, al quedar inalcanzable el contenedor, los componentes tambien quedaran listos para recoleccion.


## 8. Pon un ejemplo de composición débil entre un departamento que tiene varios profesores. Implementa dos composiciónes a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

Se puede modelar una composición débil donde `Departamento` mantiene referencias a objetos `Profesor` que pueden existir independientemente. Ademas, se modela una segunda composición débil interna: `Departamento` tambien referencia a un `director`, que debe ser siempre uno de los profesores del propio departamento.

La invariante principal es: "siempre hay director y ese director pertenece a la lista de profesores". Para cumplirla, el constructor recibe un director inicial obligatorio, se inserta automaticamente en la coleccion de profesores, y no se permite eliminar al director actual sin nombrar antes otro. Tambien se lanzan excepciones ante posiciones invalidas, `null`, exceso de capacidad o cambios inconsistentes.

```java
final class Profesor {
	private final String nombre;

	public Profesor(String nombre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre del profesor no puede ser vacio");
		}
		this.nombre = nombre;
	}

	public String getNombre() {
		return nombre;
	}
}

class Departamento {
	private static final int MAX_PROFESORES = 50;

	private final Profesor[] profesores;
	private int numProfesores;
	private Profesor director;

	public Departamento(Profesor directorInicial) {
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}
		this.profesores = new Profesor[MAX_PROFESORES];
		this.profesores[0] = directorInicial;
		this.numProfesores = 1;
		this.director = directorInicial;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser null");
		}
		if (numProfesores >= MAX_PROFESORES) {
			throw new IllegalStateException("No se pueden anadir mas profesores");
		}
		if (contiene(profesor)) {
			throw new IllegalArgumentException("El profesor ya pertenece al departamento");
		}

		profesores[numProfesores] = profesor;
		numProfesores++;
	}

	public void removeProfesor(int pos) {
		validarPosicion(pos);
		Profesor aEliminar = profesores[pos];

		if (aEliminar == director) {
			throw new IllegalStateException("No se puede eliminar al director actual");
		}

		for (int i = pos; i < numProfesores - 1; i++) {
			profesores[i] = profesores[i + 1];
		}
		profesores[numProfesores - 1] = null;
		numProfesores--;
	}

	public int getNumProfesores() {
		return numProfesores;
	}

	public Profesor getProfesor(int pos) {
		validarPosicion(pos);
		return profesores[pos];
	}

	public Profesor getDirector() {
		return director;
	}

	public void cambiarDirector(Profesor nuevoDirector) {
		if (nuevoDirector == null) {
			throw new IllegalArgumentException("El director no puede ser null");
		}
		if (!contiene(nuevoDirector)) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}
		this.director = nuevoDirector;
	}

	private boolean contiene(Profesor profesor) {
		for (int i = 0; i < numProfesores; i++) {
			if (profesores[i] == profesor) {
				return true;
			}
		}
		return false;
	}

	private void validarPosicion(int pos) {
		if (pos < 0 || pos >= numProfesores) {
			throw new IndexOutOfBoundsException("Posicion invalida: " + pos);
		}
	}
}

class MainDepartamento {
	public static void main(String[] args) {
		Profesor ana = new Profesor("Ana");
		Profesor luis = new Profesor("Luis");
		Profesor marta = new Profesor("Marta");

		Departamento dep = new Departamento(ana);
		dep.addProfesor(luis);
		dep.addProfesor(marta);

		dep.cambiarDirector(marta);
		dep.removeProfesor(1); // Elimina a Luis.

		System.out.println("Director: " + dep.getDirector().getNombre());
		System.out.println("Total profesores: " + dep.getNumProfesores());
	}
}
```

Con este diseno no se rompe la encapsulacion: desde fuera no se conoce ni se manipula directamente el array interno, solo se usan metodos del dominio. Asi se mantiene el control de invariantes del departamento y se evita que codigo cliente deje el objeto en un estado invalido.


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

Al usar `List`, la implementacion se simplifica porque ya no hace falta gestionar manualmente el desplazamiento de elementos al eliminar, ni mantener un contador separado (`numProfesores`) ni comprobar limites de capacidad fija del array. La coleccion se encarga de crecer dinamicamente y de operaciones como `add`, `remove` y `size`.

El riesgo de encapsulacion aparece si se devuelve directamente la lista interna: el codigo cliente podria modificarla por fuera del control de `Departamento` y romper invariantes (por ejemplo, eliminar al director o insertar `null`). Para evitarlo, se puede devolver una vista inmodificable (`Collections.unmodifiableList`) o una copia defensiva.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

final class Profesor {
	private final String nombre;

	public Profesor(String nombre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre del profesor no puede ser vacio");
		}
		this.nombre = nombre;
	}

	public String getNombre() {
		return nombre;
	}
}

class Departamento {
	private final List<Profesor> profesores = new ArrayList<>();
	private Profesor director;

	public Departamento(Profesor directorInicial) {
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}
		profesores.add(directorInicial);
		director = directorInicial;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser null");
		}
		if (profesores.contains(profesor)) {
			throw new IllegalArgumentException("El profesor ya pertenece al departamento");
		}
		profesores.add(profesor);
	}

	public void removeProfesor(int pos) {
		Profesor aEliminar = getProfesor(pos);
		if (aEliminar == director) {
			throw new IllegalStateException("No se puede eliminar al director actual");
		}
		profesores.remove(pos);
	}

	public int getNumProfesores() {
		return profesores.size();
	}

	public Profesor getProfesor(int pos) {
		if (pos < 0 || pos >= profesores.size()) {
			throw new IndexOutOfBoundsException("Posicion invalida: " + pos);
		}
		return profesores.get(pos);
	}

	public Profesor getDirector() {
		return director;
	}

	public void cambiarDirector(Profesor nuevoDirector) {
		if (nuevoDirector == null) {
			throw new IllegalArgumentException("El director no puede ser null");
		}
		if (!profesores.contains(nuevoDirector)) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}
		director = nuevoDirector;
	}

	public List<Profesor> getProfesores() {
		return Collections.unmodifiableList(profesores);
	}
}
```

En esta version, lo que mas se ahorra es codigo de gestion interna del almacenamiento: desplazamientos, control de huecos y tamano real frente a capacidad maxima. A cambio, sigue siendo necesario mantener las validaciones de dominio e invariantes, porque eso nunca lo resuelve automaticamente la estructura de datos.


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciónes, denominadas composiciónes recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciónes recursivas.

Una composición recursiva aparece cuando una clase contiene una referencia a otra instancia de su mismo tipo. En este caso, `Persona` puede tener una `madre` que tambien es `Persona`. Esta estructura permite modelar arboles genealogicos y otras jerarquias naturales del dominio.

Para mantener inmutabilidad, se puede declarar la clase como `final`, usar atributos `private final` y omitir setters. Asi, una vez construida una persona, ni su nombre ni su referencia a la madre cambian. El valor `null` en la madre puede representar un caso base (por ejemplo, cuando no se quiere o no se puede modelar una generacion anterior).

```java
final class Persona {
	private final String nombre;
	private final Persona madre;

	public Persona(String nombre, Persona madre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede ser vacio");
		}
		this.nombre = nombre;
		this.madre = madre;
	}

	public String getNombre() {
		return nombre;
	}

	public Persona getMadre() {
		return madre;
	}
}

class MainPersona {
	public static void main(String[] args) {
		Persona abuela = new Persona("Elena", null);
		Persona madre = new Persona("Laura", abuela);
		Persona nieto = new Persona("Diego", madre);

		System.out.println("Nieto: " + nieto.getNombre());
		System.out.println("Madre: " + nieto.getMadre().getNombre());
		System.out.println("Abuela: " + nieto.getMadre().getMadre().getNombre());
	}
}
```

Como ejemplos clasicos adicionales de composición recursiva pueden citarse: estructura de carpetas (una carpeta contiene subcarpetas del mismo tipo), nodos de arbol binario (cada nodo referencia hijos que tambien son nodos) y causa de excepcion en Java (`Throwable` puede contener otro `Throwable` como causa).

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

Una relacion bidireccional significa que ambos extremos conocen el vinculo: no solo `Departamento` conoce a sus `Profesor`, sino que cada `Profesor` tambien conoce a su `Departamento`. En otras palabras, se puede navegar en las dos direcciones del modelo: de departamento a profesor y de profesor a departamento.

Para implementarlo, `Profesor` deberia incluir una referencia al `Departamento` al que pertenece, y `Departamento` seguiria manteniendo su coleccion de profesores. La parte delicada es la consistencia: cada alta, baja o cambio debe actualizar ambos lados de forma sincronizada para evitar estados incoherentes (por ejemplo, profesor en la lista de un departamento pero apuntando a otro distinto).

En este escenario, conviene centralizar las operaciones en metodos de dominio (`addProfesor`, `removeProfesor`, `trasladarProfesor`) que validen invariantes y hagan las dos actualizaciones juntas. Tambien suele evitarse exponer setters publicos libres en ambos extremos, porque facilitan romper la relacion bidireccional sin pasar por las reglas de negocio.
