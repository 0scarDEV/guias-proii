# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.
**Opción 1: Usar un código de retorno especial**

En C, una estrategia común es devolver un valor especial que indique un error. Por ejemplo, se puede devolver -1 o NaN (Not a Number) para señalar un fallo. El código llamador debe verificar este valor especial después de cada llamada. Esta aproximación es simple pero poco elegante, ya que el valor especial más significativo (el resultado) se mezcla con la información del estado (éxito o error).

```c
#include <math.h>
#include <stdio.h>

float raiz(float numero) {
    if (numero < 0) return -1.0;  // Indica error
    return sqrt(numero);
}

int main() {
    float resultado = raiz(-4);
    if (resultado == -1.0) {
        printf("Error: no se puede calcular raíz de número negativo\n");
    }
    return 0;
}
```

**Opción 2: Usar un parámetro de salida y devolver un código de estado**

Otra alternativa es pasar un puntero donde la función almacena el resultado, y devolver un código de error (0 para éxito, -1 para fallo). De esta forma se separa claramente el valor calculado del estado de la operación. Esta aproximación es más robusta porque el resultado y el estado viajan por caminos diferentes, pero requiere más verbosidad: se debe pasar una dirección de memoria y verificar después si la operación fue exitosa.

```c
#include <math.h>
#include <stdio.h>

int raiz(float numero, float *resultado) {
    if (numero < 0) return -1;  // Error
    *resultado = sqrt(numero);
    return 0;  // Éxito
}

int main() {
    float resultado;
    int estado = raiz(-4, &resultado);
    if (estado == -1) {
        printf("Error: no se puede calcular raíz de número negativo\n");
    }
    return 0;
}
```


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una excepción es un evento extraordinario que representa una situación anómala durante la ejecución de un programa, como intentar acceder a un archivo inexistente, dividir por cero o asignar memoria inválida. A diferencia de C, donde es necesario revisar manualmente códigos de retorno, Java proporciona un mecanismo automático y estructurado para tratar estos casos excepcionales.

