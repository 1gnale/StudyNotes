El Patrón: Puertos y Adaptadores (''Estructura de Objetos'')
Nombre alternativo: ''Puertos y Adaptadores''
Nombre alternativo: ''Arquitectura Hexagonal''
Propósito
Permitir que una aplicación pueda ser conducida tanto por usuarios, programas, pruebas automatizadas o scripts por lotes, y ser desarrollada y probada de manera aislada respecto a sus dispositivos y bases de datos de tiempo de ejecución eventual.

Cuando cualquier controlador desea utilizar la aplicación en un puerto, envía una solicitud que es convertida por un adaptador específico para la tecnología del controlador en una llamada de procedimiento o mensaje utilizable, el cual pasa al puerto de la aplicación. La aplicación desconoce completamente la tecnología del controlador. Cuando la aplicación tiene algo que enviar, lo hace a través de un puerto a un adaptador, que crea las señales apropiadas necesarias para la tecnología receptora (humana o automatizada). La aplicación tiene una interacción semánticamente sólida con los adaptadores en todos sus lados, sin conocer realmente la naturaleza de las entidades al otro lado de los adaptadores.

Motivación
Uno de los grandes problemas de las aplicaciones de software a lo largo de los años ha sido la infiltración de la lógica de negocio en el código de la interfaz de usuario. Esto provoca tres problemas principales:

Primero, el sistema no puede ser probado fácilmente con suites de pruebas automatizadas porque parte de la lógica que necesita ser probada depende de detalles visuales que cambian frecuentemente, como el tamaño de los campos y la ubicación de los botones.
Por la misma razón, se vuelve imposible cambiar de un uso dirigido por humanos del sistema a un sistema ejecutado por lotes.
También por la misma razón, se vuelve difícil o imposible permitir que el programa sea dirigido por otro programa cuando esto se vuelve atractivo.
La solución intentada, repetida en muchas organizaciones, es crear una nueva capa en la arquitectura, con la promesa de que esta vez realmente y verdaderamente no se colocará lógica de negocio en la nueva capa. Sin embargo, al no tener un mecanismo para detectar cuando se viola esa promesa, la organización descubre unos años después que la nueva capa está abarrotada de lógica de negocio y el antiguo problema ha reaparecido.

Imagina ahora que ''cada'' funcionalidad que ofrece la aplicación estuviera disponible a través de una API (interfaz de programación de aplicaciones) o llamada a función. En esta situación, el departamento de pruebas o de control de calidad puede ejecutar scripts de pruebas automatizadas contra la aplicación para detectar cuando alguna nueva codificación rompe una función que anteriormente funcionaba. Los expertos en negocios pueden crear casos de prueba automatizados, antes de finalizar los detalles de la GUI, que indiquen a los programadores si han hecho bien su trabajo (y estos tests se convierten en los que ejecuta el departamento de pruebas). La aplicación puede ser desplegada en modo 'sin cabeza', de modo que solo esté disponible la API, y otros programas pueden hacer uso de su funcionalidad, simplificando el diseño global de suites de aplicaciones complejas y permitiendo también que las aplicaciones de servicio negocio a negocio se utilicen entre sí sin intervención humana a través de la web. Finalmente, las pruebas automáticas de regresión de funciones detectan cualquier violación de la promesa de mantener la lógica de negocio fuera de la capa de presentación. La organización puede detectar y corregir la fuga de lógica.

Existe un problema similar interesante en lo que normalmente se considera "el otro lado" de la aplicación, donde la lógica de la aplicación se vincula a una base de datos externa u otro servicio. Cuando el servidor de la base de datos se cae o se somete a una reestructuración o reemplazo significativo, los programadores no pueden trabajar porque su trabajo está vinculado a la presencia de la base de datos. Esto causa costos de demora y frecuentemente malos sentimientos entre las personas involucradas.

No es obvio que los dos problemas estén relacionados, pero hay una simetría entre ellos que aparece en la naturaleza de la solución.

Naturaleza de la Solución
Tanto los problemas del lado del usuario como del lado del servidor son causados en realidad por el mismo error en el diseño y la programación: la entrelazamiento entre la lógica de negocio y la interacción con entidades externas. La asimetría que se debe explotar no es entre los lados 'izquierdo' y 'derecho' de la aplicación, sino entre el 'interior' y el 'exterior' de la aplicación. La regla a seguir es que el código relacionado con la parte 'interior' no debe filtrarse hacia la parte 'exterior'.

