# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. 
La herencia es un mecanismo de la orientación a objetos que permite que una clase (subclase) herede atributos y métodos de otra clase (superclase).

#### Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. 
1. La **compatibilidad de tipos**: un objeto de tipo Artillero puede ser tratado como un Soldado porque un Artillero "es-un" Soldado.
2. La **herencia de estado y comportamiento**: las subclases heredan todos los atributos (estado) y métodos (comportamiento) de la superclase.

#### (2) Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos.
```java
// Superclase:
class Soldado {
    private String nombre;
    public Soldado(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Soy " + nombre); }
}

// Subclases:
class Artillero extends Soldado {
    private int numCohetes;
    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }
    public int getNumCohetes() { return numCohetes; }
    public void disparar() { System.out.println("¡Disparando cohete!"); }
}

class Zapador extends Soldado {
    private int numMinas;
    public Zapador(String nombre, int numMinas) {
        super(nombre);
        this.numMinas = numMinas;
    }
    public int getNumMinas() { return numMinas; }
    public void ponerMinas() { System.out.println("Poniendo minas..."); }
}
```
#### (1) Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.
```java
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

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden?
Al crear una instancia de una subclase en Java, se ejecutan varios constructores en cadena. Primero se ejecuta el constructor (por defecto) de la superclase y después el de la subclase (en cadena).
#### ¿Qué significa `super` dentro de un constructor? 
Dentro del constructor, `super(...)` sirve para invocar explícitamente un constructor de la superclase. De esta forma, cada clase se responsabiliza de inicializar su propia parte del objeto.
#### Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 
Si la superclase no tiene visible un constructor sin parámetros, entonces sí se debe llamar a `super` de forma explícita con una firma válida. Si no se hace, el compilador intentará insertar `super()` automáticamente y fallará. Por tanto, en ese escenario la llamada a `super(...)` no es opcional.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria?

Sí, los atributos privados de la superclase forman parte de la instancia completa de la subclase en memoria. Cuando se crea un `Artillero`, el objeto contiene tanto la parte heredada de `Soldado` como la parte propia de `Artillero`..

#### En caso afirmativo ¿implica que se puedan usar desde el código de la subclase?
No implica acceso directo desde el código de la subclase. En Java, `private` restringe el acceso al interior de la clase que declara el atributo. Para usar ese dato desde subclases, se necesita un método público/protegido en la superclase (por ejemplo, un getter).

Este comportamiento mantiene la encapsulación: la superclase controla cómo se consulta o modifica su estado interno. Así se evita que una subclase rompa invariantes de la clase base manipulando directamente campos sensibles.

```java
class Soldado {
    private String nombre;
    public Soldado(String nombre) { this.nombre = nombre; }
    public String getNombre() { return nombre; }
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

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos?

La compatibilidad de tipos en herencia mejora la extensibilidad porque permite programar contra el tipo general y no contra cada subtipo concreto. El código no va ligado a las clases específicas y acepta nuevas variantes sin requerir cambios estructurales.

Este enfoque reduce mantenimiento y riesgo de regresiones: al crecer el sistema, se agregan clases nuevas sin tocar lógica ya probada.

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? 

1. **Upcasting:** Es el proceso de convertir una referencia de un subtipo a su supertipo. Esto ocurre de forma implícita y segura, ya que un objeto de un subtipo siempre es compatible con su supertipo. Por ejemplo, una variable de tipo `Soldado` puede referenciar un objeto de tipo `Artillero` o `Zapador`.

2. **Downcasting:** Es el proceso de convertir una referencia de un supertipo a un subtipo específico. Esto requiere una conversión explícita y puede ser peligroso si el objeto real no es del tipo esperado, lo que puede resultar en una `ClassCastException`.

#### ¿Qué es el `instanceof`?
`instanceof` permite verificar en tiempo de ejecución el tipo real del objeto antes de hacer downcasting. Esto evita errores de conversión (`ClassCastException`) y permite ejecutar lógica específica según el subtipo concreto.

#### Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.
```java
class Soldado {
    private String nombre;
    public Soldado(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Hola, soy " + nombre); }
}
class Artillero extends Soldado {
    private int numCohetes;
    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }
    public int getNumCohetes() { return numCohetes; }
}
class Zapador extends Soldado {
    public Zapador(String nombre) { super(nombre); }
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
            } else if (s instanceof Zapador zapador) {
                System.out.println("El nombre del zapador es: " + zapador.getNombre());
            }
        }
    }
}
```

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos?
El acceso **protegido** (`protected`) en Java permite que un atributo o método sea accesible desde la propia clase, desde sus subclases y desde clases del mismo paquete. Es un nivel intermedio entre `private` y `public`: no expone el miembro a todo el mundo, pero sí lo abre parcialmente para facilitar herencia.

#### ¿Cómo se implementa en Java?
En términos de encapsulación, usar `protected` debe hacerse con criterio. Si un dato se marca como protegido, las subclases pueden usarlo directamente, lo que simplifica ciertos casos, pero también aumenta el acoplamiento entre la clase base y sus derivadas. Por eso suele recomendarse para información que realmente se necesita extender en subclases.

#### Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.
En el ejemplo, `Soldado` declara `nombre` como `protected`, y `Zapador` lo utiliza en su método de poner minas. Esto permite construir un mensaje específico sin necesidad de getter, aunque a cambio se otorga acceso directo al campo heredado.

```java
class Soldado {
    protected String nombre; // Accesible desde subclases
    public Soldado(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Soy " + nombre); }
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

La hay en algunos objetos.
En Java sí existe una clase base común: `Object`. Heredan ciertos métodos como el `toString()` o el `equals()`.
En C++ no la hay.

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La **herencia múltiple** consiste en que una clase herede de más de una superclase al mismo tiempo. Puede dar problemas si dos superclases tienen métodos o atributos con el mismo nombre, lo que genera ambigüedad sobre cuál se debe usar.

En Java no existe herencia múltiple de clases: una clase solo puede extender de una única clase (`extends`).

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. 
#### Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

```java
class Usuario {
    private String dni;
    public Usuario(String dni) { this.dni = dni; }
    @Override public String toString() {
        return "Usuario{dni='" + dni+ "'}";
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

    public Usuario getUsuario() { return usuario; }
}

class RepositorioUsuarios {
    public Usuario buscarPorDni(String dni) {
        Usuario criterio = new Usuario(dni);
        // Simulación: no se encuentra
        throw new UsuarioNoEncontradoException(criterio);
    }
}
```

## Herencia vs. Composición. 
### 10. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

No se recomienda usar herencia solo para reutilizar código porque heredar no significa "copiar funcionalidad", sino establecer una relación "A es-un B". Si esa relación conceptual no es verdadera, no se debe hacer.
En ese caso es mejor usar Composición.

### 11. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

La herencia impone una dependencia muy fuerte de las subclases respecto a la clase base. Se recomienda favorecer composición porque permite más flexibilidad, en lugar de fijarlas a esa jerarquía rígida.

### 12. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

La herencia rompe la encapsulación porque las subclases dependen de la implementación interna de la superclase. Si la superclase cambia, las subclases pueden verse afectadas, lo que hace que el sistema sea más frágil y difícil de mantener.

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