Al implementar funciones, se utiliza `throw` para señalar que ha ocurrido una condición de error, permitiendo que el código llamador maneje la situación de forma explícita mediante `try-catch`. Esto facilita la separación entre la lógica normal de la función y el manejo de errores, haciendo el código más legible y menos propenso a que se ignore un error. El programador que llama la función puede entonces decidir cómo reaccionar ante la excepción: captarla y recuperarse, registrar el error, o permitir que se propague a niveles superiores.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.
```java
public class Calculadora {
    public double raiz(double numero) {
        if (numero < 0) {
            throw new IllegalArgumentException("No se puede calcular raíz de número negativo");
        }
        return Math.sqrt(numero);
    }
}

public class Main {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        
        try {
            double resultado = calc.raiz(-4);
            System.out.println("Raíz: " + resultado);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

En Java, cuando el método `raiz()` detecta un número negativo, lanza una excepción usando `throw`. Esta excepción interrumpe inmediatamente la ejecución del método y salta al bloque `catch` en el código llamador. El manejo es mucho más elegante que en C: no requiere revisar valores de retorno especiales, el control fluye automáticamente al manejador, y el mensaje de error se encapsula en el objeto excepción.

El código que llama la función utiliza la estructura `try-catch` para capturar y manejar la excepción de forma controlada. El bloque `try` contiene el código que podría lanzar una excepción, y el bloque `catch` especifica qué tipo de excepción se captura y cómo se maneja. En este caso, se imprime el mensaje de error encapsulado en la excepción, permitiendo que el programa continúe de forma controlada en lugar de terminar abruptamente.

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

**Lanzar una excepción** significa ejecutar la instrucción `throw`, creando un objeto excepción y deteniendo inmediatamente la ejecución del método. En el ejemplo anterior, cuando se invoca `calc.raiz(-4)` y la función detecta un número negativo, se ejecuta `throw new IllegalArgumentException(...)`, lo que crea el objeto excepción y abandona el método sin devolver ningún valor.

**Controlar o capturar una excepción** es el acto de interceptarla usando un bloque `catch`. En el `main`, el bloque `catch (IllegalArgumentException e)` captura la excepción que fue lanzada en `raiz()`, permitiendo ejecutar código alternativo como imprimir un mensaje de error. Sin este bloque, el programa finalizaría abruptamente con un error no controlado.

**Propagar una excepción** significa que, si no se captura en el método donde se lanzó, la excepción "viaja" hacia arriba por la pila de llamadas buscando un `catch` que pueda manejarla. Desde el punto de vista de la pila: cuando `raiz()` lanza la excepción, ese método desaparece de la pila inmediatamente; el control retrocede un nivel buscando un `catch` en `main()`, donde lo encuentra. Las funciones intermedias que no capturan la excepción simplemente desaparecen de la pila sin reanudarse; la ejecución no retorna a ellas ni se ejecuta el código que hubiera después de la llamada. Solo el código dentro del bloque `catch` correspondiente se ejecuta después de que la excepción es capturada.

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

En C, cuando una función en medio de la cadena de llamadas (por ejemplo, `funcionA()` llamada desde `funcionB()` llamada desde `main()`) detecta un error, debe devolver un código de error explícitamente. Este código debe ser comprobado en cada nivel de la cadena de llamadas, lo que obliga a escribir verificaciones de errores tedioso y repetitivo en cada función. Si se olvida verificar algún código de retorno, el error pasará desapercibido, potencialmente causando comportamientos inesperados más adelante.

En Java, la propagación natural de excepciones permite que una excepción "salte" automáticamente sobre todos los niveles intermedios sin necesidad de manejarla explícitamente en cada uno. Si `funcionA()` lanza una excepción y ninguna función intermedia la captura, se propagará directamente al nivel superior o al contexto donde se desee manejarla. Esto significa que el manejador del error puede estar en el nivel más apropiado documentalmente, sin contaminar el código intermedio con lógica de control de errores. Además, si posteriormente se añade un nuevo nivel de función en medio de dos existentes, no es necesario modificar las verificaciones de códigos de retorno, ya que la excepción se propagará automáticamente a través del nuevo nivel.

La ventaja fundamental es la **claridad** y **mantenibilidad** del código: la lógica normal se separa claramente de la lógica de manejo de errores, y no existe riesgo de que se ignore accidentalmente un error por falta de verificación.

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

Sí, en Java las excepciones son objetos que heredan de la clase `Throwable`. Esto es fundamental para la orientación a objetos: cada excepción encapsula información relevante al error en sus atributos y métodos. Por ejemplo, cada objeto excepción incluye un mensaje descriptivo, la traza de la pila de llamadas, y puede llevar datos adicionales útiles para diagnosticar el problema. Esto contrasta con C, donde los errores se representaban como simples números o valores especiales.

Desde la perspectiva de la encapsulación, esto es muy poderoso. En lugar de devolver un simple código de error numérico como en C, la excepción encapsula en un objeto toda la información contextual del error. Además, se puede capturar excepciones por tipo, aprovechando la herencia polimórfica: un `catch (Exception e)` captura cualquier excepción, mientras que `catch (IOException e)` solo captura excepciones de entrada/salida. Esto permite manejar diferentes tipos de errores de forma distinta según su naturaleza.

También es posible crear excepciones personalizadas extendiendo las clases existentes. Por ejemplo, se puede definir una clase `ValorNegativoException` que herede de `Exception` o `RuntimeException`, permitiendo definir excepciones específicas del dominio de la aplicación. Estas excepciones personalizadas pueden encapsular campos adicionales, constructores personalizados, o métodos utilitarios relevantes para el negocio. Esto hace el código más semántico, fácil de mantener, y permite a quien llama la función ser muy específico sobre qué tipos de errores puede esperar.

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

En el ejemplo en C, cuando se recibía un código de retorno -1, no había información adicional sobre qué salió mal, dónde ocurrió, o cómo se llegó a esa situación. El programador debía agregar manualmente logs o comentarios para rastrear el problema. En cambio, todo objeto excepción en Java encapsula información essential del error que viaja con la excepción.

La información más importante que lleva una excepción es el **mensaje de error** (accesible mediante `getMessage()`), que describe qué salió mal de forma legible y contextualizada. Además, cada objeto excepción incluye la **traza de la pila** (accesible mediante `printStackTrace()` o `getStackTrace()`), que registra toda la cadena de métodos que se ejecutaron hasta el punto donde se lanzó la excepción, permitiendo identificar exactamente dónde ocurrió el error y cómo se llegó a él. El objeto también puede contener una **causa** (referencia a otra excepción que originó ésta, accesible mediante `getCause()`), útil para encadenar errores de bajo nivel a mensajes de error de alto nivel.

Cuando se captura la excepción en el manejador, toda esta información está disponible encapsulada en el objeto, facilitando tremendamente el debugging, el logging estructurado, y la recuperación inteligente del error. A diferencia de C, donde el programador debe construir manualmente toda esta información contextual, Java proporciona un mecanismo automático que preserva el contexto del error a lo largo de toda la propagación.

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, se pueden tener múltiples bloques `catch` después de un único bloque `try`. Esto permite diferenciar el manejo según el tipo específico de excepción que se lance. Cada bloque `catch` captura excepciones de un tipo determinado, y Java los evalúa en orden de arriba a abajo, ejecutando el primero cuyo tipo sea compatible (por igualdad o por herencia) con la excepción lanzada.

```java
try {
    // código que puede lanzar excepciones
} catch (IOException e) {
    System.out.println("Error de entrada/salida: " + e.getMessage());
} catch (NumberFormatException e) {
    System.out.println("Número inválido: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Error general: " + e.getMessage());
}
```

**Solo se ejecuta un bloque `catch`**: el primero cuya excepción sea del tipo adecuado o un supertipo de la excepción lanzada. Una vez ejecutado un bloque `catch`, el control continúa después del último `catch` (o `finally` si existe), saltando los bloques `catch` restantes. Es importante notar que el **orden importa**: si se coloca `catch (Exception e)` al principio, capturará todas las excepciones y los bloques `catch` posteriores nunca se ejecutarán, quedando código inalcanzable. Por eso se recomienda colocar los tipos más específicos primero (como `IOException`) y los más generales al final (como `Exception`).

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

El bloque `finally` se ejecuta **siempre**, independientemente de si se lanzó una excepción o no. Es el mecanismo perfecto para colocar código de limpieza (cierre de ficheros, liberación de recursos, etc.) que debe ejecutarse en cualquier circunstancia. Incluso si se propaga una excepción, el bloque `finally` se ejecuta antes de que la excepción continúe subiendo por la pila de llamadas.

**Ejemplo con `catch`:**

```java
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("archivo.txt"));
    String linea = reader.readLine();
    System.out.println(linea);
} catch (IOException e) {
    System.out.println("Error de lectura: " + e.getMessage());
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            System.out.println("Error cerrando fichero: " + e.getMessage());
        }
    }
}
```

**Ejemplo sin `catch` (solo `try-finally`):**

```java
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("archivo.txt"));
    String linea = reader.readLine();
    System.out.println(linea);
    // Si ocurre una excepción, se propaga después de finally
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            System.out.println("Error cerrando fichero: " + e.getMessage());
        }
    }
}
```

En ambos casos, el bloque `finally` garantiza que el recurso se cierre correctamente. Sin `catch`, la excepción se propaga después de ejecutar `finally`, lo que permite usar `finally` como garantía de limpieza sin necesariamente manejar el error en ese nivel.

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, el bloque `finally` puede ir sin `catch`. La estructura válida es `try-finally`, lo que resulta útil cuando no se desea manejar la excepción en ese nivel pero sí garantizar la limpieza de recursos. La excepción se propagará después de ejecutar el bloque `finally`, permitiendo que un nivel superior maneje el error. También es válida la estructura `try-catch-finally`, donde se maneja la excepción y luego se ejecuta la limpieza.

El bloque `finally` se ejecuta **siempre**, sin excepciones (valga el juego de palabras). Tanto si el código del `try` se ejecuta correctamente como si lanza una excepción, el `finally` se ejecutará. Esto hace que sea el lugar perfecto para código de limpieza. Incluso si hay un `catch` que captura la excepción y el programa parece recuperarse normalmente, el `finally` igualmente se ejecuta después del `catch`.

Ahora bien, si hay un `return` en medio del bloque `try`, el comportamiento puede parecer sorprendente: el `finally` **aún se ejecuta antes de que el método retorne**. Java garantiza que el bloque `finally` se ejecute incluso cuando hay un `return`, `break`, o `continue` dentro del `try` o del `catch`. El flujo es: se evalúa la instrucción `return`, se ejecuta el `finally`, y entonces se retorna el valor. Si el `finally` también contiene un `return`, este sobrescribe el return anterior.

```java
public static int ejemplo() {
    try {
        return 1;  // Se marcará para retornar 1
    } finally {
        System.out.println("Se ejecuta finally incluso con return");  // Esto se imprime
        // Aquí podría haber return 2; que sobrescribiría el return 1
    }
}
```

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Las excepciones **controladas** (checked exceptions) son aquellas que el compilador de Java **obliga** a tratar explícitamente. Si un método lanza una excepción controlada, quien lo llama debe capturarla con `try-catch` o declararla en la firma usando `throws`. Son excepciones que representan condiciones anómalas pero potencialmente recuperables, como problemas al acceder a ficheros (`IOException`) o conexiones de red. Las excepciones **no controladas** (unchecked exceptions) heredan de `RuntimeException` y no requieren captura obligatoria; el compilador no las obliga a ser tratadas. Representan típicamente errores de programación o situaciones que difícilmente se pueden recuperar, como intentar acceder a un índice de array inválido (`ArrayIndexOutOfBoundsException`) o lanzar una excepción por parámetro ilegal (`IllegalArgumentException`).

El papel de `RuntimeException` es ser la clase base de todas las excepciones no controladas. Cualquier excepción que herede de `RuntimeException` se considera "no controlada", mientras que las que heredan directamente de `Exception` se consideran "controladas". Esta distinción existe porque Java reconoce que hay errores del sistema (como los que podrían ocurrir en acceso a archivos) que deben ser tratados obligatoriamente, mientras que errores de lógica (como parámetros inválidos) pueden prevenirse en tiempo de desarrollo sin necesidad hacer verificaciones en tiempo de ejecución.

Ejemplos de excepciones controladas propias: `FileNotFoundException` (fichero no existe), `IOException` (error de lectura/escritura). Ejemplos de excepciones no controladas propias: `IllegalArgumentException` (parámetro inválido), `NullPointerException` (acceso a referencia nula). Se puede crear una excepción controlada heredando de `Exception` y una no controlada heredando de `RuntimeException`:

```java
// Excepción controlada personalizada
public class UsuarioNoEncontradoException extends Exception {
    public UsuarioNoEncontradoException(String mensaje) {
        super(mensaje);
    }
}