Al remover cualquier asimetría izquierda-derecha o arriba-abajo por un momento, vemos que la aplicación se comunica a través de 'puertos' con agencias externas. La palabra "puerto" pretende evocar los 'puertos' en un sistema operativo, donde cualquier dispositivo que cumpla con los protocolos de un puerto puede ser conectado a él; y los 'puertos' en dispositivos electrónicos, donde nuevamente, cualquier dispositivo que se ajuste a los protocolos mecánicos y eléctricos puede ser conectado.

El protocolo para un puerto está determinado por el propósito de la conversación entre los dos dispositivos.
El protocolo toma la forma de una interfaz de programa de aplicación (API).

Para cada dispositivo externo hay un 'adaptador' que convierte la definición de la API a las señales necesarias para ese dispositivo y viceversa. Una interfaz gráfica de usuario o GUI es un ejemplo de un adaptador que mapea los movimientos de una persona a la API del puerto. Otros adaptadores que se ajustan al mismo puerto son arneses de prueba automatizados como FIT o Fitnesse, controladores por lotes y cualquier código necesario para la comunicación entre aplicaciones en toda la empresa o en la red.

En otro lado de la aplicación, la aplicación se comunica con una entidad externa para obtener datos. El protocolo típicamente es un protocolo de base de datos. Desde la perspectiva de la aplicación, si la base de datos se mueve de una base de datos SQL a un archivo plano o cualquier otro tipo de base de datos, la conversación a través de la API no debería cambiar. Los adaptadores adicionales para el mismo puerto incluyen así un adaptador SQL, un adaptador de archivo plano y, lo más importante, un adaptador a una base de datos "ficticia", una que reside en la memoria y no depende de la presencia de la base de datos real en absoluto.

Muchas aplicaciones tienen solo dos puertos: el diálogo del lado del usuario y el diálogo del lado de la base de datos. Esto les da una apariencia asimétrica, lo que hace que parezca natural construir la aplicación en una arquitectura apilada de una, tres, cuatro o cinco capas unidimensionales.

Hay dos problemas con estos dibujos. Primero y más grave, las personas tienden a no tomar en serio las "líneas" en el dibujo en capas. Permiten que la lógica de la aplicación se filtre a través de los límites de las capas, causando los problemas mencionados anteriormente. En segundo lugar, puede haber más de dos puertos para la aplicación, por lo que la arquitectura no encaja en el dibujo unidimensional en capas.

La arquitectura hexagonal, o de puertos y adaptadores, resuelve estos problemas al notar la simetría en la situación: hay una aplicación en el interior que se comunica a través de algunos puertos con cosas en el exterior. Los elementos fuera de la aplicación pueden ser tratados de manera simétrica.

El hexágono está destinado a resaltar visualmente:

(a) la asimetría interior-exterior y la naturaleza similar de los puertos, para alejarse de la imagen en capas unidimensional y todo lo que evoca, y

(b) la presencia de un número definido de puertos diferentes: dos, tres o cuatro (cuatro es el máximo que he encontrado hasta la fecha).

El hexágono no es un hexágono porque el número seis sea importante, sino para permitir a las personas que hacen el dibujo tener espacio para insertar puertos y adaptadores según sea necesario, sin estar limitados por un dibujo en capas unidimensional. El término 'arquitectura hexagonal' proviene de este efecto visual.

El término "puertos y adaptadores" recoge los 'propósitos' de las partes del dibujo. Un puerto identifica una conversación con propósito. Típicamente, puede haber múltiples adaptadores para cualquier puerto, para varias tecnologías que pueden conectarse a ese puerto. Estos podrían incluir una máquina contestadora telefónica, una voz humana, un teléfono de tonos, una interfaz gráfica humana, un arnés de prueba, un controlador por lotes, una interfaz http, una interfaz directa entre programas, una base de datos ficticia en memoria, una base de datos real (quizás diferentes bases de datos para desarrollo, pruebas y uso real).

En las Notas de Aplicación, se volverá a mencionar la asimetría izquierda-derecha. Sin embargo, el propósito principal de este patrón es centrarse en la asimetría interior-exterior, fingiendo brevemente que todos los elementos externos son idénticos desde la perspectiva de la aplicación.

**Estructura**

**Figura 2: Arquitectura hexagonal con adaptadores.gif**
https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-with-adapters.gif

La Figura 2 muestra una aplicación con dos puertos activos y varios adaptadores para cada puerto. Los dos puertos son el lado de control de la aplicación y el lado de recuperación de datos. Este dibujo muestra que la aplicación puede ser igualmente controlada por una suite de pruebas automatizadas de regresión a nivel de sistema, por un usuario humano, por una aplicación remota http, o por otra aplicación local. En el lado de los datos, la aplicación puede configurarse para ejecutarse desacoplada de bases de datos externas utilizando un oracle en memoria o una base de datos "ficticia" que reemplace a la real; o puede ejecutarse contra la base de datos de prueba o de tiempo de ejecución. La especificación funcional de la aplicación, quizás en casos de uso, se realiza contra la interfaz del hexágono interno y no contra ninguna de las tecnologías externas que podrían ser utilizadas.

