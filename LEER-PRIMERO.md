# STAGECORE Rendiciones — PWA lista para instalar

App instalable en el celular. Funciona **sin señal**, guarda las boletas en el teléfono y genera el CSV para el ERP.

## 1. Publicarla (una vez, 10 minutos, gratis)

1. Entra a **https://app.netlify.com/drop** (cuenta gratis o Google).
2. Arrastra **toda la carpeta `pwa`** a la zona de drop (no un archivo suelto: la carpeta completa).
3. Te da una URL tipo `https://xxxx.netlify.app`. En *Site configuration → Change site name* ponle
   `stagecore-rendiciones` → queda `https://stagecore-rendiciones.netlify.app`.
4. Manda esa URL al equipo por WhatsApp.

Debe ser HTTPS (Netlify y Vercel lo dan gratis). Abrir el archivo directo desde el teléfono **no** sirve:
sin HTTPS no se instala ni funciona offline.

## 2. Instalarla en cada celular

- **Android / Chrome**: abrir la URL → menú ⋮ → *Instalar aplicación* (o *Agregar a pantalla principal*).
- **iPhone / Safari**: abrir la URL → botón Compartir → *Agregar a inicio*.

Queda con ícono propio, pantalla completa y abre sin internet.

## 3. Cómo se usa

**Trabajador (rol campo)**
1. Elige su cuenta y entra con PIN (inicial **1234**, lo cambia en Perfil).
2. Confirma el evento activo (toca el nombre del evento para cambiarlo).
3. Toca ◉ → *Tomar foto de la boleta* → escribe el total con el teclado → tipo de documento
   (Boleta / Factura / Sin doc.) → RUT y folio → categoría → **Guardar boleta**.
4. Cuando termina el evento: **Enviar a aprobación**. Se genera un archivo `.json` que comparte
   por WhatsApp o correo al jefe (en Android/iPhone abre el menú de compartir directo).

**Aprobador (rol jefe)**
1. Entra con su cuenta → **Importar rendición recibida** → elige el archivo que le llegó.
2. Abre la rendición, revisa las fotos, **firma con el dedo** y *Autorizar pago* (o *Devolver*).
3. **Exportar aprobadas al ERP (CSV)**: genera un CSV (separador `;`, UTF-8) con
   fecha, evento, usuario, tipo de documento, RUT, folio, categoría, total, neto, IVA, aprobado por.

**Externos (freelance, proveedores, contador)**
En el login: *No estoy en la lista* → nombre + cualquier correo + código **SC-2026** → crea su PIN.
El código se cambia en `index.html` (constante `CODIGO_INVITACION`).

## 4. Qué hace y qué no

Hace:
- Funciona 100% offline (fotos, montos, firma, CSV).
- Datos guardados en el teléfono (IndexedDB): no se pierden al cerrar ni al recargar.
- 8 cuentas con PIN + invitados externos sin correo corporativo.
- Fotos comprimidas (máx. 1280 px, JPEG 72%) para no llenar el teléfono.
- Salida al ERP por CSV, y traspaso de rendiciones por archivo.

No hace (requiere backend):
- Los datos **no se sincronizan solos** entre teléfonos: el traspaso al aprobador es por archivo.
- Sin panel web central ni respaldo en la nube: si se pierde el teléfono, se pierden las boletas
  que no se hayan enviado.
- Sin escritura automática en el ERP (por ahora es importar el CSV).

Cuando quieras esas tres cosas, el paso siguiente es conectar Supabase (plan gratis alcanza de sobra
para 15 usuarios y 50 boletas al mes) sin rehacer la interfaz: se reemplaza la capa de guardado
(`kvGet`/`kvSet` y `save()`) por llamadas a Supabase.

## 5. Cambios rápidos que puedes hacer solo

Todo está en `pwa/index.html`, arriba del código:
- `USUARIOS` — nombres, correos, cargos y roles del equipo.
- `EVENTOS` — eventos precargados (igual se pueden agendar desde la app).
- `CATS` — categorías de gasto.
- `CODIGO_INVITACION` — el código para externos.

Después de editar, vuelve a arrastrar la carpeta a Netlify (o usa "Deploys → Drag and drop").
Sube el número de versión en `sw.js` (`CACHE = 'stagecore-rendiciones-v2'`) para que los teléfonos
tomen la versión nueva.

## Archivos

- `index.html` — la app completa (sin dependencias externas, sin build).
- `manifest.webmanifest` — nombre, ícono y modo standalone.
- `sw.js` — service worker: cachea el shell para que abra sin señal.
- `icon-192.png`, `icon-512.png`, `icon-maskable.png` — íconos generados del logo.
