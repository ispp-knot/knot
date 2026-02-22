---
sidebar_position: 2
---

# Instalación del frontend

## Dependencias
### `Framework y renderizado`
Next.js + React + React DOM (*next, react, react-dom*): Construyen la aplicación web, gestionan páginas, rutas, renderizado y la interfaz de usuario.

### `Estilos y UI`
Tailwind CSS stack (*tailwindcss, @tailwindcss/postcss, tailwind-merge, tw-animate-css*): Sistema de estilos por clases, animaciones y gestión de conflictos entre estilos.

Sistema de componentes (*radix-ui, shadcn, class-variance-authority, clsx*): Creación de componentes reutilizables, accesibles y con estilos dinámicos según estado/props.

Iconos (*lucide-react*): Iconos SVG listos para usar como componentes React.

### `PWA / Offline / Caché`
Service Workers / Offline (*serwist, @serwist/next*): Permiten que la app cargue más rápido, funcione sin conexión y guarde datos en caché.

### `Tipado`
TypeScript + tipos (*typescript, @types/node, @types/react, @types/react-dom*): Tipado estático, autocompletado y detección de errores antes de ejecutar la app.

### `Calidad de código`
Linting (*eslint, eslint-config-next*): Detectan errores, malas prácticas y mantienen consistencia en el código.

## Instalación
Todas las dependencias están en el package.json, ejecutando el siguiente comando se debería de instalar todo lo necesario para ejecutar el frontend:

```bash
npm install
```

Luego para ejecutar en local:

```bash
npm run dev
```

Tambien se tiene un archivo *docker-compose* por si se quiere ejecutar en un contenedor de docker.

Para ello, se debera tener docker instalado e iniciado (para usuarios de Windows se necesita tener [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/))

Luego de tener iniciado Docker, ejecutar para iniciar el contenedor:

```bash
docker compose -d up
```
para apagarlo:

```bash
docker compose down
```

Por último, antes de comenzar, se aconseja encarecidamente consultar la sección de [linting automático para el frontend](../guia-de-desarrollo/lint.md#linting-del-frontend).
