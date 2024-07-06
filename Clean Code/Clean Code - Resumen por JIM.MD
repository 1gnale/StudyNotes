# Filosofía 5S

-Seiri u organización: Es fundamental saber dónde están las cosas,
mediante enfoques como el uso de nombres correctos. ¿Cree que los
nombres de los identificadores no son relevantes? Lea los siguientes
capítulos.

-Seiton o sistematización: Existe un antiguo dicho norteamericano: un
sitio para cada cosa y cada cosa en su sitio. Un fragmento de código
debe estar donde esperamos encontrarlo; en caso contrario, refactorice
hasta conseguirlo.

-Seiso o limpieza: Mantenga limpio el lugar de trabajo. ¿Qué dicen los
autores sobre inundar el código de comentarios y líneas que capturan
historias o deseos futuros? Elimínelos.

-Seiketsu o estandarización: El grupo decide cómo mantener limpio el
lugar de trabajo. ¿Cree que este libro habla sobre tener un estilo de
código coherente y una serie de prácticas dentro del grupo? ¿De dónde
provienen esos estándares? Siga leyendo.

-Shutsuke o disciplina: Significa ser disciplinado en la aplicación de las
prácticas y reflejarlas en el trabajo y aceptar los cambios.

# Notas Clean Code

 El resumen acortado de las bases del Clean Code según Robert Martín son: *Reducir los duplicados, maximizar la expresividad y diseñar sencillas abstracciones en las fases iniciales.* Estos conceptos se profundizan en el resumen a continuación y en el libro. Personalmente recomiendo la experiencia de leer Clean Code. Pero si este resumen, algo menos acortado a comparación del que nos da el tío Bob, te ayuda en el camino eres bienvenido

## Nombres correctos

### Usar nombres con **intención**

 El nombre de una variable, función o clase debe responder una serie de cuestiones básicas. Debe indicar por qué existe, qué hace y cómo se usa. Si un nombre requiere un comentario, significa que no revela su cometido.
```java
int d; // tiempo transcurrido en días
```
 El nombre d no revela nada. No evoca una sensación de tiempo transcurrido, ni de días.
```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
 La elección de nombres que revelen intenciones facilita considerablemente la comprensión y la modificación del código.

### Evite la **desinformación**

 No haga referencia a un grupo de cuentas como accountList a menos que realmente sea una lista (List). La palabra lista tiene un significado concreto para los programadores. Si el contenedor de las cuentas no es realmente una lista, puede provocar falsas conclusiones. Por tanto, resulta más adecuado usar accountGroup, bunchOfAccounts o simplemente accounts. Evite usar nombres con variaciones mínimas. ¿Cuánto se tarda en **apreciar la sutil diferencia entre**
 ``XYZControllerForEfficientHandlingOfStrings`` 
 y
 ``XYZControllerForEfficientStorageOfStrings``
 en un modulo? Ambas palabras tienen una forma similar. La ortografía similar de conceptos parecidos es información; el uso de ortografía incoherente es desinformación.


### Realizar distinciones **sólo** con sentido
 
 *Los nombres de series numéricas, por otra parte (a1, a2… aN) son lo contrario a los nombres intencionados. No desinforman, simplemente no ofrecen información aunque satisfaga al compilador.*

 Las palabras adicionales son redundantes. La palabra *variable* no debe incluirse nunca en el nombre de una variable. La palabra *table* no debe incluirse nunca en el nombre de una tabla. ¿Es mejor `NameString` que `Name`? ¿Podría ser `Name` un número de coma flotante? En caso afirmativo, incumple la regla anterior sobre desinformación. Imagine que encuentra una clase con el nombre `Customer` y otra con el nombre `CustomerObject`. ¿Cuál sería la distinción? ¿Cuál representa mejor el historial de pagos de un cliente?

 Debe diferenciar los nombres de forma que el lector aprecie las diferencias. Pues como escritores tenemos una responsabilidad con el lector.

### Use **nombres pronunciables**

 *Cree nombres pronunciables. Si no lo puede pronunciar, no podrá explicarlo sin parecer tonto. Es un factor importante, ya que la programación es una actividad social.*

 Teníamos que explicar las variables a los nuevos programadores y cuando las pronunciaban, usaban palabras inventadas en lugar de nombres correctos. Compare:
```java
        class DtaRcrd102 {
        private Date genymdhms;
        private Date modymdhms;
        private final String pszqint = “102”;
        /*… */
        };
```
    con:
```java
        class Customer {
            private Date generationTimestamp;
            private Date modificationTimestamp;
            private final String recordId = “102”;
        /*… */
        };
```

### Use nombres que **se puedan buscar**

 Personalmente prefiero nombres de una letra que sólo se puedan usar como variables locales dentro de métodos breves. La longitud de un nombre debe corresponderse al tamaño de su ámbito [N5]. Si una variable o constante se usa en varios puntos del código, debe asignarle un nombre que se pueda buscar. Compare:
```java
        for (int j=0; j<34; j++) {
            s
        }
```        
    con:
```java
        int realDaysPerIdealDay = 4;
        const int WORK_DAYS_PER_WEEK = 5;
        int sum = 0;
        for (int j = 0; j < NUMBER_OF_TASKS; j++) {
            int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
            int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
            sum += realTaskWeeks;
        }
```
 En este ejemplo, sum no es un nombre especialmente útil, pero al menos se puede buscar. Se usa una función más extensa, pero comprobará que resulta mucho más fácil buscar WORK_DAYS_PER_WEEK que todas las instancias de 5 y filtrar la lista a los casos con el significado adecuado.

### Evitar "codificaciones"

 No es ideal "codificar" los nombres de variables. No parece razonable que todos los nuevos empleados tengan que aprender otro lenguaje de codificación además del código con el que van a trabajar. Es una carga mental innecesaria a la hora de intentar solucionar un problema. Los nombres codificados resultan impronunciables y suelen escribirse de forma incorrecta.

 Tampoco es necesario añadir prefijos como "m_" a las variables. Compare:
```java
        public class Part {
            private String m_dsc; // La descripción textual
            void setName(String name) {
                m_dsc = name;
            }
        }