// Excepción no controlada personalizada
public class ValorNegativoException extends RuntimeException {
    public ValorNegativoException(String mensaje) {
        super(mensaje);
    }
}
```

**Situaciones donde se prefiere excepción controlada:**
- Acceso a ficheros en disco (el fichero podría no existir)
- Conexiones de red (la red podría estar caída)
- Lectura de datos de servidor remoto (el servidor podría ser inaccesible)
- Parseo de documentos XML o JSON externos (el formato podría ser inválido)

**Situaciones donde se prefiere excepción no controlada:**
- Validación de parámetros (parámetro negativo cuando se espera positivo)
- Operaciones con colecciones (índice fuera de rango)
- Precondiciones de un método (estado incorrecto del objeto)
- Errores derivados de bugs en el código (no se puede recuperar)

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

`throws` es una palabra clave que se añade a la firma de un método para declarar que ese método puede lanzar una o más excepciones controladas, sin manejarlas internamente. En lugar de usar `try-catch` para capturar la excepción dentro del método, se declara en la firma que la excepción será propagada al código llamador, transfiriendo la responsabilidad del manejo a un nivel superior de la aplicación.

```java
public void abrirArchivo(String nombreArchivo) throws IOException {
    FileInputStream fis = new FileInputStream(nombreArchivo);
    // Si el archivo no existe, se lanza IOException sin capturarla
}
```

`throws` es **alternativa a capturar** porque Java obliga a tratar las excepciones controladas de una u otra forma: bien capturándolas con `try-catch`, o bien declarándolas en `throws`. No se puede simplemente ignorar una excepción controlada; el compilador rechaza el código que no la trate de alguna manera. Al usar `throws`, se expresa que el método reconoce que puede haber un error, pero que es responsabilidad del llamador decidir cómo manejarlo. Esta es frecuentemente la mejor opción cuando el método no tiene contexto suficiente para recuperarse del error de forma inteligente, o cuando el error es más relevante en un nivel superior de abstracción.

La ventaja de usar `throws` es que permite que el error "suba" naturalmente por la cadena de llamadas hasta encontrar código que sí tenga la capacidad y el contexto para manejar la situación adecuadamente. Por ejemplo, un método bajo nivel que accede a un fichero puede no saber qué hacer si el fichero no existe, pero el controlador web que lo llamó sí sabe que debe mostrar un error al usuario.

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdata del `finally`.

```java
public class LectorArchivos {
    public String leerPrimeraLinea(String nombreArchivo) throws IOException {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader(nombreArchivo));
            return reader.readLine();
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("Error cerrando fichero: " + e.getMessage());
                }
            }
        }
    }

    public static void main(String[] args) {
        LectorArchivos lector = new LectorArchivos();
        try {
            String linea = lector.leerPrimeraLinea("datos.txt");
            System.out.println("Primera línea: " + linea);
        } catch (IOException e) {
            System.out.println("Error de lectura: " + e.getMessage());
        }
    }
}
```

La firma `public String leerPrimeraLinea(String nombreArchivo) throws IOException` declara que el método puede lanzar `IOException`. El método no captura la excepción que genera `new FileReader(nombreArchivo)` si el archivo no existe; simplemente la deja propagarse. El bloque `try-finally` (sin `catch`) garantiza que el recurso se cierre correctamente antes de que la excepción continúe subiendo hacia el código llamador. Nótese que el `finally` se ejecuta independientemente de si se lanza la excepción o no: tanto si la lectura es exitosa como si falla al abrir el fichero, el recurso se liberará.

En `main`, el código que llama a `leerPrimeraLinea()` es el responsable de capturar la `IOException`. Esta separación de responsabilidades permite que el método bajo nivel (`leerPrimeraLinea`) no tenga que decidir cómo manejar el error, sino simplemente garantizar limpieza de recursos. El código de nivel superior, que sí conoce el contexto de negocio (en este caso, simplemente mostrar un error), es quien decide cómo reaccionar.

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

