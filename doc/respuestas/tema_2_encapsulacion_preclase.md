<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

La **encapsulación** es un principio fundamental de la POO que consiste en agrupar datos (atributos) y comportamientos (métodos) relacionados dentro de una única unidad llamada clase. La **ocultación de información** es un concepto complementario que busca restringir el acceso directo a los datos internos de un objeto, permitiendo que solo puedan ser manipulados a través de métodos públicos controlados. De manera similar a como en C se pueden usar funciones y estructuras en archivos separados con encabezados que exponen solo ciertas funciones, en POO se controla qué partes de una clase son accesibles desde el exterior.

El objetivo principal de la ocultación es proteger la integridad de los datos y facilitar el mantenimiento del código. Al ocultar los detalles de implementación, se crea una barrera entre el "qué hace" un objeto (su interfaz) y el "cómo lo hace" (su implementación interna).

Entre las ventajas de la ocultación de información se encuentran: (1) **Mayor robustez**, ya que se pueden validar los datos antes de modificarlos, evitando estados inconsistentes; (2) **Facilidad de mantenimiento**, porque los cambios en la implementación interna no afectan al código que usa la clase; (3) **Reducción de la complejidad**, al exponer solo lo necesario y esconder detalles irrelevantes; (4) **Prevención de errores**, pues se evita que otros programadores accedan o modifiquen datos de forma incorrecta; y (5) **Mejor modularidad**, permitiendo desarrollar y probar componentes de forma independiente.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La **interfaz pública** de una clase es el conjunto de métodos y atributos que están accesibles desde fuera de la clase, es decir, desde otras partes del programa. Es la "cara visible" de la clase: define qué operaciones se pueden realizar con los objetos de esa clase, pero sin revelar cómo se implementan internamente. Se puede comparar con la firma de las funciones en un archivo de cabecera `.h` en C, donde se declara qué funciones están disponibles sin mostrar su implementación.

La interfaz pública se relaciona estrechamente con la ocultación de información porque actúa como un contrato entre la clase y el resto del programa. Al diseñar una clase, se decide conscientemente qué exponer (la interfaz pública) y qué mantener oculto (la implementación privada). Los detalles internos, como la estructura de datos elegida para almacenar información o los algoritmos específicos utilizados, permanecen privados y pueden cambiarse sin afectar al código que usa la clase, siempre que la interfaz pública se mantenga igual.

En resumen, la interfaz pública define el "qué" (qué servicios ofrece la clase), mientras que la ocultación de información protege el "cómo" (cómo se implementan esos servicios internamente). Esta separación permite modificar la implementación sin romper el código existente que depende de la clase.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

Hay que diseñar con cuidado la interfaz pública porque una vez que otros programadores o partes del código comienzan a usar una clase, cualquier cambio en su interfaz pública puede romper ese código existente. Si se añaden restricciones a un método, se elimina un método público, se cambian sus parámetros o su tipo de retorno, todo el código que dependía de esa interfaz dejará de funcionar y necesitará ser modificado. Es similar a cuando en C se cambia la firma de una función en un archivo de cabecera: todos los archivos que la usan necesitan adaptarse.

No, cambiar la interfaz pública no es fácil. Cuanto más se use una clase en diferentes partes de un programa (o peor aún, en programas de otros desarrolladores), más costoso resulta modificar su interfaz. Por eso es fundamental invertir tiempo en diseñar una interfaz bien pensada desde el principio: debe ser lo suficientemente completa para cubrir las necesidades esperadas, pero lo suficientemente simple para no exponer detalles innecesarios. En contraste, los detalles privados de implementación sí pueden cambiarse libremente sin afectar a nadie, razón adicional para mantener privado todo lo que no necesite ser público.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las **invariantes de clase** son condiciones o propiedades que deben cumplirse siempre para los atributos de un objeto, garantizando que el objeto se mantenga en un estado válido y coherente. Por ejemplo, en una clase `Rectangulo`, una invariante podría ser que el ancho y el alto sean siempre valores positivos; en una clase `CuentaBancaria`, el saldo no debería poder ser negativo si no se permite el descubierto. Son restricciones lógicas que definen qué estados son válidos para un objeto.