```
        con:
```java
        public class Part {
            String description;
            void setDescription(String description) {
                this.description = description;
            }
        }
```
 Además, los usuarios aprenden rápidamente a ignorar el prefijo (o sufijo) y fijarse en la parte con sentido del nombre. Cuanto más código leemos, menos nos fijamos en los prefijos. **En última instancia, los prefijos son un indicio de código antiguo.**

 Sin embargo, en la implementación y las interfaces, la "codificación" es dentro de todo valida. Si tengo que crear una factoría abstracta para formar formas y esta será una interfaz que se implementará por medio de una clase concreta ¿qué nombre se le debe asginar a cada una? Si tengo que codificar uno de los nombres será el de la implementación usano `ShapeFactoryImp` o `CShapeFactory`.

### Evitar asignaciones mentales

 Un contador de bucles puede ser bautizado como j, i o k (pero nunca "l") si su **ambito es muy reducido**. Sin embargo, en otros contextos es un nombre pobre debido a que es un marcador de posición  que el lector debe asignar mentalmente a un concepto real. Una diferencia entre un programador inteligente y un programador profesional es que este último sabe que *la claridad es lo que importa*.

### Nombres de clases

 **El nombre de una clase no debe ser un verbo.**

### Nombres de métodos

 Los métodos en cambio **deben** tener nombres de verbo como ``postPaymen`` o ``deletePage``. Los métodos de acceso, de modificación y los predicados **deben** tener como nombre su valor y usar como prefijo get, set e is.
```java
        string name = employee.getName();
        customer.setName(“mike”);
        if (paycheck.isPosted())
```
 Al sobrecargar constructores, use métodos de factoría estáticos con nombres que describan los argumentos. Refuerce su uso convirtiendo en privados sus constructores correspondientes.

### Use una palabra por concepto evitando los juegos de palabras

 Resulta confuso tener un controlador, un administrador y un control en la misma base de código. ¿Cuál es la diferencia entre DeviceManager y ProtocolController? ¿Por qué no son los dos controladores o administradores? ¿Son controladores? El nombre hace que espere que dos objetos tengan un tipo diferente y clases diferentes. Un léxico coherente es una gran ventaja para los programadores que tengan que usar su código. **De igual manera, evite usar la misma palabra con dos  fines distintos**. Imagine que hay varias clases en las que add crea un nuevo valor sumando o concatenando dos valores existentes. Imagine ahora que crea una nueva clase con un método que añada su parámetro a una colección. ¿Este método debe tener el método add? Parece coherente ya que hay otros muchos métodos add, pero en este caso hay una diferencia semántica, de modo que debemos usar un nombre como insert o append.

### Añadir contexto con sentido

 Algunos nombres tienen significado por sí mismos, pero la mayoría no. Por ello, debe incluirlos en un contexto, en clases, funciones y espacios de nombres con nombres adecuados. Cuando todo lo demás falle, pueden usarse prefijos como último recurso. Imagine que tiene las variables ``firstName``, ``lastName``, ``street``, ``houseNumber``, ``city``, ``state`` y ``zipcode``. Si las combina, es evidente que forman una dirección. Pero si la variable ``state`` se usa de forma aislada en un método, ¿sabría que forma parte de una dirección? Puede añadir contexto por medio de prefijos. Así al menos los lectores comprenderán que estas variables forman parte de una estructura mayor. Evidentemente, es mejor crear la clase Address. De ese modo, incluso el compilador sabrá que las variables pertenecen a un concepto más amplio.
 
### No añadir contexto innecesario

 Los nombres breves suelen ser más adecuados que los extensos, siempre que sean claros. No añada más contexto del necesario a un nombre. Los nombres accountAddress y customerAddress son perfectos para instancias de la clase Address pero no sirven como nombres de clase.

## Conclusión de Robert (nombres)

 La gente teme que al cambiar los nombres otros programadores se quejen.Nosotros no compartimos ese temor y agradecemos los cambios de nombre(siempre que sean a mejor). En muchos casos no memorizamos los nombres de clases y métodos. Usamos herramientas modernas para estos detalles y así poder centrarnos en si el código se lee como frases de un libro. *Seguramente acabará sorprendiendo a alguien cuando cambie los nombres, como puede suceder con cualquier otra mejora del código. No deje que le detenga.*

## Funciones

### Parentesis

 Antes de resumir las propiedades que deben tener las funciones para ser limpias el autor nos da un ejemplo extenso en java de una función de *FitNesse* y nos pide que tratemos de entenderla en 3 minutos (yo le di 5), como seguramente no la entenderemos (vease mi caso) Robert Martin nos da el mismo ejemplo refactorizado. Y seguramente no entenderemos los detalles persé. Sin embargo, en este ejemplo se entiende la **intención de la función** y, no menos importante, esta es **sencilla de leer**. Mostrandonos el poder del clean code.

### Tamaño reducido

 Las **funciones deben tener una longitud aproximada de 20 líneas**. De la misma manera los bloques en instrucciones `if`, `else`, `while` y similares deben tener una línea de longitud. Siendo idoneo que la función invocada en el bloque tenga un nombre descriptivo. Además las funciones no deben tener estructuras anidadas, por ende el nivel de sangrado/identación no debe ser mayor a 2.
 
 **Las funciones de tamaño reducido sólo deben hacer una cosa** y hacerla bien. El problema de esta afirmación es saber qué es **"una cosa"**. Si una función sólo realiza los pasos situados un nivel por debajo del nombre de la función, entonces hace **una cosa**.

 Creamos funciones para descomponer conceptos más amplios, o sea mediante su nombre, en un conjunto de pasos en el siguiente nivel de abstracción (por ejemplo creamos la función de utilidad `allPokemons()` para usarla dentro de la función `getAllPokemons()` que se usará la ruta...)

 Un síntoma de que que una función hace más de una cosa es ver que está dividida en secciones (declaraciones, inicializaciones y filtros). **Las funciones que hacen una cosa** son indivisibles en secciones.

### Un nivel de abstracción por función

 Para que las funciones realicen una cosa es vital asegurarse que las instrucciones se encuentren en el mismo nivel de abstracción (siendo los mas bajos metodos). La mezcla de niveles de abstracción resulta confusa, el lector no sabrá si una determinada expresión es un concepto esencial o un detalle.

 El objetivo es que el código se lea como un texto, de arriba a abajo. Queremos que tras todas las funciones aparezcan las del siguiente nivel de abstracción para poder leer el programa, descendiendo un nivel de abstracción por vez mientras leemos la lista de funciones. **Es lo que se denomina la regla decendente**.

### Instrucciones Switch

 Desafortunadamente no siempre podemos evitar las instrucciones `switch` pero podemos asegurarnos de incluirlas en una lcase de nivel inferior y de no repetirlas recurriendo al polimorfismo. Compare: 
```java
    public Money calculatePay(Employee e) throws InvalidEmployeeType {
        switch (e.type) {
            case COMMISSIONED:
                return calculateCommissionedPay(e);
            case HOURLY:
                return calculateHourlyPay(e);
            case SALARIED:
                return calculateSalariedPay(e);
            default:
                throw new InvalidEmployeeType(e.type);
        }
    }
