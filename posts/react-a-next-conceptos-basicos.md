---
title: "React a NextJS: 5 conceptos básicos"
date: "2022-04-07"
---

![](https://assets.vercel.com/image/upload/q_auto/front/assets/design/black-nextjs.png)

Los siguientes puntos son resultado de notas que tome al tener que comenzar un proyecto en NextJS viniendo de un background de React. La documentación que nos regala el equipo de Vercel es super clara y estas notas son producto de haber entrado en un rabbit hole hermoso de articulos y ejemplos. No es más que un recopilado sencillo de todo lo que se pueden encontrar en los docs de React y Next.
Para mayor facilidad partamos de la base que nuestra página web se llama https://www.mypage.com y que cada vez que nombre a React me estaré refiriendo al React base (es peculiar tener que aclarar que un framework es la versión base de lo que estamos hablando)

## Rutas

A diferencia de React que necesitamos declarar las rutas de forma explicita en NextJS las rutas se crean automáticamente a partir de los archivos que agreguemos bajo una carpeta root llamada Pages y cada una es asociada a una ruta a través del nombre del file. Por definición una page (aka página de nuestra app) es un componente de React que se exporta desde un file dentro de la carpeta pages. Veamos esto con dos ejemplos.
Si bajo la carpeta pages tenemos un file llamado home (pages/home.js) se creará la ruta https://www.mypage.com/home.
Si dentro de pages creamos otra carpeta llamada recips y dentro tiene dos archivos llamados cake.js y bread.js se crearan las rutas https://www.mypage.com/recips/cake y https://www.mypage.com/recips/bread.

Siempre el path, desde la carpeta pages, se convierte en el path de la URL.
También NextJS nos introduce al concepto de rutas dinámicas y lo vamos a utilizar cuando una page depende de data externa (es decir, cuando es necesario fetchear data para renderizarle la página al usuario). En este caso Next se va a encargar de generar estáticamente cada una de estas páginas (SSG - Se explica más abajo). La implementación es sencilla pero es necesario saber bien como tipar y usar las funciones requeridas, y es imposible explicarlo mejor que en la documentación oficial de Dynamic Routes de NextJS.

## Navegación

En React, y javascript en general, utilizamos el tag `<a href="…" />` cuando queremos navegar hacia alguna ruta de nuestra aplicación. En NextJS, si queremos navegar entre pages de nuestra App, vamos a continuar utilizando el mismo tag solo que lo vamos a wrappear con el componente `<Link>` que importaremos desde "next/link". Utilizando este componente la navegación se produce del lado del cliente (client-side) lo que significa que se ejecuta usando javascript y no la navegación por default del browser. Esto permite que la web no haga un refresh completo de la aplicación y por contraste la navegación a otra página se ejecuta mucho más rápido.

- React: `<a href={'/posts/${id}'}>{title}</a>`
- Next: `<Link href={`/posts/${id}`}><a>{title}</a></Link>`

Por último, pero sumamente importante, cuando Next detecta que el tag `<Link>` aparece en el viewport del browser hace un pre-fetch de la página a la cual hace referencia así ya queda cargada en el background, y la navegación se producirá de forma instantánea. Acá ya se introduce el concepto de pre-fetching dentro de Next y es una de las razones por las cuales este framework es tan querido.

## División de código - code splitting

![](https://media.giphy.com/media/l0HlEV7ZbFe0zxF0Q/giphy.gif)

En React durante el último tiempo se volvió una buena práctica, pero no tan común, enfocarse en la división de código (code-splitting). Como es una práctica que, al principio, no muchos desarrolladores conocen hagamos un breve y burdo repaso:
En todos los frameworks nuevos de javascript (react, vue, +100) se utiliza un bundler, como webpack o rollup, para que el browser cargue la aplicación web completa de una sola vez. ¿Cómo lo hace? básicamente va siguiendo todos los imports que utilizamos en nuestro código y transforma nuestros files en un solo file. Así es! todo el código de cada import que hagamos de una función propia o de una aplicación de terceros pasa a estar en un mismo file. Podemos ver un ejemplo de código en la siguiente documentación de React
Pero qué pasa cuando nuestra aplicación empieza a crecer y a crecer? el file se termina haciendo muy grande y el browser igualmente va a querer cargar toda la aplicación de una sola vez y esto va a generar delays muy importantes. Acá es donde entra en juego la división de código, una técnica que dividirá un gran bundle en varios más chicos y los cargará de forma dinámica solo cuando sea necesario. De esta forma evitaremos que nuestra app tarde en cargar más de lo que nos gustaría (y soportaría el usuario). 
En react este proceso debe hacerse de forma manual utilizando la sintaxis de import dinámica o utilizando lazy y suspense para jugar con los tiempos de carga de los componentes:

- `import("./file").then(…);`
- `const OtherComponent = React.lazy(() => import('./OtherComponent'));`

Pero … ¿y cómo lo hacemos en Next? no es necesario preocuparnos por el code-splitting ya que el framework se encarga automáticamente de todo este proceso. No importa que tengamos cientos de páginas ya que solo cargará el código de la página que sea necesaria. Esto hace que las páginas se aislen (para más info buscar concepto isolated) lo cual significa que si una de estas falla la aplicación siga funcionando.

![](https://media.giphy.com/media/cXblnKXr2BQOaYnTni/giphy.gif)

Como ya vimos con el ejemplo del componente Link que lo utilizamos para wrappear el tag `<a>` existen por igual muchos componentes más que nos da next para sacarle el máximo provecho al framework, por eso es importante siempre estar al tanto de la documentación para entender que tags podemos utilizar en cada momento.
Un buen ejemplo es el tag `<img>` que utilizamos dentro de todos los proyectos de JavaScript. En este caso Next te propone importar su propio componente "Image" de "next/image" que no es más que una extensión del tag por default pero con varias características que nos van a permitir aprovechar toda la potencia del framework. Este es un ejemplo simple pero si indagamos un poco más vamos a poder ver que tenemos desde un componente `<Head>` que es utilizado para modificar la metadata de nuestras páginas hasta un componente `<Script>` que fetchea una library de la forma más optima posible sin que nosotros nos tengamos que preocupar por sus tiempos de carga.
Pueden encontrar toda la API bajo "API Reference" dentro de la documentación de su página oficial

## Data fetching y pre-fetching

Una de las principales razones por la cual se migra a NextJS es por sus métodos de renderizado. Actualmente en la web existen tres métodos: client-side (CSR), static generation (SSG) y server-side rendering (SSR). El método utilizado por defecto en React es CSR y posiblemente es el que estes usando, y aunque no es tan complejo implementar SSR con node es un concepto que no es tan común encontrárselo en proyectos genéricos de empresas que utilizan CRA (Create React App). Si alguna vez buscaron como mejorar el SEO en un proyecto CRA posiblemente la solución era una suerte de SSR con node. En Next esto cambia porque desde el comienzo ya incorporamos el concepto de SSG y SSR como parte del framework y desde el minuto uno ya te empezas a encontrar, mínimo, con SSG porque es el método recomendado por los desarrolladores de Next por temas de performance y por si todavía no comprendes bien en que momento utilizar SSR. La documentación explica muy bien qué método elegir para cada ocasión así que solo voy a citar una excelente pregunta que ellos te recomiendan hacerte a la hora de elegir uno de los métodos: 
"Puedo hacer un pre-render de esta página antes que el usuario haga un request? Si la respuesta es sí, entonces deberías elegir SSG".

Dentro de lo que es client-side rendering (CSR) la diferencia que nos podemos encontrar es que el equipo de Next te recomienda utilizar un hook de ellos, llamado SWR, para fetchear data con CSR. Nos facilita la vida en un montón de aspectos para no tener que preocuparnos por el cacheo, handleo de errores, re-tries y varías características más que por lo general no se tienen en cuenta, o no tenemos el tiempo, a la hora de codear fetchs con React o Javascript vanilla.

---

## ¿Y ahora?

Para continuar con el traspaso de React a Next recomiendo perderse durante una noche en la siguiente doc hasta terminar deployando la app de ejemplo. No se van a arrepentir. 
Además hace unos días el equipo de Vercel saco una nueva documentación llamada Foundations que nos da contexto de cómo llegamos desde Javascript vanilla, pasando por React, hasta Next. Super recomendado.

---

_**¿Dudas?** hablemos por twitter - https://twitter.com/lfordev_