La ocultación de información es crucial para mantener las invariantes de clase porque, al hacer privados los atributos, se obliga a que cualquier modificación pase por métodos públicos controlados. Si los atributos fueran públicos y accesibles directamente desde cualquier parte del código, sería imposible garantizar que las invariantes se respeten, ya que cualquier código externo podría modificar los valores de forma arbitraria. Al forzar el acceso a través de métodos, se puede incluir lógica de validación que rechace cambios que violarían las invariantes.

Por ejemplo, si una clase `Fecha` tiene atributos privados para día, mes y año, el método que modifica el día puede verificar que el valor esté entre 1 y el número de días válido para ese mes y año. Sin ocultación de información, cualquier código podría establecer el día a 35, creando un estado inválido imposible de detectar o prevenir.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

```java
public class Punto {
    // Atributos privados (ocultos)
    private double x;
    private double y;
    
    // Constructor público
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    // Método público para calcular distancia al origen
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
    
    // Métodos públicos para acceder a las coordenadas
    public double getX() {
        return x;
    }
    
    public double getY() {
        return y;
    }
}
```

La **interfaz pública** de la clase `Punto` está formada por el constructor `Punto(double x, double y)` y los métodos `calcularDistanciaAOrigen()`, `getX()` y `getY()`. Estos son los elementos marcados con `public` y representan todo lo que el código exterior puede usar para interactuar con objetos de tipo `Punto`.

El modificador **`public`** indica que un elemento (atributo, método o constructor) es accesible desde cualquier parte del programa, desde otras clases y paquetes. Es parte de la interfaz visible de la clase. Por el contrario, **`private`** indica que un elemento solo es accesible dentro de la propia clase, permaneciendo oculto para el resto del código. En el ejemplo, los atributos `x` e `y` son privados, lo que significa que no pueden ser accedidos ni modificados directamente desde fuera de la clase `Punto`. Solo pueden ser leídos mediante los métodos públicos `getX()` y `getY()`, y se establecen únicamente a través del constructor. Esta ocultación protege la integridad de los datos y permite cambiar la representación interna sin afectar al código que usa la clase.


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores `public` y `private` se pueden aplicar a varios elementos de una clase. Se pueden usar en **atributos** (variables de instancia o de clase), **métodos** (tanto de instancia como de clase/static), **constructores**, y **clases internas** (clases definidas dentro de otra clase). Estos modificadores controlan la visibilidad y accesibilidad de estos elementos desde otras partes del código.

Sin embargo, hay una restricción importante: las **clases de nivel superior** (las que se definen directamente en un archivo, no dentro de otra clase) solo pueden ser `public` o tener visibilidad de paquete (sin modificador explícito), pero **no pueden ser `private`**. Esto tiene sentido porque si una clase de nivel superior fuera privada, no sería accesible desde ningún lugar y no tendría utilidad. Solo las clases internas (anidadas) pueden ser privadas, ya que en ese caso serían visibles únicamente dentro de la clase que las contiene.

En resumen, dentro de una clase se puede marcar como `public` o `private` casi cualquier miembro: atributos, métodos, constructores y clases internas. La elección depende de si se quiere que ese elemento forme parte de la interfaz pública (accesible desde fuera) o se mantenga como detalle de implementación privado.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

Sí, existen más niveles de visibilidad además de pública y privada. En Java hay cuatro niveles: **`private`** (accesible solo dentro de la misma clase), **visibilidad de paquete** o por defecto (sin modificador, accesible desde cualquier clase del mismo paquete), **`protected`** (accesible desde la misma clase, clases del mismo paquete, y subclases incluso si están en otros paquetes), y **`public`** (accesible desde cualquier parte del programa). Esta graduación permite un control más fino sobre qué partes del código pueden acceder a cada elemento.