```
    Con:
```java
    public abstract class Employee {
        public abstract boolean isPayday();
        public abstract Money calculatePay();
        public abstract void deliverPay(Money pay);
    }

    public interface EmployeeFactory {
        public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
    }

    public class EmployeeFactoryImpl implements EmployeeFactory {
        public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
            switch (r.type) {
                case COMMISSIONED:
                    return new CommissionedEmployee(r);
                case HOURLY:
                    return new HourlyEmployee(r);
                case SALARIED:
                    return new SalariedEmployee(r);
                default:
                    throw new InvalidEmployeeType(r.type);
            }
        }
    }
```

 El primer ejemplo tiene varios problemas. Es de gran tamaño y cuando se añadan nuevos tipos de empleados aumentará más. Hace más de una cosa, incumple el *Principio de responsabilidad Unica (SRP)* y el *Principio de abierto/cerrado (OCP)* y, el peor e los problemas es que hay un numero ilimitado de funciones que tienen la misma estructura. Podríamos tener:
```java
    isPayday(Employee e, Date date),
```
    o
```java
    deliverPay(Employee e, Date date),
```
 Hasta el infinito
 
 Mi regla para las instrucciones `switch`  es que se pueden tolerar si sólo aparecen una vez, se usan para crear objetos polimórficos y se cultan tras una relación de herencia para que el resto del sistema no las pueda ver. Evidentemente, cada caso es diferente y en ocasiones se puede incumplir una o varias partes de esta regla.

### Usar nombres descriptivos

 No hay que olvidar el valor de los nombres correctos. Para alcanzar esto gran parte del esfuerzo se basa en seleccionar nombres adecuados para pequeñas **funciones que hacen una cosa**. Cuanto más concreta y reducida sea la función, más sencillo será elegir un nombre descriptivo. No tema los nombres extensos ni a dedicar tiempo a elegir un buen nombre. Un nombre descriptivo extenso es mucho mejor que uno breve pero enigmático. Use una convención de nombres que permita leer varias palabras en los nomb
 res de las funciones y use esas palabras para asignar a la función un nombre que describa su cometido. El uso de nombres descriptivos clarifica el diseño de modulos y permite mejorarlos. Sea coherente con los nombres. Use las mismas frases, sustantivos y verbos en estos. **La estructura y secuencia de estos deben contar una historia**

### Argumentos de funciones

 El mejor número de argumentos en una función es cero. Lo siguen uno (monádico) o dos (diádico) argumentos. Es recomendable evitar tres argumentos (triádico). Y más de tres argumentos (poliádico) requiere una justificación especial y no es habitual.

 Los argumentos son complejos ya que requieren un gran poder conceptual. El argumento se encuentra en un nivel de abstracción diferente que el nombre de la función y nos obliga a conocer un detalle que no es especialmente importante en ese momento.

 Los argumentos son aun más complicados desde el punto de vista de las pruebas. Probar una función sin argumentos es fácil y sencillo. Una con uno no es demasiado difícil. Con dos el problema se acomplejiza y con más de dos probar cada combinación es todo un reto.

 **-Funciones Monádicas (un argumento)**: Hay dos motivos para pasar un argumento a una función. Realizar una pregunta sobre el argumento o que lo transforme en otra cosa y lo devuelva. Los lectores esperan estos dos usos cuando ven una función monádica. Debe elegir nombres que realicen la distinción con claridad y usar las formas en un contexto coherente. También puede usarse una funcion Monádica para manejar eventos. Donde esta altere el estado del sistema en lugar de devolver un valor. Es importante remarcar que hace la función mediante su nombre para no confundir al lector (evento, consulta sobre el argumento, transformación del argumento).
 
  El uso de booleanos como argumento es desaconsejable ya que complica la comprensión y, por ende, el mantenimiento del código. Además, es un síntoma de que la función hace más de una cosa (si true esto, si false aquello). Es aconsejable dividir la función para cada caso específico.

 **-Funciones Diádicas (dos argumentos)**: Una función con dos argumentos es más difícil de entender que una Monádica. Por ejemplo es más fácil entender: `writeField(name)` que `writeField(name, outputStream)`. Ambos se entienden pero la aparicion de `outputStream` confunde y nos puede llevar a ignorarlo. *Esto genera problemas, ya que las partes que ignoramos son las que esconden errores*. En ocaciones necesitamos dos argumentos. por ejemplo `Point p = new Point(0, 0)` es razonable y entenible. Sin embargo, en esta ocación son componentes ordenados de un mismo valor a diferencia de `name` y `outpuStream` que carecen de cohesión y orden natural. Un problema en las funciones Diádicas es ese, su orden. El orden real y esperado es una convención que se adquiere gracias a la práctica. Si bien las funciones diádicas no son el mal y tendremos que usarlas es conveniente convertirlas en monádicas. Por ejemplo haciendo que el método `writeField` sea un miembro de `outputStream` para usar `outputStream.wrteField(name)` o incluyendolos en un objeto como se explica a continuación.

 **-Funciones Triadas (tres argumentos)**: Una función Triada es sin duda mucho más difícil de entender que las Diádicas y Monádicas. Los problemas a la hora de ordenar, ignorar o detenerse en los argumentos aumentan. Piense antes de crear una Triada. Piense si vale la pena. Pueden ser necesarias rara vez, como comparando valores de coma flotante con tolerancia. Siendo utiles para abordar esta necesidad específica. 

 Cuando una función parece necesitar dos o más argumentos, es probable que alguno de ellos se incluya en una clase propia. Revise los siguientes ejemplos:
```java
    Circle makeCircle (double x, double y, double radius);
