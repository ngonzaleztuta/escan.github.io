# 📦 FreshTrack — Escáner de Inventario Inteligente

FreshTrack es una aplicación web móvil que permite escanear productos con código de barras, registrarlos en Airtable y recibir alertas antes de que venzan. Cada usuario ve únicamente sus propios productos gracias a la autenticación con Firebase.

---

## ✨ Funcionalidades

- **Escáner de códigos de barras** usando la cámara del celular (EAN-13, EAN-8, Code128, UPC, Code39)
- **Login con correo/contraseña o Google** mediante Firebase Authentication
- **Inventario personal** — cada usuario solo ve sus propios productos
- **Cuenta regresiva** en días para cada producto
- **Semáforo de estado** — verde (OK), amarillo (por vencer), rojo (urgente o vencido)
- **Notificaciones push** del navegador cuando un producto llega a su fecha de aviso
- **15 categorías de productos** organizadas por grupos
- **Filtros rápidos** por estado (todos / urgente / por vencer / OK)
- Funciona desde el navegador sin instalar nada

---

## 🗂️ Estructura del proyecto

```
freshtrack.html     ← Toda la app en un solo archivo HTML
README.md           ← Esta documentación
```

La app está construida en HTML, CSS y JavaScript puro, sin frameworks ni bundlers. Todo vive en un único archivo.

---

## ⚙️ Requisitos