El modificador `protected` es especialmente útil para la herencia, un concepto de POO que permite crear nuevas clases basadas en clases existentes. Con `protected` se pueden compartir ciertos detalles de implementación con las subclases sin exponerlos completamente al público. La visibilidad de paquete es útil cuando varias clases relacionadas dentro del mismo paquete necesitan colaborar estrechamente.

Otros lenguajes orientados a objetos tienen sistemas similares pero con variaciones. Por ejemplo, C++ tiene `private`, `protected` y `public`, pero no tiene el concepto de visibilidad de paquete. Python es más flexible y usa convenciones: un guion bajo al inicio del nombre (como `_atributo`) sugiere que es privado por convención, aunque técnicamente sigue siendo accesible. C# tiene `private`, `protected`, `internal` (similar a la visibilidad de paquete, pero a nivel de ensamblado), `public`, y combinaciones como `protected internal`. La filosofía varía: algunos lenguajes son más estrictos en el control de acceso, mientras que otros confían más en las convenciones y la responsabilidad del programador.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

```java
public class Punto {
    private double x;
    private double y;
    
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
    
    // Nuevo método que accede a los atributos privados de otro objeto Punto
    public double calcularDistanciaAPunto(Punto otro) {
        double deltaX = this.x - otro.x;  // Acceso a otro.x (privado)
        double deltaY = this.y - otro.y;  // Acceso a otro.y (privado)
        return Math.sqrt(deltaX * deltaX + deltaY * deltaY);
    }
}
```

La respuesta correcta es **(a) otras clases**. Los miembros privados están ocultos para otras clases, pero **no** para otras instancias de la misma clase. Como se observa en el ejemplo, el método `calcularDistanciaAPunto` puede acceder directamente a `otro.x` y `otro.y`, aunque `otro` sea un objeto diferente y esos atributos sean privados. Esto es posible porque el código está dentro de la clase `Punto`, y el modificador `private` otorga acceso a nivel de clase, no a nivel de instancia individual.

Esta característica es útil porque permite que los objetos de una misma clase colaboren entre sí de forma eficiente. Si la privacidad fuera a nivel de instancia, sería muy complicado implementar métodos como el de comparación o cálculo de distancias entre objetos del mismo tipo. En definitiva, `private` protege la implementación interna de una clase del acceso externo, pero permite que todas las instancias de esa clase accedan a los detalles privados de las demás instancias de su mismo tipo.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos **"getter"** y **"setter"** son métodos públicos que proporcionan acceso controlado a los atributos privados de una clase. Un **getter** (también llamado "accessor" o método de acceso) es un método que devuelve el valor de un atributo privado, permitiendo leer su contenido sin acceder directamente al atributo. Un **setter** (también llamado "mutator" o método modificador) es un método que permite modificar el valor de un atributo privado, recibiendo el nuevo valor como parámetro. En Java, la convención de nombres es usar `get` o `set` seguido del nombre del atributo con la primera letra en mayúscula, como `getX()` o `setNombre(String nombre)`.

La utilidad de estos métodos radica en que mantienen la encapsulación incluso cuando se necesita acceder o modificar atributos. A diferencia de hacer los atributos públicos, los getters y setters permiten incluir lógica adicional: validación de datos en los setters (por ejemplo, verificar que un valor no sea negativo), cálculos o transformaciones en los getters (como redondear un valor antes de devolverlo), o incluso cambiar la implementación interna sin afectar al código que usa la clase. También permiten crear atributos de solo lectura (proporcionando solo el getter) o de solo escritura (solo el setter).

Por ejemplo, en la clase `Punto`, los métodos `getX()` y `getY()` son getters que permiten leer las coordenadas. Si se quisiera permitir modificar las coordenadas, se añadirían setters como `setX(double x)` y `setY(double y)`, que podrían incluir validaciones si fuera necesario.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No, en este contexto "seguridad" no se refiere a seguridad informática frente a ataques externos o hackers. Se refiere a la **seguridad o robustez del código**: la capacidad de garantizar que el programa funcione correctamente y que los objetos mantengan estados válidos y coherentes. Es una seguridad en el sentido de que el programa esté protegido contra errores de programación, usos incorrectos accidentales, y estados inconsistentes.