**Figura 3: Imagen de puerta de granero de arquitectura hexagonal.gif**
https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-barn-door-image-1.gif

La Figura 3 muestra la misma aplicación mapeada a un dibujo arquitectónico de tres capas. Para simplificar el dibujo, se muestran solo dos adaptadores para cada puerto. Este dibujo pretende mostrar cómo múltiples adaptadores encajan en las capas superior e inferior, y la secuencia en la que se utilizan durante el desarrollo del sistema. Las flechas numeradas muestran el orden en el que un equipo podría desarrollar y utilizar la aplicación:

1. Con un arnés de pruebas FIT impulsando la aplicación y utilizando una base de datos ficticia (en memoria) en lugar de la base de datos real.
2. Agregando una GUI a la aplicación, todavía ejecutándose con la base de datos ficticia.
3. En pruebas de integración, con scripts de prueba automatizados (por ejemplo, desde Cruise Control) impulsando la aplicación contra una base de datos real que contiene datos de prueba.
4. En uso real, con una persona utilizando la aplicación para acceder a una base de datos en vivo.

**Código de ejemplo**

La aplicación más simple que demuestra los puertos y adaptadores afortunadamente viene con la documentación de FIT. Es una aplicación simple de cálculo de descuentos:

```java
descuento(monto) = monto * tasa(monto);
```

En nuestra adaptación, el monto vendrá del usuario y la tasa vendrá de una base de datos, por lo que habrá dos puertos. Los implementamos en etapas:

- Con pruebas pero con una tasa constante en lugar de una base de datos ficticia,
- luego con la GUI,
- luego con una base de datos ficticia que puede ser reemplazada por una base de datos real.


**Etapa 1: FIT App con base de datos constante como base de datos ficticia**

Primero creamos los casos de prueba como una tabla HTML (ver la documentación de FIT para esto):

```java
TestDiscounter
monto	descuento()
100	5
200	10
```

Note que los nombres de las columnas se convertirán en nombres de clase y función en nuestro programa. FIT contiene formas de eliminar este "lenguaje de programador", pero para este artículo es más fácil dejarlos.

Sabemos cuáles serán los datos de prueba, creamos el adaptador del lado del usuario, el ColumnFixture que viene con FIT tal como se envía:

```java
import fit.ColumnFixture;

public class TestDiscounter extends ColumnFixture {
    private Discounter app = new Discounter();
    public double monto;
    public double descuento() {
        return app.descuento(monto);
    }
}
```

Eso es realmente todo lo que hay en el adaptador. Hasta ahora, las pruebas se ejecutan desde la línea de comandos (consulte el libro de FIT para la ruta que necesitará). Usamos esta:

```bash
set FIT_HOME=/FIT/FitLibraryForFit15Feb2005
java -cp %FIT_HOME%/lib/javaFit1.1b.jar;%FIT_HOME%/dist/fitLibraryForFit.jar;src;bin
fit.FileRunner test/Discounter.html TestDiscount_Output.html
```

FIT produce un archivo de salida con colores que muestran lo que pasó (o falló, en caso de que hayamos cometido un error tipográfico en algún lugar a lo largo del camino).

En este punto, el código está listo para ser revisado, conectado a Cruise Control o a su máquina de construcción automatizada, e incluido en la suite de construcción y prueba.

**Etapa 2: App UI con base de datos constante como base de datos ficticia**

Voy a dejar que crees tu propia GUI y que la uses para impulsar la aplicación Discounter, ya que el código es un poco largo para incluirlo aquí. Algunas de las líneas clave en el código son estas:

```java
...
Discounter app = new Discounter();
public void actionPerformed(ActionEvent event) {
    ...
    String montoStr = text1.getText();
    double monto = Double.parseDouble(montoStr);
    descuento = app.descuento(monto);
    text3.setText( "" + descuento );
    ...
```

En este punto, la aplicación puede ser demostrada y probada de regresión. Los adaptadores del lado del usuario están ambos ejecutándose.

**Etapa 3: (FIT o UI) App con base de datos ficticia**

Para crear un adaptador reemplazable para el lado de la base de datos, creamos una 'interfaz' a un repositorio, una 'Factory de Repositorios' que producirá el objeto de servicio real o ficticio, y la base de datos ficticia en memoria.