```
```java
    Circle makeCircle(Point center, double radius);
```
 La reducción de argumentos mediante la creación de objetos puede parecer una trampa pero no lo es. Cuando se pasan variables de forma conjunta es probable que formen o puedan formar parte de un concepto que se merece un nombre propio.

### Verbos y palabras clave

 La selección de nombres correctos mejora la explicación de su cometido, así como el orden y el cometido de los argumentos. En formato monádico la función y el argumento deben formar un par de verbo y sustantivo. Ejemplo: `write(name)` resulta comprensible. Sea lo que sea `name` se escribe. Con este formato codificamos los nombres de los argumentos en el nombre de la función. Ejemplo: `assertExpectedEqualsActual(expected, actual)` mitiga el problema de tener que recordar/revisar el orden de los argumentos.

### Sin efectos secundarios

 Los efectos secundarios son mentiras que debemos evitar ya que rompemos la regla de **hacer sólo una cosa**. Supongamos que su función promete hacer una cosa, pero tambien hace varias otras ocultas. En ocaciones realiza cambios inesperados en las variables de su propia clase. En ocasiones convierte las variables pasadas a la función en otras o transforma/modifica elementos globales del sistema. **En cualquier caso se suelen provocar extrañas combinaciones temporales y dependencias de orden**.
 
 Las combinaciones temporales son confusas, en especial cuando se ocultan como "efecto secundario". *Si tiene que realizar una combinación temporal hágalo pero déjelo claro en el nombre de la función*

### Argumentos de salida

 Veamos el siguiente ejemplo:

 `appendFooter(s);`

 ¿Está función añade `s` al final de algo? ¿O añade el final de algo a `s`? ¿`s` es una entrada o una salida? Lo sabemos al ver la firma de la función:

 `public void appendFooter(StringBuffer report)`
 
 Esto lo aclara todo, pero para ello hay que comprobar la declaración de la función. Es una pausa cognitiva y debe evitarse, como todo lo que obligue al lector a comprobar la firma de la función.

 **Por lo general, los argumentos de salida deben evitarse.** Si su función tiene que cambiar el estado de un elemento, haga que cambie el estado de su objeto contenedor.

### Separación de consultas de comando

 Las funciones hacer algo o responder a algo, nunca ambas cosas. Su función debe cambiar el estado de un objeto o devolver información sobre el mismo, pero ambas operaciones juntas causan confusión. Por ejemplo:

```java
    public boolean set(String attribute, String value)
```

 Esta es una función diádica que establece el `value` de un `attribute` y devuelve `true` en caso de éxito o `false` si el atributo no existe. El problema viene ahora.

```java
    if(set("username", "unclebob"))
``` 

 El lector debe revisar la firma de la función para entender correctamente la instrucción. Podríamos cambiar el nombre de la funcion `set` por `setAndCheckIfExist`. Pero la legilibilidad de la instrucción `if` no mejoraría sustancialmente. La verdadera solución es separar el comando de la consulta para evitar la ambigüedad y confusión.

```java
    if (attributeExists(“username”)) {
        setAttribute(“username”, “unclebob”);
        …
    }
```

### Mejor excepciones que devolver códigos de error

 Devolver códigos de error es un incumplimiento de la separación de comandos de consulta.

```java
    if (deletePage(page) == E_OK)
```
 No padece la confusión entre verbo y adjetivo, pero genera estructuras anidadas. Al devolver un código de error se crea un problema: el invocador debe procesar el error de forma inmediata. Ejemplo:

```java
    if (deletePage(page) == E_OK) {
        if (registry.deleteReference(page.name) == E_OK) {
            if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
                logger.log(“page deleted”);
            } else {
                logger.log(“configKey not deleted”);
            }
        } else {
            logger.log(“deleteReference from registry failed”);
        }
    } else {
        logger.log(“delete failed”);
        return E_ERROR;
    }

```

 Por otra parte, si se usan excepciones en lugar de códigos de error, el código de procesamiento del error se puede separar del código de ruta y se puede simplificar:

```java
 try {
deletePage(page);
registry.deleteReference(page.name);
configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
logger.log(e.getMessage());
}

