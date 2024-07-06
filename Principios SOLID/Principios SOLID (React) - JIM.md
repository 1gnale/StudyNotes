# SRP - Single Responsability Principle (principio de responsabilidad única)

 Cada clase o módulo debe hacer **una** cosa. O, sólo debe ser responsable de una acción. Citando a Robert C. Martin *una función debe hacer una cosa y hacerla bien*.


## Ejemplo SRP

```tsx
import {useEffect, useState} from "react"
import axios from "axios"

type TodoType = {
    id: number
    userId: number
    title: string
    completed: boolean
}

const TodoList = () => {
    const [data, setData] = useState<TodoType[]>([])
    const [isFetching, setIsFetching] = useState(true)

    useEffect(() =>{
        axios
            .get<TodoType[]>("https://exampleurl.com/example")
            .then((res) => {
                setData(res.data)
            })
            .catch((e) => {
                console.log(e)
            })
            .finally(() => {
                setIsfetching(false)
            })
    },[])

    if(isFetching) {
        return <p>... loading </p>
    }

    return (
        <ul>
            {data.map((todo) => {
                return (
                    <li>
                        <span>{todo.id}</span>
                        <span>{todo.tittle}</span>
                    </li>
                )
            })}
        </ul>
    )
}

export default TodoList
```

 Este componente tiene varias responsabilidades. Debe:
 - Gestionar el estado
 - Hacer el fetch mediante el useEffect
 - Y renderizar el contenido
 - Define un tipo

 Si en un componente de React tenemos un **useEffect** podemos, y es recomendable, crear un custome hook.

```tsx ../src/todo/useFetchTodo.tsx
import { useEffect, useState } from "react"
import axios from "axios"
import { TodoType } from "../types" //example

const useFetchTodo = () => {
    const [data, setData] = useState<TodoType[]>([])
    const [isFetching, setIsFetching] = useState(true)

    useEffect(() =>{
        axios
            .get<TodoType[]>("https://exampleurl.com/example")
            .then((res) => {
                setData(res.data)
            })
            .catch((e) => {
                console.log(e)
            })
            .finally(() => {
                setIsfetching(false)
            })
    },[])

    return { data, isFetching}
}

export default useFetchTodo
```

```tsx ../src/todo/index.tsx
import useFetchTodo from "./useFetchTodo.tsx"

const TodoList = () => {
    const { data, isFetching} = useFetchTodo()

    if(isFetching) return <p>... loading </p>

    return (
        <ul>
            {data.map((todo) => {
                return (
                    <li>
                        <span>{todo.id}</span>
                        <span>{todo.tittle}</span>
                    </li>
                )
            })}
        </ul>
    )
}

export default TodoList
```

 Ahora nuestro componente `TodoList` respeta el **SRP**. Pero nuestro custome hook no, ya que maneja 2 estados y el fetch. Un estado para revisar si el fetch fue realizado (para dejar "cargando" el sitio) y otro para guardar los datos del mismo. Lo siguiente a hacer es simple, refactorizar hasta que se cumpla el **SRP**. Esto trae muchas ventajas, como son la reutilización, la legibilidad, entre otros.

# OCP - Open/Closed Principle (Principio de Abierto/Cerrado)

 Las entidades que tengamos deben estar abiertas para **extenderse** pero cerradas para ser **modificadas**. La idea es evitar que un componente tenga que ser modificado por dentro manteniendo la escalabilidad. La palabra **extenderse** es clave en este concepto y se entenderá mejor en el siguiente ejemplo.

## Ejemplo OCP

```tsx
import { FC } from "react"

type Props = {
    title: string;
    type: "defaul" | "withLinkButton" | "withNormalButton";
    href?: string;
    buttonText?: string;
    onClick?: () => void;
}

const Title: FC<Props> = ({
    title,
    type,
    href,
    buttonText,
    onClick,
}) => {
    return (
        <div style={{ display: "flex", justifyContent: "space-between"}}>
            <h1>{title}</h1>

            {type === "withLinkButton" && (
                <div>
                    <a href={href}>{buttonText}</a>
                </div>
            )}

            {type === "withNormalButton" && (
                <button onClick={onClick}>{buttonText}</button>
            )}
        </div>
    )
}

export default Title
```
 El problema de este componente es que al añadir un nuevo tipo de boton debemos modificar el código de nuestro `Title` matando completamente la escalibilidad dentro del mismo y violando el principio en cuestión.
 
