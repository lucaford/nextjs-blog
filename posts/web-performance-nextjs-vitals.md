---
title: "Web Performance - NextJS Vitals"
date: "2022-07-18"
description: "Como mejorar la performance de tu sitio web utilizando los Core Web Vitals y los vitals de NextJS"
---

![](https://images.unsplash.com/photo-1571008887538-b36bb32f4571?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=2940&q=80)

Uno de los objetivos principales a la hora de desarrollar un sitio web es asegurar que la experiencia del usuario, al navegar por nuestro sitio, sea optima. No por nada existen posiciones laborales como la del diseñador UX que están 100% orientadas a estudiar cómo mejorar la experiencia del usuario. Desde el lado de desarrollo front-end debemos asegurarnos que nuestras implementaciones no afecten esa experiencia. De hecho, es tan importante considerar la UX a la hora de desarrollar que hasta buscamos implementar nuevas soluciones complejas como SSG y SSR con tal de acelerar la performance de nuestro sitio. Pero, **¿cómo podemos darnos cuenta que lo que hacemos realmente impacta de forma positiva?** O mejor aún, que ninguna de las implementaciones que realicemos bajen la calidad de nuestra UX. Acá entran en juego los **Web Vitals: un conjunto de métricas estandarizados por el equipo de Google que nos permite medir la performance de nuestro sitio en diferentes campos como carga, interactividad y estabilidad visual.** Nosotros nos enfocaremos en los 3 Core Web Vitals: LCP, FID, CLS. Desde Google definen que utilizando estos 3 vitals ya es suficiente como para entender el rendimiento de nuestra plataforma. Además de estos, usando NextJS, tendremos el agregado de 3 métricas más que nos permiten medir la performance, específicamente, en el cambio de las rutas: **Hydration, Route-change-to-render y render.**

### LCP (Largest Contentful Paint)

Mide la velocidad en la que tarda en cargar el contenido más extenso del sitio web. Calcula el tiempo en que tarda en renderizar el bloque visible más grande dentro de la ventana que se esta visualizando.
Para considerarse optimo, el bloque debe tomar menos de 2.5 segundos desde que la página comienza a cargar. Se tiene en cuenta que el tiempo de respuesta hasta que una persona pierda el enfoque esta entre 0.3 y 3 segundos.

### FID (First Input Delay) — Interactividad.

Mide la interactividad entre los usuarios y el sitio. Calcula el tiempo que tarda la web en responder ante la primera acción del usuario, cómo clickear un enlace o un botón. Para considerarse optimo, el FID debe tener un tiempo menor a 100 milisegundos.

### CLS (Cumulative Layout Shift) — Estabilidad visual

Mide la frecuencia con la que los usuarios perciben cambios inesperados en el diseño del sitio. Esto puede darse porque los elementos cargan de forma asincrónica o se agregan componentes de forma dinámica haciendo que el contenido ya renderizado (visible) cambie de una posición a otra. Cuando ocurren uno o más cambios de diseño de forma sucesiva y con menos de 1 segundo de diferencia se produce una “ventana de sesión”. Esta ventana tiene una duración máxima de 5 segundos. La ventana de sesión más grande será la que tenga la puntuación acumulada máxima de todos los cambios de diseño dentro de esa ventana. Esta puntuación es calculada a través del tamaño de la ventana de visualización y el cambio de posición de los elementos.

![](https://media.giphy.com/media/WsNbxuFkLi3IuGI9NU/giphy-downsized.gif)

El concepto puede resultar un poco complejo de entender (y de explicar, no los culpo). Esta [explicación detallada](https://web.dev/cls/) se los puede dejar más en claro.
Un tiempo optimo es que la puntuación CLS sea de 0.1 o menos

### TTFB (Time to First Byte)

Alejándonos de los vitals core tenemos el TTFB.Como esta definido en la documentación de MDN, mide el tiempo que tarda el servidor en responder con el primer byte desde que el browser lanza la primera request para obtener la página. Este tiempo se obtiene en milisegundos y hasta tiene en cuenta la búsqueda del DNS. No podemos considerar al TTFB como una métrica de velocidad del sitio web, sino que debemos tomarlo como una **medida de la capacidad de respuesta.**

# NextJS Vitals

El framework tiene algunos vitals propios que son muy interesantes y nos van a ayudar a medir la performance de nuestra web, más específicamente el renderizado de nuestras páginas.

### Next.js-hydration (Hydration)

Es el tiempo que tarda la página desde que empieza hasta que termina en hidratarse. ([Hydration process](https://www.youtube.com/watch?v=fypmxYZGrfA))

### Next.js-route-change-to-render (Start to render)

Es el tiempo que tarda en **empezar** a renderizar una página desde que cambio la ruta.

### Next.js-render (Finish to render)

Es el tiempo que una página tarda en **terminar** de renderizar desde que cambio la ruta.

# Implementación

Los “web vitals” nos servirán en cualquier proyecto web. La mayoría de herramientas de análisis, como google analytics, ya traen una solución built-in que nos dirán las métricas de nuestro sitio sin que nosotros tengamos que implementar código. Pero si queremos profundizar un poco más y sacarle el máximo provecho NextJS nos trae una solución muy sencilla: **la función reportWebVitals**.
Solo tendremos que definir una función, dentro de \_app.tsx, llamada **reportWebVitals** que tiene como parámetro un objeto con los detalles de la métrica.

```
import type { AppProps, NextWebVitalsMetric } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
return <Component {…pageProps} />
}

export function reportWebVitals(metric: NextWebVitalsMetric) {
console.log(metric)
}

export default MyApp
```

En este caso la función ejecutaría un console.log() con el objeto metric cada vez que se registre algún web vital nuevo. 
El objeto metric nos trae diversos atributos pero los que nos importan en un principio son el **nombre — metric.name** y el **valor — metric.value**. Los podremos identificar a través del nombre ya que corresponde a las abreviaturas de los vitals ya mencionados. De esta forma se podría acceder utilizando un simple switch.

```
import type { AppProps, NextWebVitalsMetric } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
return <Component { …pageProps} />
}

export function reportWebVitals(metric: NextWebVitalsMetric) {
switch (metric.name) {
case "FCP":

      break;
    case "LCP":

      break;
    case "CLS":

      break;
    case "TTFB":

      break;
    case "Next.js-hydration":

      break;
    case "Next.js-route-change-to-render":

      break;
    case "Next.js-render":

      break;

    default:
      break;

}
}

export default MyApp
```

# Recomendación al clasificarlos

Para clasificar el rendimiento del sitio es recomendable considerar el percentil 75 de todas las visitas que tenga el sitio. Por ejemplo, si el 75% de las visitas tiene un LCP menor a 2.5 segundos podemos decir que el sitio esta performando bien. Se usa ese porcentaje porque se considera que el resto pueden ser valores atípicos que terminen impactando de forma negativa, y no real, a la métrica.

---

A partir de acá ya podemos integrar los web vitals al software de análisis de datos que queramos: ya sea directamente con Google Analytics o con una herramienta como MixPanel a través de Segment. De esta forma, con gráficos muy simples, podremos mantener tracking de cómo performa nuestro sitio web. Se pueden hacer infinidades de cosas: podríamos generar notificaciones ante cambios abruptos en lo valores de los vitals o hasta se podría crear un flujo donde los tests e2e corran automáticamente cada vez que un desarrollador genere un PR y así registrar las las métricas de los vitals de ese test.

---

¿Dudas? **hablemos** por twitter -  https://twitter.com/lfordev