La ocultación de información mejora esta seguridad porque previene que el código externo modifique los datos de formas no previstas que podrían romper las invariantes de clase o causar comportamientos incorrectos. Por ejemplo, si un atributo `edad` fuera público, cualquier código podría asignarle un valor negativo, causando un estado inválido. Con encapsulación, el setter puede validar que la edad sea positiva, garantizando la consistencia. Esta "seguridad" se traduce en programas más confiables, con menos errores difíciles de detectar, y más fáciles de mantener.

Aunque la encapsulación no es una medida de seguridad criptográfica, sí contribuye indirectamente a la seguridad general del software al reducir la superficie de posibles errores y hacer el código más predecible y controlable. Un programa bien encapsulado es más resistente a errores, tanto accidentales como intencionales por parte de otros desarrolladores que usen nuestro código.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Los **miembros de instancia** son atributos o métodos que pertenecen a cada objeto individual creado a partir de una clase. Cada instancia tiene su propia copia de estos atributos con valores potencialmente diferentes. Por ejemplo, en la clase `Punto`, cada objeto tiene sus propios valores de `x` e `y`. En contraste, los **miembros de clase** (también llamados miembros estáticos) pertenecen a la clase misma, no a las instancias individuales. Existe una única copia compartida por todos los objetos de esa clase. Es similar al concepto de variables globales en C, pero con el alcance limitado a la clase.

Los miembros de clase son útiles para almacenar información compartida por todas las instancias o para proporcionar funcionalidad que no depende de ningún objeto específico. Por ejemplo, se podría tener un contador estático que lleve la cuenta de cuántos objetos `Punto` se han creado, o constantes compartidas como `Math.PI` en Java. Los métodos de clase (estáticos) pueden ser invocados sin necesidad de crear un objeto, como `Math.sqrt()`.

Sí, los miembros de clase también se pueden ocultar usando los mismos modificadores de acceso (`private`, `public`, etc.). Un atributo o método estático puede ser privado si solo debe ser usado internamente por la clase, o público si forma parte de la interfaz pública. Por ejemplo, un contador privado de instancias creadas podría ser `private static int contadorInstancias`, accesible solo dentro de la clase, mientras que un método factoría estático sería `public static` para poder ser invocado desde el exterior sin crear antes un objeto.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene sentido en ciertos casos específicos. Un constructor privado impide que se creen instancias de la clase desde fuera de ella misma, lo que puede parecer contradictorio, pero resulta útil en varios patrones de diseño. El uso más común es en el **patrón Singleton**, que garantiza que solo exista una única instancia de una clase en todo el programa. El constructor privado evita que se creen múltiples instancias, y se proporciona un método estático público que devuelve la única instancia existente.

Otro uso es en clases que solo contienen métodos y constantes estáticas (como la clase `Math` de Java), donde no tiene sentido crear instancias porque toda la funcionalidad es estática. También se usan en el **patrón Factory**, donde los constructores son privados y se proporcionan métodos factoría estáticos públicos que controlan cómo y cuándo se crean los objetos, permitiendo lógica adicional como reutilizar instancias existentes, validar parámetros antes de la construcción, o devolver subtipos específicos según los parámetros.

En definitiva, los constructores privados son una herramienta de encapsulación que permite controlar completamente cómo se crean las instancias de una clase, delegando esa responsabilidad a métodos estáticos que pueden aplicar reglas o restricciones específicas.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

En Java, los miembros de clase se indican con la palabra clave **`static`**. Este modificador se coloca antes del tipo del atributo o del tipo de retorno del método, y puede combinarse con los modificadores de acceso como `private` o `public`. Un miembro declarado como `static` pertenece a la clase en lugar de a las instancias individuales, existiendo una única copia compartida por todos los objetos.