```tsx
import {FC, ReactElement} from "react"

type TitleProps = {
    children: ReactElement;
    title: string;
}

const Title: FC<TitleProps> = ({ title, children }) => {
    return (
        <div style={{ display: "flex", justifyContent: "space-between"}}>
            <h1>{title}</h1>
            {children}
        </div>
    )
}

type TitleWithLinkProps = {
    title: string;
    href: string;
    buttonText: string;
}

export const TitleWithLinks: FC<TitleWithLinkProps> = (
    {title, href, buttonText}
) => {
    return(
        <Title title={title}>
            <div>
                <a href={href}>{buttonText}</a>
            </div>
        </Title>
    )
}

type TitleWithButtonProps = {
    title: string;
    buttonText: string;
    onClick: () => void;
}

export const TitleWithButton: FC<TitleWithButtonProps> = (
    {title, buttonText, onClick}
) => {
    return (
        <Title title={title}>
            <button onClick={onClick}>{buttonText}</button>
        </Title>
    )
}
```

 Ahora nuestro componente `Title` ya no necesita ser **modificado** y puede **extenderse**.

# LSP - Liskov Substitution Principle (Principio de Sustitución de Liskov)

 Este principio establece que los objetos de una clase derivada deben poder ser **sustituidos** por objetos de su clase base o derivada sin alterar el funcionamiento del programa.

## Ejemplo LSP
```tsx
// Button.tsx
interface ButtonProps {
  onClick: () => void;
}

const Button: React.FC<ButtonProps> = ({ onClick, children }) => {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
};

// DangerButton.tsx
interface DangerButtonProps extends ButtonProps {
  onClick: () => void;
}

const DangerButton: React.FC<DangerButtonProps> = ({ onClick, children }) => {
  const handleClick = () => {
    alert("¡Cuidado! ¡Esto es peligroso!");
    onClick();
  };

  return (
    <button onClick={handleClick}>
      {children}
    </button>
  );
};

// App.tsx
const App = () => {
  const handleButtonClick = () => {
    console.log("Botón normal clickeado");
  };

  return (
    <div>
      <h1>Botones:</h1>
      <Button onClick={handleButtonClick}>Botón normal</Button>
      <DangerButton onClick={handleButtonClick}>Botón peligroso</DangerButton>
    </div>
  );
};
```
 En este ejemplo, `DangerButton` extiende `Button` pero redefine el comportamiento del `onClick`. Alterando el funcionamiento inicial de `Button` y perpetrando el principio de sustitución de Liskov (LSP).

```tsx
// Button.tsx
interface ButtonProps {
  onClick: () => void;
}

const Button: React.FC<ButtonProps> = ({ onClick, children }) => {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
};

// DangerButton.tsx
interface DangerButtonProps extends ButtonProps {
  onDangerClick: () => void;
}

const DangerButton: React.FC<DangerButtonProps> = ({ onClick, onDangerClick, children }) => {
  const handleClick = () => {
    alert("¡Cuidado! ¡Esto es peligroso!");
    onDangerClick();
  };

  return (
    <button onClick={handleClick}>
      {children}
    </button>
  );
};

// App.tsx
const App = () => {
  const handleButtonClick = () => {
    console.log("Botón normal clickeado");
  };

  const handleDangerButtonClick = () => {
    console.log("Botón peligroso clickeado");
  };

  return (
    <div>
      <h1>Botones:</h1>
      <Button onClick={handleButtonClick}>Botón normal</Button>
      <DangerButton onClick={handleButtonClick} onDangerClick={handleDangerButtonClick}>Botón peligroso</DangerButton>
    </div>
  );
};
```

 En esta versión corregida, `DangerButton` ahora tiene un propiedad adicional, `onDangerClick`, para manejar la funcionalidad específica de advertencia, mientras mantiene el `onClick` estándar que espera cualquier botón. Esto asegura que `DangerButton` pueda ser **sustituido** por `Button` en cualquier lugar donde se espere un botón estándar, sin cambiar el comportamiento esperado del programa.