```java
public interface RateRepository {
    double getRate(double monto);
}

public class RepositoryFactory {
    public RepositoryFactory() {  super(); }
    public static RateRepository getMockRateRepository() {
        return new MockRateRepository();
    }
}

public class MockRateRepository implements RateRepository {
    public double getRate(double monto) {
        if(monto <= 100) return 0.01;
        if(monto <= 1000) return 0.02;
        return 0.05;
    }
}
```

Para conectar este adaptador a la aplicación Discounter, necesitamos actualizar la propia aplicación para aceptar un adaptador de repositorio a utilizar, y que el adaptador del lado del usuario (FIT o UI) pase el repositorio a utilizar (real o ficticio) en el constructor de la aplicación misma. Aquí está la aplicación actualizada y un adaptador FIT que pasa un repositorio ficticio (el código del adaptador FIT para elegir si pasa el adaptador de repositorio ficticio o real es más largo sin agregar mucha información nueva, así que omito esa versión aquí).

```java
import repository.RepositoryFactory;
import repository.RateRepository;

public class Discounter {
    private RateRepository rateRepository;
    
    public Discounter(RateRepository r) {
        super();
        rateRepository = r;
    }
    
    public double discount(double amount) {
        double rate = rateRepository.getRate(amount); 
        return amount * rate;
    }
}
```

```java
import app.Discounter;
import fit.ColumnFixture;

public class TestDiscounter extends ColumnFixture {
    private Discounter app = new Discounter(RepositoryFactory.getMockRateRepository());
    public double amount;
    
    public double discount() {
        return app.discount(amount);
    }
}

```

Esto concluye la implementación de la versión más simple de la arquitectura hexagonal.

Para una implementación diferente, utilizando Ruby y Rack para el uso del navegador, consulta https://github.com/totheralistair/SmallerWebHexagon.

**Notas de Aplicación**

**La Asimetría Izquierda-Derecha**

El patrón de puertos y adaptadores está deliberadamente escrito asumiendo que todos los puertos son fundamentalmente similares. Esa pretensión es útil a nivel arquitectónico. En la implementación, los puertos y adaptadores aparecen en dos sabores, que llamaré "primario" y "secundario", por razones que pronto serán evidentes. También podrían ser llamados adaptadores "impulsores" y "impulsados".

El lector atento habrá notado que en todos los ejemplos dados, se utilizan fixtures FIT en los puertos del lado izquierdo y mocks en el lado derecho. En la arquitectura de tres capas, FIT se encuentra en la capa superior y el mock en la capa inferior.

Esto está relacionado con la idea de casos de uso de "actores primarios" y "actores secundarios". Un "actor primario" es aquel que impulsa la aplicación (sacándola de un estado de reposo para realizar una de sus funciones publicitadas). Un "actor secundario" es aquel que la aplicación impulsa, ya sea para obtener respuestas o simplemente para notificar. La distinción entre "primario" y "secundario" radica en quién inicia o está a cargo de la conversación.

El adaptador de prueba natural para sustituir a un "actor primario" es FIT, ya que ese marco está diseñado para leer un guion y conducir la aplicación. El adaptador de prueba natural para sustituir a un "actor secundario", como una base de datos, es un mock, ya que está diseñado para responder consultas o registrar eventos desde la aplicación.

Estas observaciones nos llevan a seguir el diagrama de contexto de casos de uso del sistema y dibujar los "puertos primarios" y "adaptadores primarios" en el lado izquierdo (o superior) del hexágono, y los "puertos secundarios" y "adaptadores secundarios" en el lado derecho (o inferior) del hexágono.

La relación entre los puertos/adaptadores primarios y secundarios y su implementación respectiva en FIT y mocks es útil tenerla en mente, pero debería utilizarse como consecuencia de usar la arquitectura de puertos y adaptadores, no para acortarla. El beneficio final de una implementación de puertos y adaptadores es la capacidad de ejecutar la aplicación en modo completamente aislado.

**Casos de Uso y el Límite de Aplicación**

Es útil utilizar el patrón de arquitectura hexagonal para reforzar la forma preferida de escribir casos de uso.

Un error común es escribir casos de uso que contienen un conocimiento íntimo de la tecnología que está fuera de cada puerto. Estos casos de uso han ganado justificadamente una mala reputación en la industria por ser largos, difíciles de leer, aburridos, frágiles y costosos de mantener.

Comprendiendo la arquitectura de puertos y adaptadores, podemos ver que los casos de uso generalmente deberían escribirse en el límite de la aplicación (el hexágono interno), para especificar las funciones y eventos soportados por la aplicación, independientemente de la tecnología externa. Estos casos de uso son más cortos, más fáciles de leer, menos costosos de mantener y más estables con el tiempo.

**¿Cuántos Puertos?**