```

### Extraer bloques Try/Catch

 Los bloques `try/catch` no son muy atractivos. Confunden la estructura del código y mezclan el procesamiento de errores con el normal. Lo conveniente es **extraer el cuerpo de los bloques `try` y `catch` en funciones individuales** Ejemplo:

 ```java
    public void delete(Page page) {
        try {
            deletePageAndAllReferences(page);
        }
        catch (Exception e) {
            logError(e);
        }
    }

    private void deletePageAndAllReferences(Page page) throws Exception {
        deletePage(page);
        registry.deleteReference(page.name);
        configKeys.deleteKey(page.name.makeKey());
    }

    private void logError(Exception e) {
        logger.log(e.getMessage());
    }
 ```

### El procesamiento de errores es una cosa

 Las funciones sólo deben hacer una cosa y el procesamiento de errores es un ejemplo. Por ende, una funcion que procese errores no debe hacer nada más. Esto implica que, si una función incluye una instrucción `try` debe ser la primera de la función y no debe haber nada más despues de los bloques `catch/finally`

### No repetirse

 No es fácil detectar algoritmos que se repiten ya que las instancias pueden mezclarse con otro código. Pero la duplicación es un problema ya que aumenta el tamaño del código y requerirá una modificación de N (cantidad de veces que se repite el algoritmo) veces si alguna vez se cambia el algoritmo. Además se multiplica por N el riesgo de errores. La duplicación o repetición puede ser la raíz de todos los problemas del sofware. Existen numerosos principios y prácticas para controlar o eliminar esto (podría tirar alguno, pero tocará investigar).

### Programación estructurada

 Dijkstra afirma que todas las funciones y todos los bloques de una función deben tener una entrada y una salida. Estas reglas implican que sólo debe haber una instrucción `return` en una función, que no debe haber instrucciones `break` o `continue` en un bucle y nunca, bajo ningún concepto, debe haber instrucciones `goto`.

 El verdadero poder de este "mantra" reside en aplicarlo en funciones de gran tamaño. Por tanto al escribir funciones reducidas una instrucción `return`, `break` o continue no hará daño alguno y en ocasiones puede resultar más expresiva que la regla de una entrada y una salida. Por otra parte, goto sólo tiene sentido en funciones de gran tamaño y debe evitarse.

### Cómo crear este tipo de funciones

 La creación de software es como cualquier otro proceso creativo. Al escribir un informe o artículo, primero se estructuran las ideas y después el mensaje hasta que se lea bien. El primer borrador puede estar desorganizado o desprolijo, de modo que lo retoca y mejora hasta que se lea de forma adecuada.

 Cuando creo funciones, suelen ser extensas y complicadas, con abundancia en identación y bucles anidados. Con extensas listas de argumentos, nombres arbitrarios y código duplicado. Pero también cuento con **pruebas unitarias** que abarcan todas y cada una de las líneas de código. 

 Por tanto, retoco el código, divido las funciones, cambio los nombres y elimino los duplicados. Reduzco los métodos y los reordeno. En ocaciones elimino clases enteras mientras mantengo las pruebas.

 **Al final, y sólo al final, consigo que las funciones cumplan las reglas detalladas en este capítulo. No las escribo así al comenzar y dudo que alguien pueda hacerlo.**

 *El poeta francés Paul Valery afirma que un poema no se acaba nunca, que tiene que retocarse continuamente, y que dejar de trabajar en el poema es señal de abandono*.

### Conclusión

 Los programadores experimentados piensan en los sistemas como en historias que contar, no como en programas que escribir. Recurren a las prestaciones del lenguaje de programación seleccionado para crear un lenguaje expresivo mejor y más completo que poder usar para contar esa historia. Parte de ese lenguaje es la jerarquía de funciones que describen las acciones que se pueden realizar en el sistema. Dichas acciones se crean para usar el lenguaje de dominio concreto que definen para contar su pequeña parte de la historia.

 En este capítulo hemos visto la mecánica de la creación de funciones correctas. Si aplica estas reglas, sus funciones serán breves, con nombres correctos, y bien organizadas, pero **no olvide que su verdadero objetivo es contar la historia del sistema y que las funciones que escriba deben encajar en un lenguaje claro y preciso que le sirva para contar esa historia**.

### Al final, y sólo al final (SetupTearDownIncluder)

 Durante el capítulo de Funciones, Robert, nos da un ejemplo de forma recurrente, SetupTearDownIncluder. Este ejemplo va evolucionando poco a poco hasta que, *al final*, llega a la cuspide a continuación.

```java
package fitnesse.html;
import fitnesse.responders.run.SuiteResponder;
import fitnesse.wiki.*;

public class SetupTeardownIncluder {
    private PageData pageData;
    private boolean isSuite;
    private WikiPage testPage;
    private StringBuffer newPageContent;
    private PageCrawler pageCrawler;

    public static String render(PageData pageData) throws Exception {
        return render(pageData, false);
    }

    public static String render(PageData pageData, boolean isSuite) throws Exception {
        return new SetupTeardownIncluder(pageData).render(isSuite);
    }

    private SetupTeardownIncluder(PageData pageData) {
        this.pageData = pageData;
        testPage = pageData.getWikiPage();
        pageCrawler = testPage.getPageCrawler();
        newPageContent = new StringBuffer();
    }

    private String render(boolean isSuite) throws Exception {
        this.isSuite = isSuite;
        if (isTestPage())
            includeSetupAndTeardownPages();
        return pageData.getHtml();
    }

    private boolean isTestPage() throws Exception {
        return pageData.hasAttribute("Test");
    }

    private void includeSetupAndTeardownPages() throws Exception {
        includeSetupPages();
        includePageContent();
        includeTeardownPages();
        updatePageContent();
    }

    private void includeSetupPages() throws Exception {
        if (isSuite)
            includeSuiteSetupPage();
        includeSetupPage();
    }

    private void includeSuiteSetupPage() throws Exception {
        include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
    }

    private void includeSetupPage() throws Exception {
        include("SetUp", "-setup");
    }

    private void includePageContent() throws Exception {
        newPageContent.append(pageData.getContent());
    }

