---
title: "Setup proyecto React"
date: "2020-05-11"
---

Hace poco se nos dio la oportunidad de comenzar un nuevo proyecto web desde 0, y queremos traerles una documentación de las implementaciones realizadas desde el principio.
En Edutive decidimos implementar React. Una librería de JavaScript desarrollada principalmente por Facebook que sirve para construir interfaces de usuario. Esta respaldada por una gran comunidad, y mes a mes tiene un crecimiento exponencial. Este articulo no es un tutorial de cómo funciona React, sino que vamos a ver cómo sería implementar React desde 0, y las decisiones que fuimos tomando ( y por qué ) a la hora de integrar las librerías que conforman la estructura principal de nuestro proyecto. Comencemos!

## Inicializar React

Lo primero que hicimos fue crear el proyecto en React. Existen diversas formas de hacerlo, pero en este articulo utilizaremos una de las más sencillas. Nos evitara varios dolores de cabeza y se encargara de las configuraciones iniciales necesarias para comenzar a trabajar sin problemas.
Teniendo instalado yarn o npm, arrojaremos cualquiera de los siguientes comandos dentro del directorio donde querramos crear nuestro proyecto.

`npx create-react-app my-app o yarn create react-app my-app`

Así nos habrá creado una carpeta con el nombre de la app con todo el proyecto dentro de esta misma. Corriendo el siguiente comando dentro de la carpeta root de nuestro proyecto podremos visualizar nuestra pagina en http://localhost:3000/
npm start
A partir de aquí vamos a utilizar los comandos propios de npm, pero pueden seguir con yarn sin ningún problema. Para información más detallada acerca del setup inicial del proyecto visiten la pagina oficial de React!

## TypeScript

Sí … estamos utilizando TypeScript! decidimos implementarlo de raíz para no tener problemas a futuro, ya que es mucho más fácil integrarlo al comienzo y no cuando el proyecto sea mas grande. Al principio puede ser difícil, pero esta librería nos va a permitir evitar muchos issues a lo largo de nuestro proyecto. Dos grandes beneficios de utilizarlo son:

- Al ser completamente tipado, TypeScript nos permite evitar muchos errores a la hora de desarrollar ya que deberemos detallar mucho mejor nuestro código ( lo que lo hace más legible a la hora de trabajar en equipo! ).
- TS es compatible con nuestro código JS, así que si ya tenemos un proyecto en curso vamos a poder ir migrando los archivos .js de a poco sin ningún tipo de problema.
  Instalarlo es realmente sencillo, solo debemos pararnos sobre el root de nuestro proyecto y arrojar el siguiente comando.
  npm install — save typescript
  Además de integrar typescript necesitaremos los types definitions de todas ( o al menos de las que lo necesitan ) las librerías que vayamos instalando posteriormente. Para eso deberemos agregar un @types/ como prefijo.
  npm install — save-dev @types/react @types/react-dom
  En este caso estamos obteniendo todos los declarations files de react y react-dom ( previamente instalados con create-react-app ) y agregamos el — save-dev para que solo se instalen en las dev dependencies.
  Ahora toca ir pasando todos los archivos .js a .tsx y listo! los errores que se vayan encontrando en los files .tsx son propios de typescript y van a necesitar apoyarse en un curso o en la documentación correspondiente para ir solucionándolos.

## ESLint

Antes de adentrarnos en el tema necesitamos entender por qué queremos utilizar ESLint. Un linter es un software que analiza tu código de forma estática, esto significa que recorre tu código fuente sin necesidad de ejecutarlo. Se aplica en todo tipo de desarrollos y sirve para evitar errores comunes, potenciales bugs, malas practicas, y muchas más reglas personalizadas que vamos a ir agregando a medida que lo veamos necesario.
Comencemos instalando ESLint, una herramienta open source que nos va a proveer lo necesario para lintear nuestro código JS. Sobre la carpeta root de nuestro proyecto arrojaremos el siguiente comando:
npm install — save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
Ahora, para configurarlo, necesitaremos seguir los siguientes pasos:

- Crearemos un archivo .eslintrc, sobre el root de nuestro proyecto, donde se encontraran todas las configuraciones. Con el paso del tiempo y leyendo la documentación podrán ir modificando las rules de esta herramienta como mejor les quede a su equipo, de momento pueden copiar las siguientes dentro de su archivo para tener un buen punto de partida:

{
"parser": "@typescript-eslint/parser",
"extends": [
"plugin:react/recommended",
"plugin:@typescript-eslint/recommended",
"prettier/@typescript-eslint",
"plugin:prettier/recommended"
],
"parserOptions": {
"ecmaVersion": 2018,
"sourceType": "module",
"ecmaFeatures": {
"jsx": true
}
},
"rules": {
// TODO: modify certain rules as required
"no-console": "error",
"@typescript-eslint/explicit-function-return-type": "off"
},
"settings": {
"react": {
"version": "detect"
}
}
}