```java
public class Punto {
    // Atributos de instancia (privados)
    private double x;
    private double y;
    
    // Atributos de clase (estáticos) para rastrear máximos
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;
    
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        
        // Actualizar los máximos si es necesario
        if (x > maxX) {
            maxX = x;
        }
        if (y > maxY) {
            maxY = y;
        }
    }
    
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
    
    public double getX() {
        return x;
    }
    
    public double getY() {
        return y;
    }
    
    // Métodos de clase (estáticos) para acceder a los máximos
    public static double getMaxX() {
        return maxX;
    }
    
    public static double getMaxY() {
        return maxY;
    }
}
```

En este ejemplo, `maxX` y `maxY` son atributos estáticos privados que se comparten entre todas las instancias de `Punto`. Cada vez que se crea un nuevo punto, el constructor actualiza estos valores si las coordenadas superan los máximos actuales. Los métodos estáticos `getMaxX()` y `getMaxY()` permiten consultar estos valores sin necesidad de tener un objeto `Punto` específico, invocándolos como `Punto.getMaxX()`. Como los atributos estáticos son privados, se mantiene la encapsulación y solo pueden ser modificados de forma controlada dentro de la clase.


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`?

```java
public static Punto crearPuntoRedondeado(double x, double y) {
    double xRedondeada = Math.round(x);
    double yRedondeada = Math.round(y);
    return new Punto(xRedondeada, yRedondeada);
}
```

Sí, se ha usado **`static`** porque un método factoría debe ser un método de clase, no de instancia. Esto es necesario porque el objetivo del método factoría es precisamente crear y devolver una nueva instancia de `Punto`, y para llamarlo no debe ser necesario tener ya un objeto `Punto` existente. Se invoca directamente sobre la clase: `Punto p = Punto.crearPuntoRedondeado(3.7, 5.2);`.

El método factoría es una alternativa a los constructores que ofrece varias ventajas: puede tener un nombre descriptivo que indique qué tipo de objeto crea (en este caso, que redondea las coordenadas), puede incluir lógica de procesamiento antes de la construcción, y puede decidir qué constructor llamar o incluso reutilizar instancias existentes. En este ejemplo, el método recibe coordenadas con decimales, las redondea usando `Math.round()`, y luego invoca el constructor normal de `Punto` con los valores redondeados. Esta aproximación mantiene la responsabilidad del redondeo encapsulada dentro del método factoría, separada de la lógica del constructor básico.


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

```java
public class Punto {
    // Cambio en la implementación: ahora usa un array
    private double[] coordenadas;
    
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;
    
    // La interfaz pública del constructor permanece igual
    public Punto(double x, double y) {
        coordenadas = new double[2];
        coordenadas[0] = x;
        coordenadas[1] = y;
        
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }
    
