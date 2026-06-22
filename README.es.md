# Contador de Calorías

Aplicación web de una sola página para registrar comidas y ejercicios y ver el balance calórico del día. Está construida con React 19, TypeScript y Vite. Los datos se guardan en el navegador usando `localStorage`, por lo que no requiere backend.

> Also available in English: [README.md](./README.md)

## Stack tecnológico

- **React 19** con **TypeScript** (modo strict)
- **Vite 6** con el plugin de SWC para HMR rápido
- **Tailwind CSS 4** mediante el plugin oficial de Vite (sin necesidad de configurar PostCSS)
- **Heroicons** para los íconos de editar y eliminar
- **uuid** para generar los identificadores de cada actividad

## Funcionalidades

- Registrar una entrada como *Comida* (suma a las calorías consumidas) o *Ejercicio* (suma a las calorías quemadas).
- Ver en tiempo real el total de calorías consumidas, quemadas y la diferencia neta.
- Editar o eliminar cualquier entrada directamente desde la lista.
- Reiniciar el historial completo con un clic.
- Las entradas se guardan automáticamente. Si cierras la pestaña y vuelves a abrirla, los datos siguen ahí.

La interfaz está en español de forma intencional, manteniendo los textos originales del producto.

## Cómo arrancar el proyecto

Requisitos: Node.js 18 o superior y npm.

```bash
npm install
npm run dev
```

Luego abre la URL que aparece en la terminal (normalmente `http://localhost:5173`).

| Comando | Para qué sirve |
| --- | --- |
| `npm run dev` | Levanta el servidor de desarrollo con HMR |
| `npm run build` | Verifica los tipos con `tsc -b` y genera el build de producción |
| `npm run preview` | Previsualiza localmente el build de producción |
| `npm run lint` | Ejecuta ESLint sobre todo el proyecto |

El build de producción falla si TypeScript reporta cualquier error.

## Estructura del proyecto

```
src/
├── components/
│   ├── ActivityList.tsx     // Renderiza la lista y los botones de editar/eliminar
│   ├── CalorieDisplay.tsx   // Tarjeta individual de número (consumidas / quemadas / neto)
│   ├── CalorieTracker.tsx   // Calcula y muestra los totales
│   └── Form.tsx             // Formulario para agregar y editar
├── data/
│   └── categories.ts        // Categorías estáticas: Comida (1), Ejercicio (2)
├── reducers/
│   └── activity-reducer.ts  // Todas las transiciones de estado
├── types/
│   └── index.ts             // Tipos Activity y Category
├── App.tsx                  // Layout, conexión del reducer y escritura en localStorage
└── main.tsx                 // Punto de entrada
```

## Cómo funciona por dentro

**Estado.** Un solo `useReducer` en `App.tsx` maneja todo lo que necesita la UI. La forma es `{ activities, activeId }` y cuatro acciones cubren la funcionalidad completa: `save-activity`, `set-activeId`, `delete-activity` y `restart-app`. No se utiliza Redux, Zustand ni Context. Para una app de este tamaño las herramientas que vienen con React son suficientes y mantienen el código fácil de seguir.

**Persistencia.** El reducer lee la lista de actividades desde `localStorage` cuando se inicializa, y un efecto en `App.tsx` la guarda cada vez que cambia. Solo se persiste la lista; `activeId` se trata como estado temporal de la UI.

**Edición reutilizando el formulario.** Cuando se hace clic en el ícono del lápiz, la lista despacha `set-activeId` con el id de esa entrada. El formulario observa `state.activeId`, copia la actividad correspondiente a su estado local, y al enviar despacha `save-activity`. El reducer detecta que `activeId` tiene valor y actualiza en su lugar en vez de añadir una entrada nueva.

**Totales memorizados.** `CalorieTracker` usa `useMemo` para calcular los totales por id de categoría: el id `1` suma a consumidas, el id `2` suma a quemadas, y la diferencia entre ambos es el neto.

## Posibles mejoras

Algunas ideas si alguien quiere extender el proyecto:

- Reemplazar `localStorage` por un backend real o IndexedDB para sincronizar entre dispositivos.
- Añadir una meta diaria y una barra de progreso.
- Agrupar las entradas por fecha en lugar de mostrar una sola lista plana.
- Agregar pruebas unitarias para el reducer (actualmente no hay configuración de testing).
- Desplegar una versión en vivo (Vercel, Netlify o GitHub Pages).
