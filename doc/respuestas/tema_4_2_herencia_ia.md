<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

La herencia es un mecanismo de la orientación a objetos que permite que una clase (subclase) herede atributos y métodos de otra clase (superclase). La relación "A es-un B" indica una dependencia de tipos donde A es un caso especial o más específico de B. Por ejemplo, un Artillero es-un Soldado, y un Zapador es-un Soldado. Esto establece una jerarquía de clases donde los subtipos comparten características comunes de su supertipo.

La primera implicación principal es la **compatibilidad de tipos**: un objeto de tipo Artillero puede ser tratado como un Soldado porque un Artillero "es-un" Soldado. Esto permite que referencias de tipo Soldado apunten a objetos reales de tipo Artillero o Zapador, facilitando la generalidad en el código.

La segunda implicación es la **herencia de estado y comportamiento**: las subclases heredan todos los atributos (estado) y métodos (comportamiento) de la superclase. En el ejemplo, tanto Artillero como Zapador heredan el atributo privado `nombre` y el método `saludar()` de Soldado. Además, cada subclase puede tener sus propios atributos (numCohetes, numMinas) y métodos específicos (disparar(), ponerMinas()).

```java
// Clase base: Soldado
class Soldado {
    private String nombre;
    
    public Soldado(String nombre) {
        this.nombre = nombre;
    }
    
    public void saludar() {
        System.out.println("Soy " + nombre);
    }
}

// Subclase: Artillero
class Artillero extends Soldado {
    private int numCohetes;
    
    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }
    
    public int getNumCohetes() {
        return numCohetes;
    }
    
    public void disparar() {
        System.out.println("¡Disparando cohete!");
    }
}

// Subclase: Zapador
class Zapador extends Soldado {
    private int numMinas;
    
    public Zapador(String nombre, int numMinas) {
        super(nombre);
        this.numMinas = numMinas;
    }
    
    public int getNumMinas() {
        return numMinas;
    }
    
    public void ponerMinas() {
        System.out.println("Poniendo minas...");
    }
}

// Uso de compatibilidad de tipos
public class Principal {
    public static void main(String[] args) {
        // Array de Soldado - puede contener objetos de cualquier subtipo
        Soldado[] ejercito = new Soldado[3];
        ejercito[0] = new Soldado("Carlos");
        ejercito[1] = new Artillero("Juan", 5);
        ejercito[2] = new Zapador("María", 10);
        
        // Todos saludan - polimorfismo
        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}
```

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 


Al crear una instancia de una subclase en Java, se ejecutan varios constructores en cadena. Primero se ejecuta el constructor de la superclase y después el de la subclase. Por ejemplo, al construir un `Artillero`, antes de inicializar sus propios atributos (como el número de cohetes), se inicializa la parte heredada de `Soldado`.

Dentro del constructor, `super(...)` sirve para invocar explícitamente un constructor de la superclase. En este caso, `super(nombre)` transfiere el valor a `Soldado` para que inicialice su estado interno (por ejemplo, el atributo privado `nombre`). De esta forma, cada clase se responsabiliza de inicializar su propia parte del objeto.

Si la superclase no tiene visible un constructor sin parámetros, entonces sí se debe llamar a `super` de forma explícita con una firma válida. Si no se hace, el compilador intentará insertar `super()` automáticamente y fallará. Por tanto, en ese escenario la llamada a `super(...)` no es opcional.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }
}

class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre); // Primero inicializa la parte Soldado
        this.numCohetes = numCohetes;
    }
}
```

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

Sí, los atributos privados de la superclase forman parte de la instancia completa de la subclase en memoria. Cuando se crea un `Artillero`, el objeto contiene tanto la parte heredada de `Soldado` (incluyendo `nombre`) como la parte propia de `Artillero` (por ejemplo, `numCohetes`). En otras palabras, la memoria del objeto incluye el estado definido en toda la cadena de herencia.

Sin embargo, que esos datos existan en memoria no implica acceso directo desde el código de la subclase. En Java, `private` restringe el acceso al interior de la clase que declara el atributo. Por tanto, `Artillero` no puede leer ni modificar directamente `nombre`, aunque ese valor exista dentro del objeto. Para usar ese dato desde subclases, se necesita un método público/protegido en la superclase (por ejemplo, un getter).

Este comportamiento mantiene la encapsulación: la superclase controla cómo se consulta o modifica su estado interno. Así se evita que una subclase rompa invariantes de la clase base manipulando directamente campos sensibles.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}

class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }

    public void informar() {
        // System.out.println(nombre); // Error: nombre es privado en Soldado
        System.out.println("Artillero: " + getNombre() + ", cohetes: " + numCohetes);
    }
}
```

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

La compatibilidad de tipos en herencia mejora la extensibilidad porque permite programar contra el tipo general (`Soldado`) y no contra cada subtipo concreto. De esta manera, el código cliente queda desacoplado de las clases específicas y acepta nuevas variantes sin requerir cambios estructurales.

En la práctica, esto significa que se puede añadir un nuevo subtipo, por ejemplo `Francotirador`, y seguir usando exactamente el mismo bucle que recorre un array de `Soldado`. El bloque que solicita el saludo no necesita conocer qué tipos concretos existen; solo invoca el comportamiento común heredado (`saludar()`).

Este enfoque reduce mantenimiento y riesgo de regresiones: al crecer el sistema, se agregan clases nuevas sin tocar lógica ya probada. Es una ventaja directa del principio "A es-un B" aplicado con polimorfismo.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy " + nombre);
    }
}

class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
}

class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
}