    private void includeTeardownPages() throws Exception {
        includeTeardownPage();
        if (isSuite)
            includeSuiteTeardownPage();
    }

    private void includeTeardownPage() throws Exception {
        include("TearDown", "-teardown");
    }

    private void includeSuiteTeardownPage() throws Exception {
        include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
    }

    private void updatePageContent() throws Exception {
        pageData.setContent(newPageContent.toString());
    }

    private void include(String pageName, String arg) throws Exception {
        WikiPage inheritedPage = findInheritedPage(pageName);
        if (inheritedPage != null) {
            String pagePathName = getPathNameForPage(inheritedPage);
            buildIncludeDirective(pagePathName, arg);
        }
    }

    private WikiPage findInheritedPage(String pageName) throws Exception {
        return PageCrawlerImpl.getInheritedPage(pageName, testPage);
    }

    private String getPathNameForPage(WikiPage page) throws Exception {
        WikiPagePath pagePath = pageCrawler.getFullPath(page);
        return PathParser.render(pagePath);
    }

    private void buildIncludeDirective(String pagePathName, String arg) {
        newPageContent
            .append("\n!include ")
            .append(arg)
            .append(" .")
            .append(pagePathName)
            .append("\n");
    }
}

```

## Comentarios

 No hay nada más útil que un comentario bien colocado. No hay nada que colapse más un módulo que comentarios dogmáticos innecesarios. No hay nada más dañino que un comentario antiguo que propague mentiras y desinformación. 

 Los comentarios son, en el mejor de los casos, un mal necesario. Si los lenguajes de programación fueran más expresivos o si pudiéramos dominarlos para expresar nuestras intenciones, no necesitariamos tantos (o ningún) comentarios. 
 
 **El uso correcto de los comentarios radica en compensar nuestra incapacidad para expresarnos en el código. Los comentarios son fallos que admitimos y debemos usarlos porque no siempre sabemos cómo expresarnos sin ellos.**

 Cuando tenga que escribir un comentario, piense si no existe otra forma de expresarse en el código. **Los comentarios tienden a mentir**. Cuando más antiguo un comentario y más se aleja del código que "describe" mayor es la probabilidad de que sea equivocado y confunda al lector o lo deje sesgado. Esto debido a que los programadores no los pueden mantener (o no lo hacen a menudo).

 El código evoluciona y cambia. Los fragmentos cambian de lugar, se bifurcan, etc. Desafortunadamente, los comentarios no siempre siguen el ritmo. Convirtiendose en huérfanos sin precisión alguna.

 Podríamos tratar que los progrmadores mantengan los comentarios actualizados. Pero esa energía debería invertirse en **crear código claro y exresivo que no necesite comentario alguno**.

 **La verdad sólo se encuentra en un punto: el código**. Sólo el código puede contar lo que hace. Es la única fuente de información precisa.

### Los comentarios no compensan código incorrecto

 Una de las principales motivaciones para crear comentarios es el código incorrecto. Creamos un módulo y sabemos que es confuso y está desorganizado. Sabemos que es un desastre y entonces decidimos comentarlo. Error. Mejor límpielo.

### Comentarios de calidad

 Algunos comentarios son necesarios o beneficiosos. A continuación veremos ejemplos. Sin embargo, recuerde que el único comentario realmente bueno es el que no tiene que escribir.

 - **Comentarios legales:** 
 En ocasiones, nuestros estándares corporativos de creación de código nos obligan a crear determinados comentarios por motivos legales. Por ejemplo, los comentarios de derechos de autor son necesarios y deben incluirse al inicio de cada archivo.

 Este tipo de comentarios no deben ser contratos ni tomos legales. Siempre que sea posible, haga referencia a una licencia estándar o a otro documento externo en lugar de incluir todos los términos y condiciones en el comentario.

 - **Comentarios informativos:** 
 En ocasiones es útil proporcionar información básica con un comentario. Por ejemplo, el siguiente comentario explica el valor devuelto por un método abstracto:
 ```java
    // Devuelve una instancia del elemento Responder probado.
    protected abstract Responder responderInstance();

 ```

 - **Explicar la intención:**
 En ocaciones, un comentario es algo más que información útil sobre la implementacion y proporciona la intención de una desición.

 - **Clarificación:**
 En ocaciones basta con traducir el significado de un argumento o valor a algo más legible. Por lo general, conviene buscar la forma de que el argumento o el valor devuelto sea claro por si mismo. Pero cuando forma parte de una biblioteca estándar o de código que no se puede alterar un comentario aclarativo puede ser útil.

 Sin embargo, tambien **existe el riesgo de que un comentario declarativo sea incorrecto**.  Por ello, antes de escribir estos comentarios, asegúrese de que no hay una solución mejor y también de que sean precisos.

 - **Advertir de consecuencias:**
 En ocasiones es muy útil advertir a otros programadores de determinadas consecuencias.

 - **Comentarios "TODO":**
 En ocasiones conviene usar notas con forma de comentarios `//TODO`. Ejemplo:
 ```java
    // TODO-MdM no son necesarios
    // Esperamos que desaparezca en el modelo definitivo
 ```
 Los `TODO` son tareas que el programador piensa que debería haber hecho pero no es así. Pueden ser un recordatorio para eliminar una función obsoleta o una petición para resolver un problema.

 Suelen ser una solicitud para buscar un nombre más adecuado o para realizar un cambio de agenda de un evento planeado. Sea lo que sea, no es excusa para mantener código incorrecto en el sistema. Examínelos y elimine todos los que pueda.

 - **Amplificación:**

 Se puede usar un comentario para amplificar la importancia de algo que, en caso contrario, pareceria irrelevante.