    // Los métodos públicos mantienen la misma firma
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(coordenadas[0] * coordenadas[0] + 
                        coordenadas[1] * coordenadas[1]);
    }
    
    public double getX() {
        return coordenadas[0];
    }
    
    public double getY() {
        return coordenadas[1];
    }
    
    public double calcularDistanciaAPunto(Punto otro) {
        double deltaX = this.coordenadas[0] - otro.coordenadas[0];
        double deltaY = this.coordenadas[1] - otro.coordenadas[1];
        return Math.sqrt(deltaX * deltaX + deltaY * deltaY);
    }
    
    public static double getMaxX() {
        return maxX;
    }
    
    public static double getMaxY() {
        return maxY;
    }
    
    public static Punto crearPuntoRedondeado(double x, double y) {
        return new Punto(Math.round(x), Math.round(y));
    }
}
```

Este ejemplo demuestra perfectamente el valor de la encapsulación. Al haber mantenido privados los atributos `x` e `y` originales y haber proporcionado acceso solo a través de métodos públicos, se ha podido cambiar completamente la representación interna (de dos variables separadas a un array) sin modificar la interfaz pública. Todo el código que usaba la clase `Punto` seguirá funcionando exactamente igual, sin necesidad de ningún cambio.

Los métodos `getX()` y `getY()` ahora devuelven `coordenadas[0]` y `coordenadas[1]` respectivamente, y el constructor almacena los valores en el array en lugar de en variables separadas. El cambio es completamente transparente para los usuarios de la clase. Este es uno de los beneficios más importantes de la ocultación de información: permite evolucionar y mejorar la implementación interna sin romper el código que depende de la clase, siempre que se mantenga la misma interfaz pública.


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

No, no es mejor declararlo público, aunque pueda parecer equivalente a primera vista. Usar getters y setters en lugar de atributos públicos ofrece varias ventajas importantes: permite añadir validación en el setter para garantizar que solo se asignen valores válidos, permite cambiar la representación interna sin afectar al código que usa la clase (como se vio en el ejercicio anterior), permite realizar cálculos o transformaciones antes de devolver o establecer el valor, y permite convertir un atributo de lectura-escritura a solo lectura simplemente eliminando el setter, sin romper el código que lo lee.

La **convención más habitual** en programación orientada a objetos es que **los atributos sean privados**. Esta es una práctica fuertemente recomendada en Java y la mayoría de lenguajes orientados a objetos. Solo se proporcionan getters y setters públicos cuando realmente se necesita acceso externo a esos valores. Muchas veces, los atributos privados solo son accedidos y modificados internamente por los métodos de la clase, sin necesidad de exponerlos.

Sí, esto tiene mucho que ver con las **invariantes de clase**. Un setter puede validar que el nuevo valor no viole las invariantes antes de asignarlo. Por ejemplo, en una clase `CuentaBancaria`, el setter del saldo puede verificar que no se establezca un valor negativo si no se permite descubierto. Con un atributo público, es imposible prevenir asignaciones inválidas. Además, en el futuro se podría necesitar añadir validación o lógica adicional, y tener getters/setters desde el principio lo permite sin cambiar la interfaz pública. Los atributos públicos no ofrecen esta flexibilidad y rompen la encapsulación.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase es **inmutable** cuando sus objetos no pueden cambiar de estado después de ser creados. Una vez construido el objeto, sus atributos permanecen constantes durante toda su vida. Para lograr la inmutabilidad, los atributos se declaran `private` y `final` (para que no puedan ser reasignados), no se proporcionan métodos setter, y se tiene cuidado de no exponer referencias a objetos mutables internos. La clase `String` de Java es un ejemplo clásico de clase inmutable.

Un **método modificador** es cualquier método que cambia el estado interno de un objeto, es decir, que modifica el valor de uno o más atributos. No, un método modificador no es siempre un setter. Los setters son el tipo más directo de método modificador (asignan un nuevo valor a un atributo), pero existen muchos otros. Por ejemplo, en una clase `CuentaBancaria`, los métodos `depositar(double cantidad)` o `retirar(double cantidad)` son modificadores porque cambian el saldo, aunque no sean setters tradicionales. Un método `incrementarEdad()` también sería modificador. En una clase inmutable, no existen métodos modificadores.

Sí, la inmutabilidad tiene ventajas significativas. Los objetos inmutables son inherentemente **seguros en entornos concurrentes** (pueden ser compartidos entre múltiples hilos sin preocupación por sincronización), son más **simples de entender y usar** (su estado nunca cambia, eliminando efectos secundarios inesperados), facilitan el **razonamiento sobre el código** (si se pasa un objeto inmutable a un método, se sabe que no será modificado), y pueden ser **compartidos libremente** sin necesidad de hacer copias defensivas. Además, su comportamiento es más predecible y reducen la posibilidad de errores relacionados con cambios de estado inadvertidos. La desventaja es que operaciones que implican "modificar" un objeto requieren crear uno nuevo, lo que puede ser menos eficiente en ciertos casos.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`