# Tema 4.2. Herencia

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

En Java, una excepción personalizada *no controlada* se define heredando de `RuntimeException`. De este modo no se obliga a declararla con `throws`, pero puede propagarse en tiempo de ejecución cuando ocurre una situación anómala del dominio, como no encontrar un usuario.

Si se quiere conservar contexto del error, la excepción puede componerse con un objeto `Usuario` que identifique qué dato provocó el problema. Además, es buena práctica ofrecer un constructor que reciba la causa (`Throwable`) para no perder la excepción original cuando se está envolviendo un error de más bajo nivel.

El siguiente ejemplo muestra ambas ideas: la excepción contiene el `Usuario` problemático y ofrece dos constructores, uno simple y otro con causa encadenada.

```java
class Usuario {
    private String dni;
    private String nombre;

    public Usuario(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }

    @Override
    public String toString() {
        return "Usuario{dni='" + dni + "', nombre='" + nombre + "'}";
    }
}

class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("No se encontró el usuario: " + usuario);
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super("No se encontró el usuario: " + usuario, causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}

class RepositorioUsuarios {
    public Usuario buscarPorDni(String dni) {
        Usuario criterio = new Usuario(dni, "(desconocido)");

        // Simulación: no se encuentra
        throw new UsuarioNoEncontradoException(criterio);
    }
}
```


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

No se recomienda usar herencia solo para reutilizar código porque heredar no significa "copiar funcionalidad", sino establecer una relación semántica fuerte de tipo "A es-un B". Si esa relación conceptual no es verdadera, la jerarquía queda forzada y el modelo del dominio pierde claridad.

Además, la herencia introduce acoplamiento estructural entre superclase y subclase. Cualquier cambio en la clase base puede afectar comportamientos de clases derivadas, incluso en lugares no previstos. Eso vuelve el diseño más frágil y dificulta evolucionar el sistema con seguridad.

Cuando el objetivo principal es reutilizar comportamiento sin imponer una relación de subtipo, suele ser preferible composición (tener un objeto colaborador) o delegación. Así se reutiliza código de forma más flexible, se pueden intercambiar implementaciones y se reduce el impacto de cambios internos.

En resumen, la herencia debe elegirse por modelado del tipo y polimorfismo, no como mecanismo automático de reutilización. Si solo se quiere compartir lógica, composición suele ofrecer mejor mantenibilidad.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

Se recomienda favorecer composición porque permite construir clases a partir de colaboradores, en lugar de fijarlas a una jerarquía rígida. Con composición, cada responsabilidad se encapsula en componentes separados y la clase principal delega en ellos. Esto genera diseños más modulares y fáciles de adaptar.

También reduce acoplamiento: cambiar una implementación interna suele requerir reemplazar un objeto colaborador, sin alterar una cadena de subclases. En herencia, en cambio, las dependencias entre superclase y subclases son más fuertes, y un cambio en la base puede provocar efectos colaterales en varios niveles.

Otra ventaja es la variación de comportamiento en tiempo de ejecución. Mediante composición se pueden inyectar distintas estrategias según contexto, algo difícil si la variación está codificada en jerarquías profundas. Este enfoque se alinea con principios de diseño como responsabilidad única y abierto/cerrado.

En conclusión, la composición no elimina la utilidad de la herencia, pero suele ser una opción más segura y flexible como punto de partida. La herencia queda mejor reservada para casos donde existe una relación clara de subtipo.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

La frase "la herencia rompe la encapsulación" se refiere a que una subclase depende no solo de la interfaz pública de la superclase, sino también de detalles de implementación y de supuestos internos sobre su funcionamiento. Aunque esos detalles no estén explícitamente documentados como contrato, la subclase puede quedar atada a ellos para funcionar correctamente.

Como consecuencia, cambios internos en la superclase (por ejemplo, orden de llamadas, uso de métodos sobrescribibles o modificación de comportamiento base) pueden romper subclases existentes sin que haya cambios en la firma pública. Esto debilita el aislamiento entre módulos y aumenta el riesgo de regresiones al evolucionar la clase padre.

El problema se vuelve más visible cuando las subclases sobreescriben métodos y dependen del comportamiento previo de `super`. Si la lógica heredada cambia, la subclase puede violar invariantes o producir resultados inesperados. En ese sentido, la encapsulación queda parcialmente comprometida por el acoplamiento vertical de la jerarquía.

Por eso se recomienda diseñar herencia con contratos muy claros, jerarquías pequeñas y puntos de extensión controlados. En muchos escenarios, composición evita esta fragilidad porque la colaboración entre objetos se basa en interfaces explícitas, no en internals heredados.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

Se puede modelar el caso de dos formas válidas. Con **herencia**, se crea una superclase `Persona` con los campos comunes (`dni`, `nombre`) y luego `Estudiante` y `Trabajador` extienden de ella. Esta alternativa expresa explícitamente que ambos tipos son personas y centraliza en un punto el estado compartido.

Con **composición**, se separan los datos comunes en una clase `DatosPersonales` y cada clase (`Estudiante`, `Trabajador`) contiene una referencia a ese objeto. Esta opción favorece flexibilidad y menor acoplamiento de jerarquía, porque la relación es "tiene-un" en lugar de "es-un". Además, facilita reutilizar `DatosPersonales` en otros contextos sin forzar herencia.

En el ejemplo siguiente se muestran ambas alternativas. En la versión por composición, tal como se pide, el constructor de `Estudiante` y `Trabajador` recibe una instancia de `DatosPersonales`.

```java
// ===== Alternativa 1: Herencia =====
class Persona {
    private String dni;
    private String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}

class EstudianteH extends Persona {
    private String carrera;

    public EstudianteH(String dni, String nombre, String carrera) {
        super(dni, nombre);
        this.carrera = carrera;
    }
}

class TrabajadorH extends Persona {
    private String empresa;

    public TrabajadorH(String dni, String nombre, String empresa) {
        super(dni, nombre);
        this.empresa = empresa;
    }
}

// ===== Alternativa 2: Composición =====
class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}

class Estudiante {
    private DatosPersonales datos;
    private String carrera;

    public Estudiante(DatosPersonales datos, String carrera) {
        this.datos = datos;
        this.carrera = carrera;
    }
}

class Trabajador {
    private DatosPersonales datos;
    private String empresa;

    public Trabajador(DatosPersonales datos, String empresa) {
        this.datos = datos;
        this.empresa = empresa;
    }
}
```