```java
    String listItemContent = match.group(3).trim();
    // el recorte es importante. Elimina los espacios iniciales
    // que harían que el elemento se reconociera como
    // otra lista.
    new ListItemWidget(this, listItemContent, this.level + 1);
    return buildList(text.substring(match.end()));
``` 

### Comentarios incorrectos

 Muchos comentarios pertenecen a esta categoría. Suelen ser excusas de código pobre o justificaciones de decisiones insuficientes, algo así *como si el programador se hablara a sí mismo.* A continuación veremos algunos ejemplos.

 - **Balbucear:**
 Añadir un comentario sin razón es un error. Si decide escribir un comentario, tómese el tiempo necesario para asegurarse de que sea el mejor que puede redactar. Caso contrario puede generar confusión al lector. Cualquier comentario que le obligue a buscar su significado en otro módulo ha fallado en su intento de comunicación, *no merece los bits que consume* (+1 punto de drama).

 - **Comentarios redundantes:**
 Si el comentario no es más informativo que el código entonces ha fallado fallado en su intento de comunicación. Idem si el comentario no se justifica, o es más fácil de leer que el código.

 - **Comentarios confusos:**
 En ocaciones un programador realiza una afirmación en sus comentarios que no es del todo precisa (ya sea equivoca o ambigua). Esto es particularmente peligroso si el lector decide creer en el comentario en lugar de consultar la verdad.

 - **Comentarios obligatorios:**
 Es una locura tener una regla que afirma que todas las funciones o variables deben tener un comentario. Este tipo de comentarios ensucian el código, generando confusión y desorganización. 

 - **Comentarios periódicos:**

 En ocaciones, se añade un comentario al inicio de un módulo cada vez que se edita. Estos comentarios acumulan una surte de registro de todos los cambios registrados. En su momento un sistema de control de códugo fuente carecía. Pero en la actualidad estas entradas son elementos sobrantes que complican innecesariamente los módulos, deben eliminarse totalmente.

 - **Comentarios sobrantes:**
 En ocaciones vemos comentarios que simplemente sobran. Restan importancia a lo evidente y no ofrecen información nueva. Estos comentarios son tan inservibles, que aprendemos a ignorarlos. Al leer el código, la vista los salta. Y, lo peor, es que empiezan a mentir cuando el código que los rodea cambia. **Cambie la tentación de crear elementos sobrantes por la determinación de limpiar su código. Mejorará como programador y será más fácil.**

 - **No usar comentarios si se puede usar una función o una variable:**
 Si el código puede ser mejorado para ser más legible, no pierda tiempo escribiendo comentarios.

 - **Marcadores de posición:**
 En ocasiones los programadores marcan una determinada posición en un archivo. Ejemplo:
 ```java
 // Acciones //////////////////////
 ```
 Son escasas las ocasiones en las que tiene sentido agrupar funciones bajo esta estructura. Por lo general deben eliminarse o usarlas muy esporádicamente. Si las usa en exceso, eventualmente serán ignoradas (así, igual que ella).

 - **Comentarios de llave de cierre:**
 En ocasiones, los programadores incluyen comentarios especiales en llaves de cierre. Ejemplo:
 ```java
    public class wc {
        public static void main(String[] args) {
            BufferedReader in = new BufferedReader(new InputstreamReader(System.in));
            String line;
            int lineCount = 0;
            int charCount = 0;
            int wordCount = 0;
            try {
                while ((line = in.readLine()) != null) {
                    lineCount++;
                    charCount += line.length();
                    String words[] = line.split(“\\W”);
                    wordCount += words.length;
                } //while
                System.out.println(“wordCount = ” + wordCount);
                System.out.println(“lineCount = ” + lineCount);
                System.out.println(“charCount = ” + charCount);
            } // try
            catch (IOException e) {
                System.err.println(“Error: ” + e.getMessage());
            } //catch
        } //main
    }
 ```
 Aunque puede tener sentido en funciones extensas con estructuras anidadas, únicamente estorba a las funciones encapsuladas y de pequeño tamaño que nos gustan. Por ello, si desea marcar sus llaves de cierre purebe en su lugar reducir el tamaño de sus funciones.

 - **Asignacion y menciones:**
 Estos comentarios ya no son necesarios al tener controladores de código fuente actualmente. Puede pensar que estos comentarios son útiles y que ayudan a otros a hablar sobre el código, pero en realidad sobreviven durante años y cada vez son menos precisos y relevantes. En su lugar, revise el sistema de control.

 - **Código comentado:** 
 No hay nada más terrible que el código comentado. Los lectores que vean código comentado no tendrán el valor de borrarlo. Pensarán que está ahí por algo y que es demasiado valioso para borrarlo. El código comentado se acumula comno los sedimientos en una botella de vino malo (en mi opinión, al tío bob le sobra clase pero le falta calle). Nuevamente, en el pasado el código comentado pudo ser útil pero en la actualidad no tiene lugar.

 - **Comentarios HTML:**
 Son una aberración, evítelos. (No dice exactamente eso, pero es con lo que yo me quedo).

 - **Información no local:**
 Si tiene que escribir un comentario, asegúrese de que describa el código que le rodea. No ofrezca información global del sistema en el contexto de un comentario local.

 - **Demasiada información**
 No incluya en sus comentarios reflexiones historicas ni irrelevantes descripciones en detalle. Ejemplo: 

 ![IMG Example](<https://www.um.es/metadatosvsdatos/wp-content/uploads/2022/12/mucho-texto.jpg>)

 - **Conexiones no evidentes:**
 La conexión entre un comentario y el código debe ser evidente. El lector debe entender a que se refiere.

 - **Encabezado de función:**
 Las funciones breves apenas necesian explicación. Un nombre bien elegido debería ser suficiente. Caso contrario, refactorice.

## Formato

 Cuando los usuarios miran entre bastidores, queremos que queden impresionados por el atractivo, la coherencia y atención al detalle que les dejamos. Queremos que se aprecie que se trata de un trabajo profesional. Si ven un espagueti amorfo que parece escrito por 3 o 4 ebrios, es probable que piensen que sucederá lo mismo en otros aspectos del proyecto.

 Debemos preocuparnos por el formato del código. Se debe acordar una serie de reglas que todos los miembros puedan cumplir. Es recomendable usar herramientas automatizadas que se encarguen de esto también.

### La función del formato

 El formato es importante debido a que es un pilar principal en un desarrollador profesional siendo que el formato se basa en la comunicación.

 Puede que piense que conseguir que algo funcione es la principal preocupación, cambie de idea. La funcionalidad que se crea hoy puede cambiar en la siguiente versión, pero la legibilidad de su código afectará profundamente su mantenimiento. 

 El estilo y la disciplina sobrevivirán aunque el código no lo haga.

### Formato vertical

 Piense en un artículo de periódico bien escrito. En la parte superior espera un titular que indique de qué se trata la historia y le permita determinar si quiere leerlo o no. El primer párrafo ofrece una sinopsis de la historia, oculta los detalles y muestra conceptos generales. Al avanzar la lectura, aumentan los detalles junto con todas las fechas, nombres, citas y otros elementos. 
 
 Un archivo de código debe ser como un artículo de periódico. El nombre debe ser sencillo pero claro. Por sí mismo, debe bastar para indicarnos si estamos o no en el módulo correcto. Los elementos superiores del archivo deben proporcionar conceptos y algoritmos de nivel superior. Los detalles deben aumentar según avanzamos, hasta que en la parte final encontremos las funciones de nivel inferior del archivo. 
 
 Un periódico se compone de varios artículos, algunos muy reducidos y otros de gran tamaño. No hay muchos que ocupen toda la página con texto, para que el periódico sea manejable. Si el periódico fuera un único y extenso texto con una aglomeración desorganizada de hechos, fechas y nombres, no lo leeríamos.

¿Que tamaño debe tener un archivo en cuanto a líneas de código? Robert nos propone que el límite debe estar entre las 200 y 500 líneas de código.

### Apertura vertical entre conceptos

 La gran mayoría del código se lee de izquierda a derecha y de arriba a abajo. Cada línea representa una expresión o una cláusula y cada grupo de líneas un *pensamiento completo*. Estos pensamientos deben separarse mediante líneas en blanco. Compare :
```java
    package fitnesse.wikitext.widgets;
    import java.util.regex.*;
    public class BoldWidget extends ParentWidget {
        public static final String REGEXP = “‘‘‘.+?’’’”;
        private static final Pattern pattern = Pattern.compile (“‘‘‘(.+?)’’’”,
            Pattern.MULTILINE + Pattern.DOTALL
        );
        public BoldWidget(ParentWidget parent, String text) throws Exception {
            super(parent);
            Matcher match = pattern.matcher(text);
            match.find();
            addChildWidgets(match.group(1));
        }
        public String render() throws Exception {
            StringBuffer html = new StringBuffer(“<b>”);
            html.append(childHtml()).append (“</b>”);
            return html.toString();
        }
    }
```
con:
```java
    package fitnesse.wikitext.widgets;
    import java.util.regex.*;

    public class BoldWidget extends ParentWidget {
        public static final String REGEXP = “‘‘‘.+?’’’”;
        private static final Pattern pattern = Pattern.compile (“‘‘‘(.+?)’’’”,
            Pattern.MULTILINE + Pattern.DOTALL
        );

        public BoldWidget(ParentWidget parent, String text) throws Exception {
            super(parent);
            Matcher match = pattern.matcher(text);
            match.find();
            addChildWidgets(match.group(1));
        }

        public String render() throws Exception {
            StringBuffer html = new StringBuffer(“<b>”);
            html.append(childHtml()).append (“</b>”);
            return html.toString();
        }
    }
```

 La diferencia es una ligera apertura vertical. Pero en el primer ejemplo las ideas se amalgaman perdiendo legibilidad mientras que el segundo es más amigable.

### Densidad vertical

 Si la apertura separa los conceptos, la densidad vertical implica asociasiones. Por tanto, las líneas de código con una relación directa deben aparecer verticalmente densas (juntas papi, juntas).

### Distancia vertical 
 
 Los conceptos relacionados entre sí deben mantenerse juntos. Así evitamos que el lector tenga que hacer una pausa para buscar e investigar significados. Sin embargo, **esta regla no aplica a conceptos de archivos independientes**. Por lo tanto, no se debe separar conceptos relacionados en archivos diferentes a menos que tenga un motivo de peso. De hecho es uno de los motivos por lo que se debe evitar el uso de variables protegidas.

 Para los conceptos relacionados que pertenecen al mismo archivo, su separación vertical debe medir su importancia con respecto a la legibilidad del otro. Debe evitar que el lector deambule entre archivos y clases.

 - Declaración de variables

 Las variables deben declararse de forma más aproximada a su uso. Como las funciones son muy breves, la s variables locales debener aparecer en la parte superior de cada función.

 - Variables de instancia

 Las variables de instancia deben declararse en la parte superior de la clase. Esto no debe aumentar la distancia vertical de las variables, ya que en una clase bien diseñada se usan muchos/todos sus métodos.

 - Funciones dependientes

 Si una función invoca otra, deben estar verticalmente próximas, y la función de invocación debe estar por encima de la invocada siempre que sea posible. De este modo el programa fluye con normalidad. Si la convención se sigue de forma apropiada, los lectores sabrán que las definiciones de la función aparecen después de su uso.

 - Afinidad conceptual

 Determinados conceptos de código deben estar próximos a otros ya que tienen una afinidad conceptual concreta. Cuanto mayor sea esta afinidad, menor distancia vertical debe existir entre ellos. Esta afinidad puede estar basada en una dependencia directa, como cuando una función invoca a otra,