Lo que exactamente es un puerto y lo que no lo es, es en gran medida una cuestión de gusto. En un extremo, cada caso de uso podría tener su propio puerto, produciendo cientos de puertos para muchas aplicaciones. Alternativamente, se podría imaginar fusionar todos los puertos primarios y todos los puertos secundarios para que haya solo dos puertos, un lado izquierdo y un lado derecho.

Ningún extremo parece óptimo.

El sistema meteorológico descrito en los Usos Conocidos tiene cuatro puertos naturales: el feed del clima, el administrador, los suscriptores notificados, la base de datos de suscriptores. Un controlador de máquina de café tiene cuatro puertos naturales: el usuario, la base de datos que contiene las recetas y los precios, los dispensadores y la caja de monedas. Un sistema de medicación hospitalaria podría tener tres: uno para la enfermera, uno para la base de datos de prescripciones y uno para los dispensadores de medicación controlados por computadora.

No parece que haya ningún daño particular en elegir el "número incorrecto" de puertos, por lo que sigue siendo una cuestión de intuición. Mi selección tiende a favorecer un número pequeño, dos, tres o cuatro puertos, como se describe arriba y en los Usos Conocidos.

Usos Conocidos

Figura 4: Ejemplo complejo de arquitectura hexagonal.gif
https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-complex-example.gif

La Figura 4 muestra una aplicación con cuatro puertos y varios adaptadores en cada puerto. Esta fue derivada de una aplicación que escuchaba alertas del servicio meteorológico nacional sobre terremotos, tornados, incendios e inundaciones, y notificaba a las personas en sus teléfonos o contestadores automáticos. En ese momento, discutimos este sistema, identificando y discutiendo las interfaces del sistema como 'tecnología vinculada a propósito'. Había una interfaz para recibir datos de activación a través de una alimentación por cable, otra para enviar datos de notificación a los contestadores automáticos, una interfaz administrativa implementada en una GUI, y una interfaz de base de datos para obtener los datos de suscriptores.

Las personas tenían dificultades porque necesitaban añadir una interfaz HTTP desde el servicio meteorológico, una interfaz de correo electrónico para sus suscriptores, y tenían que encontrar una manera de agrupar y desagrupar su creciente suite de aplicaciones para diferentes preferencias de compra de los clientes. Temían que estaban enfrentando una pesadilla de mantenimiento y prueba al tener que implementar, probar y mantener versiones separadas para todas las combinaciones y permutaciones.

Su cambio en el diseño fue arquitectar las interfaces del sistema 'por propósito' en lugar de por tecnología, y hacer que las tecnologías sean sustituibles (en todos los lados) mediante adaptadores. Inmediatamente lograron la capacidad de incluir la alimentación HTTP y la notificación por correo electrónico (los nuevos adaptadores se muestran en el dibujo con líneas discontinuas). Al hacer que cada aplicación sea ejecutable en modo 'headless' a través de APIs, pudieron agregar un adaptador de aplicación para agregar y desagrupar la suite de aplicaciones, conectando las subaplicaciones según la demanda. Finalmente, al hacer que cada aplicación sea ejecutable completamente de forma aislada, con adaptadores de prueba y simulación en su lugar, obtuvieron la capacidad de realizar pruebas de regresión en sus aplicaciones con scripts de prueba automatizados independientes.

Mac, Windows, Google, Flickr, Web 2.0

A principios de los años 1990, las aplicaciones de MacIntosh, como procesadores de texto, debían tener interfaces manejables por API para que las aplicaciones y scripts escritos por el usuario pudieran acceder a todas las funciones de las aplicaciones. Las aplicaciones de escritorio de Windows han evolucionado con la misma capacidad (no tengo conocimientos históricos para decir cuál fue primero, ni es relevante para el punto).

La tendencia actual (2005) en las aplicaciones web es publicar una API y permitir que otras aplicaciones web accedan directamente a esas APIs. Así, es posible publicar datos locales sobre crímenes en un mapa de Google, o crear aplicaciones web que incluyan las capacidades de archivado y anotación de fotos de Flickr.

Todos estos ejemplos se centran en hacer visibles las 'puertas' primarias de las APIs. Aquí no se menciona información sobre las puertas secundarias.

Salidas Almacenadas

Este ejemplo escrito por Willem Bogaerts en el wiki C2:

"Me encontré con algo similar, pero principalmente porque mi capa de aplicación tenía una fuerte tendencia a convertirse en una central telefónica que gestionaba cosas que no debería hacer. Mi aplicación generaba salida, la mostraba al usuario y luego tenía alguna posibilidad de almacenarla también. Mi problema principal era que no siempre era necesario almacenarlo. Así que mi aplicación generaba salida, tenía que almacenarla en búfer y presentarla al usuario. Entonces, cuando el usuario decidía que quería almacenar la salida, la aplicación recuperaba el búfer y la almacenaba de verdad.

Esto no me gustaba en absoluto. Luego se me ocurrió una solución: tener un control de presentación con capacidades de almacenamiento. Ahora la aplicación ya no canaliza la salida en diferentes direcciones, sino que simplemente la envía al control de presentación. Es el control de presentación el que almacena la respuesta en búfer y le da al usuario la posibilidad de almacenarla.

La arquitectura tradicional de capas enfatiza que "UI" (Interfaz de Usuario) y "almacenamiento" deben ser diferentes. La Arquitectura de Puertos y Adaptadores puede reducir la salida a ser simplemente "salida" nuevamente."

Ejemplo anónimo del wiki C2:

"En un proyecto en el que trabajé, usamos la Metáfora del Sistema de componentes estéreo. Cada componente tiene interfaces definidas, cada una con un propósito específico. Luego podemos conectar los componentes de manera casi ilimitada utilizando cables y adaptadores simples."

Desarrollo Distribuido, de Equipos Grandes

Este todavía está en fase de prueba, por lo que no cuenta como un uso del patrón de manera adecuada. Sin embargo, es interesante considerarlo.

Equipos en diferentes ubicaciones construyen todos bajo la arquitectura hexagonal, utilizando FIT (Framework for Integrated Test) y mocks (simulaciones) para que las aplicaciones o componentes puedan ser probados en modo autónomo. La construcción con CruiseControl se ejecuta cada media hora y ejecuta todas las aplicaciones utilizando la combinación FIT+mock. A medida que se completan los subsistemas de aplicaciones y bases de datos, los mocks se reemplazan con bases de datos de prueba.

Separación del Desarrollo de la UI y la Lógica de Aplicación

Este también está en una fase de prueba inicial, por lo que no cuenta como un uso del patrón. Sin embargo, es interesante considerarlo.

El diseño de la UI es inestable, ya que aún no se ha decidido una tecnología de conducción o una metáfora. La arquitectura de los servicios de backend tampoco ha sido decidida y de hecho probablemente cambiará varias veces en los próximos seis meses. No obstante, el proyecto ha comenzado oficialmente y el tiempo avanza.

El equipo de aplicación crea pruebas FIT y mocks para aislar su aplicación, y crea funcionalidad demostrable y probable para mostrar a sus usuarios. Cuando las decisiones de la UI y los servicios de backend finalmente se concreten, "debería ser sencillo" agregar esos elementos a la aplicación. Manténganse al tanto para saber cómo resulta esto (o inténtenlo ustedes mismos y escríbanme para informarme cómo les fue).

Patrones Relacionados

Adaptador

El libro 'Design Patterns' contiene una descripción del patrón genérico 'Adaptador': "Convierte la interfaz de una clase en otra interfaz que los clientes esperan". La arquitectura de puertos y adaptadores es un uso particular del patrón 'Adaptador'.

El patrón Modelo-Vista-Controlador (MVC) fue implementado tan temprano como en 1974 en el proyecto Smalltalk. A lo largo de los años, ha dado lugar a muchas variaciones, como Modelo-Interactor y Modelo-Vista-Presentador. Cada una de estas implementaciones sigue la idea de puertos y adaptadores en los puertos primarios, pero no en los secundarios.

Objetos Mock y Loopback

"Un objeto mock es un 'doble agente' utilizado para probar el comportamiento de otros objetos. Primero, actúa como una implementación falsa de una interfaz o clase que imita el comportamiento externo de una implementación real. Segundo, un objeto mock observa cómo interactúan otros objetos con sus métodos y compara el comportamiento real con las expectativas predefinidas. Cuando ocurre una discrepancia, un objeto mock puede interrumpir la prueba e informar la anomalía. Si la discrepancia no puede ser notada durante la prueba, un método de verificación llamado por el tester asegura que se hayan cumplido todas las expectativas o se informen los fallos." — De http://MockObjects.com

Totalmente implementado según la agenda de objetos mock, estos son utilizados en toda la aplicación, no solo en la interfaz externa. El enfoque principal del movimiento de objetos mock es la conformidad con el protocolo especificado a nivel de clase y objeto individual. Yo tomo su palabra "mock" como la mejor descripción corta de un sustituto en memoria para un actor secundario externo.

El patrón Loopback es un patrón explícito para crear un reemplazo interno de un dispositivo externo.

Pedestales

En "Patrones para Generar una Arquitectura en Capas", Barry Rubel describe un patrón para crear un eje de simetría en software de control que es muy similar a los puertos y adaptadores. El patrón 'Pedestal' requiere implementar un objeto que represente cada dispositivo hardware dentro del sistema, y conectar esos objetos en una capa de control. El patrón 'Pedestal' puede ser utilizado para describir ambos lados de la arquitectura hexagonal, pero aún no enfatiza la similitud a través de los adaptadores. Además, al estar escrito para un entorno de control mecánico, no es tan fácil ver cómo aplicar el patrón a aplicaciones de tecnología de la información.

Checks

El lenguaje de patrones de Ward Cunningham para detectar y manejar errores de entrada de usuario es bueno para el manejo de errores a través de los límites internos del hexágono.

Inversión de Dependencias (Inyección de Dependencias) y SPRING

El Principio de Inversión de Dependencias de Bob Martin (también llamado Inyección de Dependencias por Martin Fowler) establece que "los módulos de alto nivel no deben depender de los módulos de bajo nivel. Ambos deben depender de abstracciones. Las abstracciones no deben depender de detalles. Los detalles deben depender de abstracciones". El patrón 'Inyección de Dependencias' de Martin Fowler proporciona algunas implementaciones. Estas muestran cómo crear adaptadores intercambiables para actores secundarios. El código puede ser escrito directamente, como se hace en el código de muestra del artículo, o utilizando archivos de configuración y haciendo que el framework SPRING genere el código equivalente.

Agradecimientos

Gracias a Gyan Sharma en Intermountain Health Care por proporcionar el código de muestra utilizado aquí. Gracias a Rebecca Wirfs-Brock por su libro 'Object Design', que junto con el patrón 'Adapter' del libro 'Design Patterns', me ayudó a entender de qué se trataba el hexágono. También gracias a las personas en el wiki de Ward, quienes proporcionaron comentarios sobre este patrón a lo largo de los años (por ejemplo, especialmente el sitio web de Kevin Rutherford http://silkandspinach.net/blog/2004/07/hexagonal_soup.html).

Referencias y Lecturas Relacionadas

FIT, un Framework para Pruebas Integradas: Cunningham, W., en línea en http://fit.c2.com, y Mugridge, R. y Cunningham, W., 'Fit for Developing Software', Prentice-Hall PTR, 2005.

El patrón 'Adapter': en Gamma, E., Helm, R., Johnson, R., Vlissides, J., 'Design Patterns', Addison-Wesley, 1995, pp. 139-150.

El patrón 'Pedestal': en Rubel, B., "Patrones para Generar una Arquitectura en Capas", en Coplien, J., Schmidt, D., 'Pattern Languages of Program Design', Addison-Wesley, 1995, pp. 119-150.

El patrón 'Checks': por Cunningham, W., en línea en http://c2.com/ppr/checks.html

El Principio de Inversión de Dependencias: Martin, R., en 'Agile Software Development Principles Patterns and Practices', Prentice Hall, 2003, Capítulo 11: "El Principio de Inversión de Dependencias", y en línea en http://www.objectmentor.com/resources/articles/dip.pdf

El patrón 'Inyección de Dependencias': Fowler, M., en línea en http://www.martinfowler.com/articles/injection.html

El patrón 'Mock Object': Freeman, S., en línea en http://MockObjects.com

El patrón 'Loopback': Cockburn, A., en línea en http://c2.com/cgi/wiki?LoopBack

'Caso de uso': Cockburn, A., 'Writing Effective Use Cases', Addison-Wesley, 2001, y Cockburn, A., "Estructurando Casos de Uso con Metas", en línea en http://alistair.cockburn.us/crystal/articles/sucwg/structuringucswithgoals.htm

La página original estaba en el wiki de Ward en http://c2.com/cgi/wiki?HexagonalArchitecture

Utah Code Camp Septiembre 19, 2009: Tarea de Codificación
La aplicación más simple viene con la documentación FIT. Es una aplicación simple de cálculo de descuento:

descuento(monto) = monto * tasaDescuento(monto);

'Monto' proviene del usuario o de un marco de prueba (o un archivo).
'Rate' proviene de una base de datos o de un mock en memoria de una base de datos.
Implementar en etapas:

Entrada desde un marco de prueba, utilizando una constante para la tasa de descuento,
Entrada desde una GUI, todavía utilizando una constante para la tasa de descuento.
Entrada desde ya sea una prueba o una GUI, tasa de descuento desde una base de datos mock que puede ser intercambiada por una base de datos real.
Implementación en Ruby / Rack (sin Rails)
Hice una pequeña versión web lectora de esto, véala en

https://github.com/totheralistair/SmallerWebHexagon
Esa toma, ya sea del navegador, o el controlador de Rack, o solo un controlador de prueba en el lado izquierdo, y ya sea una constante, o en el código, o desde un archivo de base de datos de tasa en el lado derecho. Escribí esto para mostrar la implementación en un entorno simple pero real (2014).

Otras discusiones e implementaciones
Puedes encontrar más en línea sobre esta arquitectura buscando en Google o Twitter (en particular). También ver:

http://tpierrain.blogspot.fr/2013/08/a-zoom-on-hexagonalcleanonion.html
La presentación para la charla sobre esto que di en Utah Code está en http://alistair.cockburn.us/Hexagonal+Architecture+Keynote+at+Utah+Code+Camp+September+2009.pps
También una buena elaboración con notas para él mismo por Duncan Nisbet en http://www.duncannisbet.co.uk/hexagonal-architecture-for-testers-part-1
mi charla de relámpago en video en la Conferencia Ruby Mountain West en 2010: Video de la charla relámpago de arquitectura hexagonal CQRS de Alistair en la Conferencia Ruby Mountain West 2010 (discusión: Re: Video de la charla relámpago de arquitectura hexagonal CQRS de Alistair en la Conferencia Ruby Mountain West 2010)
https://twitter.com/search?q=%22hexagonal%20architecture%22
http://twitter.com/andrzejkrzywda/status/267420878487310336
(solo puerto de usuario) un CMS diminuto https://github.com/totheralistair/SmallWebHexagon
https://github.com/patmaddox/hexarch2 Pat Maddox comienza con una Arquitectura Hexagonal y la transforma en una basada en eventos y luego en CQRS. Echa un vistazo.
Una larga y detallada discusión encantadora sobre la arquitectura hexagonal y Rails con BadrinathJanakiraman y Martin Fowler: http://thoughtworks.wistia.com/medias/uxjb0lwrcz
evolución detallada en https://github.com/Lunch-box/SimpleOrderRouting/wiki/Logbook-4#day-15-october-27th-2014
Dependencias Configurables, Actores Primarios y Secundarios
Intenté hacer este patrón realmente simétrico, de ahí el hexágono. Sin embargo, al observar varias implementaciones, lentamente quedó claro que hay una asimetría (lo que hace que esto sea fundamentalmente diferente de patrones vecinos como la arquitectura de cebolla). Como se menciona anteriormente (ver La Asimetría Izquierda-Derecha), la asimetría coincide con el concepto de actores primarios y secundarios de Ivar Jacobson, y afecta cómo se implementa la Dependencia Configurable (discusión: Re: Dependencia Configurable). (Esto se muestra brevemente en el esquema de Dependencia Configurable:

Ilustración de Dependencia Configurable ilustrada1-800pxV.jpg
Ilustración de Dependencia Configurable ilustrada1-800pxV.jpg (discusión: Re: Ilustración de Dependencia Configurable ilustrada1-800pxV.jpg)

La diferencia entre un puerto de actor primario y secundario radica únicamente en quién inicia la conversación. Un actor primario conoce y inicia la conversación con el sistema o la aplicación; para un actor secundario, es el sistema o la aplicación quien conoce y inicia la conversación con el otro. Esa es realmente la única diferencia entre los dos, en el mundo de los casos de uso.

En la implementación, esa diferencia importa: A quien iniciará la conversación se le debe pasar el controlador para el otro.

En el caso de los puertos de actores primarios, el constructor macro pasará a la interfaz de usuario, el marco de prueba, o el controlador el controlador de la aplicación y dirá, "Ve a hablar con eso". El actor primario llamará a la aplicación, y probablemente la aplicación nunca sabrá quién la llamó. (Eso es normal para los receptores de una llamada).

En marcado contraste, para los puertos de actores secundarios, el constructor macro pasará a la interfaz de usuario, el marco de prueba, o el controlador el controlador del actor secundario que se utilizará, que se pasará como parámetro a la aplicación, y ahora la aplicación sabrá quién o qué es el destinatario de la llamada saliente. (Esto es nuevamente normal para enviar una llamada).

Así, el sistema o la aplicación se construye de manera diferente para los puertos de actores primarios y secundarios: ignorante y inicialmente pasiva para los actores primarios, y teniendo una forma de almacenar y llamar a los puertos de actores secundarios.

Ambos puertos implementan Dependencia Configurable (discusión: Re: Dependencia Configurable), pero de manera diferente.

Ejemplo simple para un sistema de 3 puertos, como una máquina de café o una unidad médica hospitalaria que dispensa medicamentos intravenosos (¡Qué extraño que ambos resulten iguales, arquitectónicamente!):

El comprador, el arnés de prueba o el administrador del hospital es un actor primario que impulsa el sistema.
La base de datos de recetas o la base de datos médica es un actor secundario, ofreciendo su información.
Los dispensadores químicos en cualquiera son actores secundarios.
Resultado final: el aspecto primario/secundario de un puerto no se puede ignorar.
