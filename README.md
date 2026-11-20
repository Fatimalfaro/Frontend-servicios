# Frontend -- Servicios de Programación

Aplicación web para explorar un catálogo de servicios de programación,
consultar sus detalles, crear una cuenta, iniciar sesión, administrar un
catálogo y realizar compras mediante un carrito integrado con Mercado
Pago.

**Demo:** https://servicios-de-programacion.netlify.app\
**Backend:** https://backend-servicios-smzy.onrender.com\
**Repositorio:** \[Agregar URL del repositorio\]

## Tecnologías

-   React 19
-   TypeScript
-   Vite
-   React Router
-   Tailwind CSS 4
-   React Hook Form
-   React Icons
-   SweetAlert2

## Requisitos

-   Node.js
-   pnpm o un gestor compatible con el proyecto
-   Backend disponible (local o desplegado)

## Instalación y ejecución

``` bash
git clone <URL_DEL_REPOSITORIO>
cd frontend-servicios
pnpm install
```

Crear un archivo `.env` en la raíz del frontend:

``` env
VITE_SERVICIO=http://localhost:3000/api
```

`VITE_SERVICIO` debe contener la URL base de la API, incluyendo el
prefijo `/api`. Para utilizar el backend publicado:

``` env
VITE_SERVICIO=https://backend-servicios-smzy.onrender.com/api
```

Iniciar el servidor de desarrollo:

``` bash
pnpm dev
```

Abrir la URL que informa Vite (habitualmente `http://localhost:5173`).

Generar una compilación de producción:

``` bash
pnpm build
```

Previsualizar la compilación:

``` bash
pnpm preview
```

Ejecutar el análisis estático:

``` bash
pnpm lint
```

## Estructura principal

``` text
frontend-servicios/
├── index.html
└── src/
    ├── components/
    │   ├── pages/
    │   ├── routes/
    │   ├── services/
    │   └── shared/
    ├── context/
    ├── helpers/
    ├── interfaces/
    ├── utils/
    ├── App.tsx
    ├── index.css
    └── main.tsx
```

-   `components/pages`: pantallas principales de la aplicación.
-   `components/routes`: protección de rutas privadas.
-   `components/services`: componentes para mostrar servicios y filas de
    administración.
-   `components/shared`: menú y pie de página.
-   `context`: contexto global de sesión y carrito.
-   `helpers/queries.ts`: funciones para consumir la API.
-   `interfaces`: tipos de TypeScript.
-   `utils`: funciones auxiliares, como el formateo de precios.

## Funcionalidades

### Catálogo público

-   Visualización de servicios disponibles.
-   Búsqueda por término.
-   Paginación.
-   Vista de detalle de cada servicio.
-   Visualización de categoría, descripción, imagen y precio.

### Usuarios

-   Inicio de sesión.
-   Consulta de la sesión existente al cargar la aplicación.
-   Cierre de sesión.
-   Interfaz adaptada según el rol del usuario.

El registro y la verificación de correo se implementan en el backend; en
los archivos frontend revisados no aparece una pantalla de registro o
verificación.

### Administración

El panel de administración permite:

-   Listar servicios en una tabla.
-   Crear servicios.
-   Editar servicios.
-   Eliminar servicios.
-   Navegar los resultados mediante paginación.

El acceso a las pantallas administrativas se controla mediante
`ProtectorRutas` y el usuario cargado en el contexto global.

### Carrito y compras

-   Agregar servicios al carrito.
-   Aumentar o disminuir cantidades.
-   Eliminar un servicio.
-   Consultar el total.
-   Crear una preferencia de pago.
-   Redirigir al checkout de Mercado Pago.
-   Mostrar una pantalla de resultado al regresar del checkout.

## Rutas de la aplicación

  Ruta                          Pantalla                  Acceso
  ----------------------------- ------------------------- -----------------------------
  `/`                           Inicio / catálogo         Público
  `/login`                      Inicio de sesión          Público
  `/detalle-servicio/:id`       Detalle de servicio       Público
  `/carrito`                    Carrito de compras        Requiere sesión para operar
  `/checkout/resultado`         Resultado del checkout    Público
  `/administrador`              Panel de administración   Administrador
  `/administrador/crear`        Formulario de creación    Administrador
  `/administrador/editar/:id`   Formulario de edición     Administrador
  `*`                           Página 404                Público

## Integración con el backend

Las funciones de comunicación con la API se centralizan en:

``` text
src/helpers/queries.ts
```

La URL base se obtiene de la variable de entorno `VITE_SERVICIO`. Los
recursos utilizados son:

-   `/servicios`
-   `/categorias`
-   `/usuarios`
-   `/carrito`
-   `/pago`

Las operaciones que necesitan la sesión envían `credentials: "include"`
para que el navegador incluya la cookie de autenticación.

### Ejemplos de operaciones consumidas

  Acción en la interfaz   Método   Endpoint
  ----------------------- -------- ---------------------------------
  Listar servicios        GET      `/servicios`
  Listar categorías       GET      `/categorias`
  Crear servicio          POST     `/servicios`
  Editar servicio         PUT      `/servicios/:id`
  Buscar detalle          GET      `/servicios/:id`
  Eliminar servicio       DELETE   `/servicios/:id`
  Iniciar sesión          POST     `/usuarios/login`
  Cerrar sesión           POST     `/usuarios/logout`
  Consultar perfil        GET      `/usuarios/perfil`
  Consultar carrito       GET      `/carrito`
  Agregar al carrito      POST     `/carrito`
  Restar cantidad         PATCH    `/carrito/restar/:servicioId`
  Eliminar ítem           DELETE   `/carrito/servicio/:servicioId`
  Crear preferencia       POST     `/pago/crear-preferencia`

## Flujo de prueba manual

1.  Configurar `VITE_SERVICIO` con la URL del backend.
2.  Iniciar el backend y comprobar que la API y MongoDB estén
    disponibles.
3.  Abrir la página de inicio y comprobar que se muestren los servicios.
4.  Abrir el detalle de un servicio.
5.  Iniciar sesión con un usuario previamente registrado y verificado.
6.  Agregar uno o más servicios al carrito.
7.  Modificar cantidades y comprobar el total.
8.  Iniciar el proceso de compra para solicitar una preferencia de
    Mercado Pago.
9.  Completar o cancelar el checkout y comprobar el retorno a
    `/checkout/resultado`.
10. Ingresar con un usuario administrador para probar el panel y las
    operaciones CRUD.

Para probar el panel administrativo es necesario que el usuario tenga el
rol de administrador en el backend. La interfaz no crea ni asigna ese
rol.

## Consideraciones

-   El frontend depende del backend para obtener servicios, categorías,
    sesión, carrito y pagos.
-   Para la autenticación basada en cookies, el backend debe permitir el
    origen del frontend mediante CORS y habilitar credenciales.
-   En producción, configurar `VITE_SERVICIO` con la URL pública del
    backend y volver a generar el build.
-   El código actual de `CheckoutResultado` muestra el mensaje de éxito
    únicamente cuando recibe `status=success`; los demás estados se
    muestran como pago no completado.
-   El frontend utiliza `pagina` y `cantServicios` al solicitar el
    listado. El controlador backend incluido lee `pagina` y `limite`; se
    recomienda unificar el nombre del parámetro para que la paginación
    aplique el límite solicitado.

## Autor

**Fátima Alfaro**
