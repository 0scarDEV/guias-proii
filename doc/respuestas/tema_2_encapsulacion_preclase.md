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


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`?


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`