- Cuenta gratuita en [Firebase](https://firebase.google.com)
- Cuenta gratuita en [Airtable](https://airtable.com)
- Navegador moderno con soporte de cámara (Chrome, Safari, Firefox)
- Conexión a internet

---

## 🔧 Configuración paso a paso

### 1. Airtable

#### Crear la base de datos

1. Entra a [airtable.com](https://airtable.com) y crea una nueva base
2. Renombra la tabla principal como `BASE DE DATOS` (exactamente así, con mayúsculas y espacios)
3. Crea los siguientes campos:

| Nombre del campo | Tipo |
|---|---|
| `CODIGO` | Single line text |
| `Nombre` | Single line text |
| `Tipo` | Single line text |
| `Fecha vencimiento` | Date |
| `Fecha Aviso` | Date |
| `UsuarioUID` | Single line text |
| `UsuarioEmail` | Single line text (o Email) |

> ⚠️ Los nombres deben ser exactamente iguales, incluyendo mayúsculas, tildes y espacios.

#### Obtener el Token y el Base ID

**Token de acceso:**
1. Ve a [airtable.com/create/tokens](https://airtable.com/create/tokens)
2. Clic en **Create new token**
3. Dale un nombre (ej: `freshtrack`)
4. En Scopes agrega: `data.records:read` y `data.records:write`
5. En Access agrega tu base
6. Copia el token generado

**Base ID:**
1. Abre tu base en Airtable
2. Mira la URL: `https://airtable.com/appXXXXXXXXXXXXXX/...`
3. El Base ID es la parte que empieza con `app`

#### Pegar las credenciales en el archivo

Busca esta sección en `freshtrack.html` y reemplaza los valores:

```javascript
const AIRTABLE_TOKEN = "TU_TOKEN_AQUI";
const BASE_ID        = "appXXXXXXXXXXXXXX";
const TABLA          = "BASE DE DATOS";
```

---

### 2. Firebase

#### Crear el proyecto

1. Ve a [console.firebase.google.com](https://console.firebase.google.com)
2. Clic en **Agregar proyecto**
3. Escribe un nombre y sigue los pasos (puedes desactivar Google Analytics)

#### Registrar la app web

1. En el panel del proyecto, clic en el ícono **`</>`** (Web)
2. Escribe un apodo (ej: `freshtrack`)
3. Clic en **Registrar app**
4. Firebase te mostrará un bloque `firebaseConfig` — cópialo

#### Pegar la configuración en el archivo

Busca esta sección en `freshtrack.html` y reemplaza con tus valores reales:

```javascript
const firebaseConfig = {
  apiKey:            "TU_API_KEY",
  authDomain:        "TU_PROYECTO.firebaseapp.com",
  projectId:         "TU_PROYECTO",
  storageBucket:     "TU_PROYECTO.appspot.com",
  messagingSenderId: "TU_SENDER_ID",
  appId:             "TU_APP_ID"
};
```

#### Activar métodos de autenticación

**Correo y contraseña:**
1. En Firebase Console → **Authentication** → pestaña **Sign-in method**
2. Clic en **Correo electrónico/Contraseña**
3. Activa el primer toggle
4. Clic en **Guardar**

**Google:**
1. En la misma pantalla, clic en **Google**
2. Activa el toggle
3. Ingresa tu correo en "Correo electrónico de soporte"
4. Clic en **Guardar**

---

### 3. Publicar en GitHub Pages (opcional)

Si quieres que la app tenga una URL pública y funcione desde el celular sin abrir archivos localmente:

1. Crea una cuenta en [github.com](https://github.com) (gratuita)
2. Crea un **repositorio público** nuevo (ej: `freshtrack`)
3. Sube el archivo `freshtrack.html` renombrado como `index.html`
4. Ve a **Settings** del repositorio → **Pages**
5. En Source selecciona `Deploy from a branch` → `main` → `/ (root)`
6. Guarda. En 1-2 minutos aparecerá tu URL:

```
https://TU_USUARIO.github.io/freshtrack/
```

#### Autorizar el dominio en Firebase

Para que el login funcione desde GitHub Pages:

1. Firebase Console → **Authentication** → **Settings**
2. Sección **Authorized domains**
3. Clic en **Add domain**
4. Pega tu dominio: `TU_USUARIO.github.io`
5. Guardar

---

## 🗃️ Categorías de productos disponibles

| Grupo | Categorías |
|---|---|
| 🥩 Alimentos | Carne y pescado, Lácteos, Frutas y verduras, Panadería y repostería, Granos y cereales, Conservas y enlatados, Condimentos y salsas, Snacks |
| 🥤 Bebidas | Bebidas no alcohólicas, Bebidas alcohólicas, Jugos y néctares, Agua |
| 💊 Salud | Medicamentos, Suplementos, Vitaminas |
| 🧴 Hogar | Limpieza, Cuidado personal, Cosméticos |
| 🐾 Mascotas | Alimento para mascotas |

---

## 🔔 Sistema de notificaciones

Las notificaciones funcionan a través del navegador (no requieren app instalada).

| Situación | Notificación |
|---|---|
| Fecha de aviso llegó a 0 días | ⚠️ "Por vencer: [producto]" |
| Producto vence hoy | 🚨 "Vence HOY: [producto]" |
| Producto ya venció | ❌ "Vencido: [producto]" |

**Notas importantes:**
- El usuario debe aceptar el permiso de notificaciones cuando la app lo solicite
- Solo se envía una notificación por producto por día para evitar spam
- En iOS Safari las notificaciones push tienen soporte limitado; funcionan mejor en Android Chrome
- Las notificaciones se comprueban cada vez que el usuario abre la app o cambia de pestaña

---

## 🚦 Estados del semáforo

Cada producto muestra un color según su proximidad al vencimiento:

| Color | Estado | Condición |
|---|---|---|
| 🟢 Verde | OK | La fecha de aviso aún no llegó |
| 🟡 Amarillo | Por vencer | La fecha de aviso llegó pero no ha vencido |
| 🔴 Rojo | Urgente | Ya venció o vence hoy |

---

## 🔒 Privacidad y datos

- Cada producto se guarda con el `UID` del usuario de Firebase
- El inventario se filtra automáticamente por `UID`, por lo que cada usuario solo ve sus propios registros
- Las credenciales de Firebase y Airtable quedan en el archivo HTML — no compartas el archivo con tus tokens si publicas el código en un repositorio público

---

## 🐛 Errores comunes

| Error | Causa | Solución |
|---|---|---|
| `auth/invalid-api-key` | El `apiKey` de Firebase está mal pegado | Verifica que copiaste el valor completo sin espacios |
| `auth/configuration-not-found` | Método de login no activado | Activa Correo/Contraseña en Firebase → Authentication |
| `auth/popup-blocked` | El navegador bloqueó el popup de Google | Permite popups para el sitio en la configuración del navegador |
| `auth/unauthorized-domain` | Dominio no autorizado en Firebase | Agrega tu dominio en Authentication → Settings → Authorized domains |
| Error 422 en Airtable | Nombre de campo incorrecto | Verifica que los campos existen con el nombre exacto incluyendo mayúsculas |
| Error 401 en Airtable | Token inválido o vencido | Genera un nuevo token en airtable.com/create/tokens |
| Cámara no funciona | Permiso denegado o HTTP | Asegúrate de usar HTTPS (GitHub Pages ya lo incluye) |
| Productos no aparecen en inventario | Falta el campo `UsuarioUID` | Crea el campo en Airtable y guarda un producto nuevo |

---

## 🛠️ Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| HTML / CSS / JavaScript | Base de la aplicación |
| [QuaggaJS](https://github.com/serratus/quaggaJS) | Lectura de códigos de barras por cámara |
| [Firebase Authentication](https://firebase.google.com/products/auth) | Login con correo y Google |
| [Airtable API](https://airtable.com/developers/web/api/introduction) | Base de datos de productos |
| Notifications API | Notificaciones push del navegador |
| GitHub Pages | Hosting gratuito |

---

## 📝 Licencia

Uso libre para proyectos personales. Recuerda no exponer tus tokens de Airtable o Firebase en repositorios públicos.
