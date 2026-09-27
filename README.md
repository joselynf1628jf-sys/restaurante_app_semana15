# Restaurante App — Semana 15

| Campo | Detalle |
|---|---|
| **Estudiante** | Joselyn Yomaira Fuentes Rosero |
| **Asignatura** | Programación Orientada a Objetos |
| **Universidad** | Universidad Estatal Amazónica |
| **Tema** | Conceptos fundamentales de manejo de eventos |

---

## Sobre esta entrega

Esta versión evoluciona `restaurante_app` (Restaurante Casero) a partir de la gestión completa del menú construida en la Semana 14. El objetivo central es comprender los **fundamentos básicos del manejo de eventos**: cómo una acción del usuario sobre un componente (un botón) dispara, mediante `command=`, un **callback** que coordina una operación de negocio sin concentrar la lógica dentro de la interfaz.

Como contexto práctico se incorpora la sección **Ventas**, que relaciona un **usuario** existente con un **producto** existente del menú y conserva el registro en `ventas.json`. Se conservan íntegramente el inicio de sesión, la navegación y la gestión completa del menú ya construidas.

---

## Organización de archivos

```
restaurante_app/
├── assets/
│   ├── icons/               ← iconos de navegacion y de acciones
│   └── logo/                ← logo.png e icono.png del sistema
├── datos/
│   ├── productos.json      ← productos con descripcion y stock
│   ├── usuarios.json       ← usuarios con telefono y credenciales de acceso
│   └── ventas.json         ← ventas registradas (nuevo)
├── modelos/
│   ├── __init__.py
│   ├── producto.py
│   ├── usuario.py
│   └── venta.py             ← relaciona usuario + producto + fecha (nuevo)
├── servicios/
│   ├── __init__.py
│   ├── archivo_servicio.py
│   └── restaurante_servicio.py  ← ahora con CRUD de productos y registro de ventas
├── ui/
│   ├── __init__.py
│   ├── login_view.py       ← pantalla de acceso, con logo
│   └── main_view.py        ← panel principal, ahora con seccion Ventas
├── main.py
└── README.md
```

---

## Responsabilidad de cada capa

| Capa | Responsabilidad |
|---|---|
| `modelos/` | `Producto` (con descripción) y `Usuario` (con teléfono), validados con `property`. `Venta` (nuevo) relaciona `usuario_identificacion`, `producto_codigo` y `fecha`. |
| `servicios/archivo_servicio.py` | Lee y escribe los archivos JSON de `datos/`. |
| `servicios/restaurante_servicio.py` | Convierte los datos en objetos, valida el acceso, expone el CRUD completo de productos y ahora `registrar_venta`, `listar_ventas`, `cantidad_ventas` y `guardar_ventas`. Toda la validación y persistencia de la venta vive aquí, no en la interfaz. |
| `ui/` | `LoginView` y `MainView`, construidas con Tkinter; los callbacks solo recolectan la selección del usuario y piden la operación a `RestauranteServicio`. |
| `main.py` | Crea la única ventana principal, configura el ícono del sistema desde `assets/` y controla el cambio entre vistas. |

---

## Sección Ventas: flujo de eventos aplicado

| Paso | Acción |
|---|---|
| 1 | El usuario elige un **Usuario** y un **Producto** en los Combobox |
| 2 | Clic en **Registrar venta** — el botón usa `command=self.registrar_venta` |
| 3 | El callback obtiene la selección de los Combobox y la traduce a la identificación/código real |
| 4 | El callback llama a `restaurante_servicio.registrar_venta(...)` |
| 5 | `RestauranteServicio` valida que el usuario y el producto existan |
| 6 | Se crea un objeto `Venta` (el modelo valida sus propios datos) y se agrega en memoria |
| 7 | `guardar_ventas()` persiste el cambio en `ventas.json` mediante `ArchivoServicio` |
| 8 | `MainView.refrescar_ventas()` actualiza la tabla (`Treeview`) y la barra de estado |