# ISP - Interface Segregation Principle (Principio de Segregación de Interfaz)

 El Principio de Segregación de Interfaz propone que **el cliente no debe depender de interfaces que no necesitan** y pretende minimizar las dependencias entre componentes del sistema. 

## Ejemplo ISP

 En el siguiente ejemplo veremos como, el cliente (en este caso `Thumbnail`), recibe propiedades que no necesita realmente.

```tsx ..src/components/VideoList.tsx
...
type Video = {
  title: string;
  duration: number;
  coverUrl: string;
};

type Props = {
  items: Array<Video>;
};

const VideoList = ({ items }: Props) => {
  return (
    <ul>
      {items.map(item => (
        <Thumbnail
          key={item.title}
          video={item}
        />
      ))}
    </ul>
  );
};
...
```

```tsx ..src/components/Thumbnail.tsx
type Props = {
    video: Video
}

const Thumbnail = ({ video }: Props) => {
    return <img src={video.coverUrl} />
}
```

 Nuestro componente `Thumbnail` es pequeño pero tiene un problema: espera, y por ende **depede**, todo un objeto a ser pasado por props, cuando realmente sólo está usando y **necesita** una de sus propiedades.

```tsx ..src/components/VideoList.tsx
...
type Video = {
  title: string;
  duration: number;
  coverUrl: string;
};

type Props = {
  items: Array<Video>;
};

const VideoList = ({ items }: Props) => {
  return (
    <ul>
      {items.map(item => (
        <Thumbnail
          key={item.title}
          coverUrl={item.coverUrl}
        />
      ))}
    </ul>
  );
};
...
```

```tsx ..src/components/Thumbnail.tsx
type Props = {
    coverUrl: string;
}

const Thumbnail = ({ coverUrl }: Props) => {
    return <img src={coverUrl} />
}
```

 En esta versión refactorizada `Thumbnail` ya no **depende** de un objeto entero que **no necesita** 

# DIP - Dependency Inversion Principle (Principio de Inversión de Dependencia)

 El DIP propone que las funciones y clases deberían depender de abstracciones en lugar de implementaciones concretas y que las abstracciones no deben depender de los detalles pero los detalles si de las abstracciones.

## Ejemplo DIP

```tsx
import {FC} from 'react';

interface UserListProps {
  users: string[];
}

const UserList: FC<UserListProps> = ({ users }) => {
  return (
    <div>
      <h2>Lista de Usuarios:</h2>
      <ul>
        {users.map((user, index) => (
          <li key={index}>{user}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

 En este caso, `UserList` depende  directamente de un array de strings (linea 425) para renderizar los nombres de usuario. Perpetrando el DIP. 

 Para solucionarlo introduciremos una abstraccion, `UserRepository`, que represente la fuente de datos. Luego `UserList` dependerá de esta abstracción. 

```tsx
// ../src/components/UserRepository.ts
export interface UserRepository {
  getUsers(): string[];
}

export class MockUserRepository implements UserRepository {
  getUsers(): string[] {
    return ['Alice', 'Bob', 'Charlie'];
  }
} 
```

```tsx
// ../src/components/UserList.tsx
import React from 'react';
import { UserRepository } from './UserRepository';

interface UserListProps {
  userRepository: UserRepository;
}

const UserList: React.FC<UserListProps> = ({ userRepository }) => {
  const users = userRepository.getUsers();

  return (
    <div>
      <h2>Lista de Usuarios:</h2>
      <ul>
        {users.map((user, index) => (
          <li key={index}>{user}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

 Al aplicar el Dependency Inversion Principle, hemos mejorado la modularidad y la flexibilidad de nuestro código, lo que facilita la extensibilidad y el mantenimiento a medida que el sistema evoluciona.

# Conclusión

 Debo escribir una conclusión, pero toy re cansao