Ya agregamos ciertas validaciones de prettier que veremos más adelante.

- Crearemos otro archivo llamado .eslintignore que nos permitirá agregar los files a los que no querramos que se apliquen estas reglas. Un buen ejemplo puede ser evitar los node modules, para eso solo debemos copiar lo siguiente:
  \*\*/node_modules
- Y por último, es altamente recomendado instalar la extension de ESLint para visual code.

## Prettier

Es una herramienta que te permite formatear tu código de forma automática y así añadir consistencia a este mismo. Agregando ciertas rules en las configuraciones de prettier podemos lograr que todos los developers mantengan un estandar en su forma de programar, lo cual en equipos grandes representa una ventaja muy significativa.
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
Una vez instalado vamos agregar la extensión de VS Code Prettier — Code Formatter. Luego, iremos al archivo settings.json y agregaremos la siguiente linea:
“editor.formatOnSave”: true
Ahora cada vez que guardemos un archivo ( ctrl + s ) formateara nuestro código de forma automática. Pero, que rules va a tomar en cuenta? las que agreguemos en un archivo root llamado .prettierrc. Un ejemplo puede ser:
{
“printWidth”: 120, // width máximo de una linea de código
“semi”: false, // si se aceptan “;” o no
}
Se pueden agregar todas las reglas que se imaginen! para más información pueden visitar la página oficial de Prettier.

## Styled Components

Llegó el momento de elegir cómo queremos manejar los estilos de nuestro proyecto. Existen alternativas muy buenas en el mercado para manejar styles dentro de React ( Sass, Emotion, etc .. ) pero luego de mucho research decidimos optar por la librería Styled Components. La elección depende completamente de a donde apunta tu proyecto, la estructura que elijas y cómo estes acostumbrado a trabajar. A pesar de que nosotros siempre utilizamos CSS puro en un file externo, vimos varias ventajas para migrar a esta librería. Algunas de estas pueden ser:

- Utiliza una técnica muy interesante llamada CSS-in-JS la cual tiene una gran cantidad de ventajas. En este artículo podemos encontrar una comparación muy detallada con respecto a utilizar plain CSS
- Gracias a que podemos utilizar props dentro de nuestros estilos podremos manipular el CSS de nuestro componente fuera de este mismo, y así no tener que utilizar condicionales dentro de nuestro component para modificar los styles.
  También styled-compoments tiene un uso mucho más avanzado como determinar themes específicos para tu página web, soporte de server-side rendering y diversos beneficios a la hora de implementar jest para testear tus componentes.
  Para instalarlo solo debemos correr el siguiente comando
  npm install — save styled-components
  A partir de aquí se recomiendan leer diversas guías sobre cómo implementar esta librería, ya que a pesar de que tiene una curva de aprendizaje baja si vamos a tener que prestar atención a la documentación y a sus buenas practicas.

## Storybook

Una parte fundamental dentro de cualquier proyecto es la documentación, aún más cuando estamos hablando de una empresa 100% remota. Es fundamental para comunicarse entre los diferentes sectores de la empresa y entre tus propios compañeros. Una documentación clara que te lleva solo unos minutos desarrollar puede ahorrar incontables horas de trabajo. Pero, cómo podemos lograr esto con nuestros componentes front-end?

> Storybook is a user interface development environment and playground for UI components

Storybook es una herramienta que podemos ejecutar por fuera de nuestro proyecto principal, y nos va a permitir crear componentes de forma independiente sin necesidad de aplicarlos en una screen para comenzar a probarlos. Esto nos dará una gran autonomía a la hora de desarrollar, y en conjunto a otras poderosas herramientas que nos ofrece este software podremos generar una documentación muy detallada de todos nuestros componentes UI.

https://storybook.js.org/docs/basics/introduction/
Para instalarlo solo debemos correr el siguiente comando en el root de nuestro proyecto:
npx -p @storybook/cli sb init
Vamos a ver que nos genero una carpeta llamada .storybook con dos archivos .js dentro:

- **addons.js:** acá vamos a registrar addons que nos permitirán realizar muchas más acciones dentro de nuestro proyecto.
- **config.js:** en este archivo se va a configurar todo lo necesario para ejecutar storybook. Vamos a encontrar que el path por defualt es ‘/src/stories’

También nos vamos a encontrar con una nueva carpeta llamada stories que contiene un index.js donde se encontraran todas las historias que desarrollaremos para nuestros componentes. La locación del archivo es completamente configurable y pueden modificarla en el config.js.
Recomiendo revisar esta lista de reproducción en la cual podremos ver mejores practicas para organizar nuestra estructura de carpetas a la hora de utilizar storybook!
Las librerías que estuvimos repasando fueron algunas de las implementadas al comienzo del proyecto. En este articulo dejamos de lado varias importantes — y que también utilizamos — como axios o lodash, pero como son un poco más avanzadas vamos a destinarle todo un post entero.