El callback de la interfaz **no** decide si la venta es válida ni toca el archivo JSON: solo recolecta la selección y coordina la llamada al servicio.

### Componentes usados en Ventas

| Componente | Uso |
|---|---|
| `ttk.Combobox` (solo lectura) | Selección de usuario y de producto existentes |
| `ttk.Button` | Botón **Registrar venta** con `command=` |
| `ttk.Treeview` + `ttk.Scrollbar` | Tabla de ventas registradas (Venta, Usuario, Producto, Fecha) |
| `tk.LabelFrame` | Separa el formulario del listado, igual que en Productos |

---

## Recursos gráficos (`assets/`)

| Recurso | Uso |
|---|---|
| `assets/logo/logo.png` | Logotipo mostrado en la pantalla de inicio de sesión |
| `assets/logo/icono.png` | Ícono de la ventana principal y del encabezado de `MainView` |
| `assets/icons/*.png` | Íconos de los botones de navegación (Inicio, Productos, Usuarios, Reservas, Ventas, Cerrar sesión) y de las acciones del formulario (Registrar, Consultar, Actualizar, Eliminar, Limpiar) |

---

## Operaciones implementadas sobre productos

| Operación | Acción en la interfaz | Método en `RestauranteServicio` |
|---|---|---|
| Registrar | Botón **Registrar** | `registrar_producto(codigo, nombre, precio, categoria, descripcion, stock)` |
| Consultar | Botón **Cargar / Consultar** | `buscar_producto_por_codigo(codigo)` |
| Actualizar | Botón **Actualizar** | `actualizar_producto(codigo, nombre, precio, categoria, descripcion, stock)` |
| Eliminar | Botón **Eliminar** | `eliminar_producto(codigo)` |

---

## Persistencia

Los cambios sobre productos y ventas se guardan de inmediato en `datos/productos.json` y `datos/ventas.json` mediante `RestauranteServicio`, que delega en `ArchivoServicio`. Al reabrir la aplicación, los datos modificados se conservan.

---

## Flujo de la aplicación

| Paso | Acción |
|---|---|
| 1 | `main.py` prepara Tkinter, el ícono del sistema y los servicios |
| 2 | Se muestra `LoginView`, con el logotipo del sistema |
| 3 | El usuario ingresa usuario y contraseña |
| 4 | `RestauranteServicio` valida el acceso |
| 5 | Se muestra `MainView` |
| 6 | El usuario consulta Usuarios, gestiona Productos o registra una Venta |
| 7 | Cada operación persiste en el JSON correspondiente y actualiza la tabla y la barra de estado |
| 8 | Al cerrar sesión, se regresa a `LoginView` dentro de la misma ventana |

---

## Credenciales de acceso (demostración)

| Usuario | Contraseña |
|---|---|
| `jfuentes` | `caseroapp` |
| `admin` | `admin321` |

---

## Cómo ejecutar

```bash
cd restaurante_app
python main.py
```

---

## Pruebas realizadas

| Prueba | Resultado esperado |
|---|---|
| Ejecutar `main.py` | La aplicación inicia sin errores, con el ícono del sistema visible |
| Acceso con credenciales válidas | Se despliega `MainView`, con el logotipo en el encabezado |
| Opción **Usuarios** | Muestra la tabla de usuarios cargada desde `usuarios.json` |
| Opción **Productos** | El CRUD completo de la Semana 14 sigue funcionando |
| Opción **Ventas** | Muestra el formulario con los Combobox y la tabla de ventas |
| Registrar venta con selección válida | La venta aparece de inmediato en la tabla y se guarda en `ventas.json` |
| Registrar venta sin seleccionar usuario/producto | `RestauranteServicio` rechaza la operación con un mensaje claro |
| Reabrir la aplicación | Las ventas registradas se recuperan desde `ventas.json` |
| Cerrar sesión | Regresa a `LoginView` sin abrir una ventana nueva |

---

## Nota sobre la autenticación

El acceso implementado es una simulación con fines académicos. Las contraseñas se guardan en JSON sin cifrado; esto no sería adecuado en un sistema real.