// Nuevo tipo añadido después
class Francotirador extends Soldado {
    public Francotirador(String nombre) {
        super(nombre);
    }
}

public class Principal {
    public static void main(String[] args) {
        Soldado[] ejercito = {
            new Artillero("Ana"),
            new Zapador("Luis"),
            new Francotirador("Elena") // Nuevo subtipo
        };

        // Este código no cambia al añadir subtipos
        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}
```


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

Sí, en Java una referencia del supertipo puede apuntar a un objeto real de un subtipo. Por ejemplo, una variable `Soldado` puede referenciar un `Artillero` o un `Zapador`. A esto se le llama **upcasting** y ocurre de forma implícita y segura porque todo subtipo "es-un" supertipo.

Con esa referencia de supertipo solo se pueden invocar directamente los métodos visibles en el tipo `Soldado`. Si el objeto real tiene métodos específicos de `Artillero` (como `getNumCohetes()`), no se pueden usar de forma directa desde una referencia `Soldado`. Para ello se necesita un **downcasting**, es decir, convertir explícitamente de `Soldado` a `Artillero`.

`instanceof` permite verificar en tiempo de ejecución el tipo real del objeto antes de hacer downcasting. Esto evita errores de conversión (`ClassCastException`) y permite ejecutar lógica específica según el subtipo concreto.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }

    public int getNumCohetes() {
        return numCohetes;
    }
}

class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
}

public class Principal {
    public static void main(String[] args) {
        Soldado[] ejercito = {
            new Artillero("Juan", 6),
            new Zapador("Marta"),
            new Artillero("Luis", 3)
        };

        for (Soldado s : ejercito) {
            s.saludar(); // método común del supertipo

            if (s instanceof Artillero) {
                Artillero a = (Artillero) s; // downcasting seguro
                System.out.println("Cohetes disponibles: " + a.getNumCohetes());
            }
        }
    }
}
```


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

El acceso **protegido** (`protected`) en Java permite que un atributo o método sea accesible desde la propia clase, desde sus subclases y desde clases del mismo paquete. Es un nivel intermedio entre `private` y `public`: no expone el miembro a todo el mundo, pero sí lo abre parcialmente para facilitar herencia.

En términos de encapsulación, usar `protected` debe hacerse con criterio. Si un dato se marca como protegido, las subclases pueden usarlo directamente, lo que simplifica ciertos casos, pero también aumenta el acoplamiento entre la clase base y sus derivadas. Por eso suele recomendarse para información que realmente se necesita extender en subclases.

En el ejemplo, `Soldado` declara `nombre` como `protected`, y `Zapador` lo utiliza en su método de poner minas. Esto permite construir un mensaje específico sin necesidad de getter, aunque a cambio se otorga acceso directo al campo heredado.

```java
class Soldado {
    protected String nombre; // Accesible desde subclases

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy " + nombre);
    }
}

class Zapador extends Soldado {
    private int numMinas;

    public Zapador(String nombre, int numMinas) {
        super(nombre);
        this.numMinas = numMinas;
    }

    public void ponerMinas() {
        System.out.println(nombre + " está colocando minas. Total: " + numMinas);
    }
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

En muchos lenguajes orientados a objetos existe una clase raíz común para todos los objetos, pero no es una regla universal en todos los lenguajes ni en todos los paradigmas. Depende del diseño del lenguaje: algunos imponen una jerarquía única, y otros combinan modelos distintos o permiten tipos que no participan en una jerarquía de objetos convencional.

En Java sí existe una clase base común: `Object`. Toda clase que se define en Java hereda directa o indirectamente de `Object`, incluso si no se escribe `extends Object`. Esto permite que cualquier instancia comparta un conjunto mínimo de métodos heredados, como `toString()`, `equals()` o `hashCode()`.

Esta decisión facilita el polimorfismo general y la interoperabilidad entre APIs: una referencia de tipo `Object` puede apuntar a cualquier objeto de clase. Sin embargo, trabajar solo con `Object` pierde información de tipo específico y puede requerir conversiones cuando se necesitan comportamientos concretos.

```java
class Soldado {
}

class Artillero extends Soldado {
}

public class Principal {
    public static void main(String[] args) {
        Object o1 = new Soldado();
        Object o2 = new Artillero();

        System.out.println(o1.toString());
        System.out.println(o2.getClass().getSimpleName());
    }
}
```

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La **herencia múltiple** consiste en que una clase herede de más de una superclase al mismo tiempo. Esto puede resultar útil para combinar comportamientos, pero también introduce conflictos cuando dos superclases definen métodos o estados con el mismo nombre (problema clásico del "diamante").

En Java no existe herencia múltiple de clases: una clase solo puede extender de una única clase (`extends`). Esta restricción simplifica el modelo de objetos y evita ambigüedades en la resolución de miembros heredados.

Sin embargo, Java sí permite implementar múltiples interfaces (`implements`), lo que ofrece una forma controlada de combinar contratos de comportamiento sin heredar estado de varias clases. Desde Java 8, las interfaces pueden tener métodos `default`, y cuando hay conflicto Java obliga a resolverlo explícitamente en la clase implementadora.

```java
class Soldado {
    public void saludar() {
        System.out.println("Saludos");
    }
}

interface Comunicador {
    void transmitir();
}

interface Navegador {
    void orientarse();
}

// Correcto: una sola clase base + múltiples interfaces
class Artillero extends Soldado implements Comunicador, Navegador {
    @Override
    public void transmitir() {
        System.out.println("Transmitiendo coordenadas");
    }

    @Override
    public void orientarse() {
        System.out.println("Orientando posición");
    }
}

// No permitido en Java:
// class X extends A, B { }
```


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta
