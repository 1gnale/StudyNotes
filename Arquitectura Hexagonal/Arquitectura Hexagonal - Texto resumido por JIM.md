# Patrón: Puertos y adaptadores.
# Nombre alternativo: Arquitectura hexagonal

## Proposito:
 Permitir que una aplicación pueda ser conducida tanto por usuarios, programas, pruebas automatizadas o scripts por lotes, y ser desarrollada y probada de manera aislada respecto a sus dispositivos y bases de datos de tiempo de ejecución eventual.

## Motivo:
 Un gran problema que tienen las aplicaciones de sofware es la infiltración de la lógica de negocio en el código de la interfaz de usuario. Esto se sintetiza en 3 "pequeños" problemitas:

 **- El sistema es difícil de probar con suites de pruebas automatizadas ya que parte de la lógica que necesita ser probada depende de detalles que cambian frecuentemente.**

 **- Se vuelve imposible cambiar un uso impulsado por humanos a un sistema ejecutado por lotes.**

 **- Se vuelve difícil o imposible permitir que el programa sea impulsado por otro programa.**

 La solución que suele aparecer es crear una nueva capa con la promesa de que esta vez la lógica de negocio no se filtrará al código. Mas, al no tener un mecanismo que detecte que esto pasa, pasa el tiempo y, eventualmente, la lógica de negocio se filtra y el problema reaparece.

 Imagina ahora que cada funcionalidad está en una API o una llamada a función. En este caso se pueden realzar pruebas automatizadas controlando que las nuevas funcionalidades no rompan las anteriores. Los expertos pueden crear tests automatizados antes de terminar la GUI  indicando a los desarrolladores si han hecho bien su trabajo. La aplicación puede ser desplegada de manera "headless" para que sólo esté disponible la API. Otros programas pueden hacer uso de su funcionalidad, simplificando el diseño de los suites en aplicaciones complejas y permitiendo que las aplicaciones B2B se utilicen entre si sin intervención humana. Finalmente las pruebas automáticas detectan cualquier violación de la promesa de mantener la lógica de negocio fuera de la capa de presentación, permitiendo corregir la fuga de lógica en el instante.

 Sin embargo, existe un problema en esto. Particularmente en dónde la aplicación se vincula a una base de datos externa u otro servicio ("el otro lado" según Alistair):
 
 **- Cuando el servidor de la base de datos se cae, se somete a una reestructuración o reemplazo los desarrolladores no pueden trabajar porque su trabajo está vinculado a la base de datos. Esto causa demoras y, frecuentemente, malos sentimientos entre las personas involucradas**

 Estos dos problemas tienen simetría entre ellos, detallados en *la naturaleza de la solución*

## La naturaleza de la solución

 Los problemas del lado del usuario y del servidor son causados en realidad por el mismo error de diseño: el entrelazamiento entre lógica de negocio e interacción con entidades externas. La asimetría que se debe explotar no es entre el lado "izquierdo" y "derecho" sino entre el "interior" y el "exterior" de la aplicación. La regla a seguir es que el código relacionado con la parte "interior" no debe filtrarse hacia la parte "exterior".

 La idea es que la aplicación se comunique por "puertos" con agencias externas. El termino "puerto/s" se refiere a los de un OS, donde cualquier dispositivo que cumpla con los protocolos del puerto puede ser conectado.

 El protocolo toma la forma de interfaz de programa de aplicación (API).

 Para cada "dispositivo" hay un "adaptador" que convierte la definición de la API a las señales necesarias para ese dispositivo y viceversa. **Una GUI es un ejemplo de adaptador**, que mapea las interacciones del usuario en llamadas a la API del "puerto". En esencia **un adaptador es: cualquier código necesario para la comunicación entre aplicaciones**.

 En otro lado de la aplicación, esta se comunica con uan entidad externa para obtener datos. El protocolo típico es uno de base de datos. **Desde la perspectiva de la aplicación, si la base de datos migra o se mueve a un archivo plano la conversación a través de la API no debe cambiar**. Los adaptadores adicionales para el mismo puerto son: un adaptador SQL, uno de archivo plano y, él más importante, un adaptador a una base de datos "ficticia" que recide en memoria. Todos estos cumplen con la regla de comunicación entre aplicaciones (en el caso del último la aplicación se comunica con si misma).

 Muchas aplicaciones tienen sólo dos puertos: el diálogo del lado del usuario y el diálogo del lado de la base de datos. Esto hace que parezca natural construi la app en una arquitectura en capas unidimensionales. Esto tiene dos problemas:

 **- Permiten que la lógica se filtre causando los problemas mencionados anteriormente.**

 **- Puede haber más de dos puertos para la aplicación pero la arquitectura no encaja con el dibujo unidimensional.**

 Abrazando la simetría de la situación resolvemos estos problemas. Hay una aplicación en el interior que se comunica por puertos con el exterior. El hexágono está destinado a resaltar visualmente:

 **- La asimetría interior-exterior y la naturaleza similar de los puertos. Alejandose de la imagen en capas unidimensional**

 **- La presencia de un numero *definido* de puertos diferentes: dos, tres o cuatro** (siendo cuatro el máximo que Alistair encontró a la hora de redactar)

 El hexágono aparece no porque el 6 sea importante, sino para dar más espacio en el dibujo para insertar puertos y adaptadores según las necesidades. Por otro lado, el termino "puertos y adaptadores" refiere a que un puerto identifica una conversación con proposito. Habiendo uno más adaptadores para cualquier puerto.

 Mas adelante, se mencionará nuevamente la asimetría izquierda-derecha. Mas, el proposito principal de este patrónw es centrarse en la asimetría interior-exterior, interpretando desde la perspectiva de la aplicación que todos los elementos externos son idénticos (*fingimos que todos los elementos externos son idénticos*).

## Estructura

![Adaptadores aplicados](<img/Figura 2 - Hexagono con adaptadores.png>) 
![Archivo original](https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-with-adapters.gif)

 La figura (en mi opinión el mejor ejemplo visual en todo el documento) muestra una aplicación con dos puertos activos y varios adaptadores en cada uno. Los dos puertos son: el lado de control y el lado de obtención de datos. El dibujo muestra que la app puede ser controlada por un suite de pruebas automatizadas, un usuario, una aplicación remota HTTP o por otra aplicación local. En el lado de los datos la aplicación puede ejecutarse desacoplada a la DB utilizando una base de datos ficticia que reemplace la real o puede ejecutarse contra la DB de prueba. La especificación funcinal en casos de uso se realiza contra la interfaz del hexágono interno y no contra ninguna de las tecnologías externas que podrian ser utilizadas.

![Paralelismo](<img/Figura 3 - Paralelismo.png>)
![Archivo original](<https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-barn-door-image-1.gif>)

 La figura muestra la misma aplicación mapeada a un dibujo arquitectónico de tres capas. Se muestran dos adaptadores para cada puerto para simplificar el ejemplo. Se pretende mostrar cómo múltiples adaptadores enjacan en las capas superior e inferior y la secuencia en la que se utilizan durante el desarrollo del sistema. Las flechas muestran el orden en el que se podría desarrollar y utilizar la aplicación

1. Con un arnés de pruebas impulsando la aplicación y utilizando una DB ficticia en memoria en lugar de la base de datos real.
2. Agregando una GUI a la aplicación, todavía ejecutando la base de datos ficticia.
3. En pruebas de integración, con scripts de prueba automatizados (CI/CD de toda la vida) impulsando la aplicación contra una base de datos real pero con datos de prueba.
4. En uso real, con una persona utilizando la aplicación para acceder a una DB en vivo.

## Ejemplo inplementación TS, React y Jest

 El ejemplo original de Alistair usa Java y FIT, yo lo cambio por el mencionado arriba por cuestiones de comodidad personal. Sin embargo, es igualmente útil como ejemplo. Si desea ver el original le recomiendo leer el texto original (traducido está ligeramente mejor formateado, pero la traducción no tiene tanto cariño como este resumen). El orden de implementación es el siguiente:

- Con pruebas pero con una tasa constante en lugar de una base de datos ficticia.
- Luego con la GUI.
- Luego con una base de datos ficticia que puede ser reemplazada por una base de datos real.

### Etapa 1: Implementación con Base de Datos Ficticia

 En esta etapa, creamos pruebas usando una tabla HTML como base de datos ficticia. Utilizaremos TypeScript y Jest para las pruebas unitarias.

#### Caso de Prueba en TypeScript (Jest)

```typescript
// Discounter.ts

export class Discounter {
  discount(amount: number): number {
    if (amount <= 100) {
      return amount * 0.05;
    } else if (amount <= 200) {
      return amount * 0.1;
    } else {
      return amount * 0.15;
    }
  }
}
```

#### Prueba Unitaria en Jest

```typescript
// TestDiscounter.test.ts
import { Discounter } from './Discounter';

describe('Discounter', () => {
  let discounter: Discounter;

  beforeEach(() => {
    discounter = new Discounter();
  });

  it('should calculate discount correctly', () => {
    expect(discounter.discount(100)).toBe(5);
    expect(discounter.discount(200)).toBe(20);
  });
});
```

 **En este punto, el código está listo para ser revisado, conectado a CI/CD e incluido en la suite de construcción y prueba.**

### Etapa 2: Aplicación UI con Base de Datos Ficticia

 Creamos una interfaz de usuario simple en React.

```tsx
// App.tsx
import React, { useState } from 'react';
import { Discounter } from './Discounter';

const App: React.FC = () => {
  const [amount, setAmount] = useState<number>(0);
  const [discount, setDiscount] = useState<number>(0);
  const discounter = new Discounter();

  const handleCalculateDiscount = () => {
    const calculatedDiscount = discounter.discount(amount);
    setDiscount(calculatedDiscount);
  };

  return (
    <div>
      <input
        type="number"
        value={amount}
        onChange={(e) => setAmount(parseFloat(e.target.value))}
      />
      <button onClick={handleCalculateDiscount}>Calculate Discount</button>
      <div>Discount: {discount}</div>
    </div>
  );
};

export default App;
```

### Etapa 3: Aplicación con Repositorio Ficticio

 Para crear un adaptador reemplazable para el lado de la base de datos, creamos una 'interfaz' a un repositorio, una 'Factory de Repositorios' que producirá el objeto de servicio real o ficticio, y la base de datos ficticia en memoria.


```typescript
// Repository.ts
export interface RateRepository {
  getRate(amount: number): number;
}

export class MockRateRepository implements RateRepository {
  getRate(amount: number): number {
    if (amount <= 100) return 0.01;
    if (amount <= 1000) return 0.02;
    return 0.05;
  }
}
```

### Conexión en la Aplicación

 Para conectar este adaptador a la aplicación `Discounter`, necesitamos actualizar la propia aplicación para aceptar un adaptador de repositorio a utilizar, y que el adaptador del lado del usuario pase el repositorio a utilizar (real o ficticio) en el constructor de la aplicación misma. Aquí está la aplicación actualizada.

```typescript
// Discounter.ts
import { RateRepository } from './Repository';

export class Discounter {
  private rateRepository: RateRepository;

  constructor(repo: RateRepository) {
    this.rateRepository = repo;
  }

  discount(amount: number): number {
    const rate = this.rateRepository.getRate(amount);
    return amount * rate;
  }
}
```

### Prueba Unitaria con Jest para Adaptador de Repositorio

```typescript
// TestDiscounter.test.ts
import { Discounter } from './Discounter';
import { MockRateRepository } from './Repository';

describe('Discounter', () => {
  let discounter: Discounter;

  beforeEach(() => {
    const mockRepo = new MockRateRepository();
    discounter = new Discounter(mockRepo);
  });

  it('should calculate discount correctly using mock repository', () => {
    expect(discounter.discount(50)).toBeCloseTo(0.5);
    expect(discounter.discount(500)).toBeCloseTo(10);
  });
});
```
 Asi concluye el ejemplo de implementación de la arquitectura hexagonal.

## La asimetría Izq-der

 El patrón de diseño asume deliberadamente que todos los puertos son, fundamentalmente, similares. Esta pretensión es útil a nivel arquitectónico. En la implemtentación, tenemos dos tipos de puertos: "primarios/impulsores" y "secundarios/impuslados".

 Los atentos habrán notado que en todos los ejemplos se utilizan los testeos del lado izquierdo y los mocks del lado derecho. En la arquitectura de tres capas los tests van en la capa superior y el mock en la capa inferior.

 Esto está relacionado con la idea de casos de uso de "actores primarios" y "actores secundarios".

- **Actores Primarios**: se denomina como tal a aquel que *impulse* la aplicación, sácandola de un estado de reposo para que realice una de sus funcionalidades.

- **Actores Secundarios**: se denominan como tal a aquel que la aplicación *impulsa*, ya sea para obtener respuestas o para notificar.

 La diferencia radica entre cual inicia o está a cargo de la conversación.

 **Ejemplo**: el adaptador de tests juega el rol de "actor primario" leyendo un guion y conduciendo la aplicación. Mientras que el mock juega de "actor secundario" ya que está diseñado para responder consultas o registrar eventos desde la aplicación.

 Estas observaciones nos hacen seguir el diagrama de contexto de casos de uso y dibujar los puertos y adaptadores primarios en el lado izquierdo o superior del hexágono y los puertos y adaptadores secundarios en el lado derecho o inferior.

 La relación entre los puertos o adaptadores primarios y secundarios con su implementación respectiva en tests y mocks es útil para tener en mente. Mas debe utilizarse como consecuencia de usar la arquitectura hexagonal, no para acortarla. **El beneficio final de la implementación de puertos y adaptadores es la capacidad de ejecutar la aplicación en modo completamente aislado**.

## Casos de Uso y el Límite de Aplicación

 Es útil utilizar la arquitectura hexagonal para reforzar la forma preferida de escribir casos de uso. 

 Tengamos en cuenta que un caso de uso escrito con conocimiento íntimo de la tecnología externa al puerto es frágil y costoso de mantener. Evitemoslos recordando que *fingimos que todos los elementos externos son idénticos*.

 Comprendiendo la arquitectura hexagonal podemos ver que los casos de uso generalmente deberían escribirse al límite de la aplicación (hexágono interno), para especificar las funciones y eventos soportados por la aplicación independientemente de la tecnología externa.

## Número de puertos 

 Cada caso de uso podría tener su propio puerto, produciendo cientos de puertos para muchas aplicaciones. A su vez, se podrían fusionar todos los puertos primarios y todos los secundarios para que solo haya dos puertos generando un lado izquierdo y derecho.

 Ningún extremo parece óptimo. Veamos los siguientes ejemplos:

 - Sistema meteorológico (4 puertos naturales/primarios):
 1. Feed del clima.
 2. El administrador.
 3. Usuarios notificados.
 4. Base de datos de suscriptores.

 - Una máquina de café (4 puertos naturales/primarios):
 1. Usuario.
 2. Base de datos con recetas y precios.
 3. Dispensadores.
 4. Caja de monedas.

 - Sistema de medicación hospitalaria (3 puertos naturales/primarios):
 1. Enfermera.
 2. Base de datos de prescripciones.
 3. Dispensadores de medicación 

 **Nota para JIM del futuro: añadir más ejemplos que estos 3 son medio falopa**

 No parece que hayan daños al elegir un "número incorrecto" de puertos, por lo que es una cuestión de intuición. Alistair prefiere el uso de números pequeños: dos, tres o cuatro puertos.

![Ejemplo complejizado](<img/Figura 4 - Complejidad explayada.png>)
![Archivo original](<https://alistair.cockburn.us/wp-content/uploads/2018/02/Hexagonal-architecture-complex-example.gif>)

## IMPORTANTE 

 El texto original sigue, pero no considero que lo demás tenga extremada relevancia a excepción del Principio de Inversión de Dependencia que se detalla a continuación.

## Principio de Inversión de Dependencia

 El Principio de Inversión de Dependencias de "Bob" establece que los módulos de alto nivel no deben depender de los de bajo nivel. Ambos deben depender de abstracciones. Las abstracciones no deben depender de detalles y los detalles deben depender de abstracciones. Mucho muy importante.
