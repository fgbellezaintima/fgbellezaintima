# F&G Belleza Intima — sistema de stock

Sitio estático (HTML/CSS/JS puro) que lee y escribe directamente en Supabase.
No necesita build ni backend propio: Netlify solo sirve los archivos.

## 1. Supabase

1. Crea un proyecto nuevo en https://supabase.com.
2. Ve a **SQL editor** → pega el contenido completo de `supabase/schema.sql` → **Run**.
   Esto crea las tablas (`brands`, `products`, `product_sizes`, `stock_movements`),
   las 6 marcas iniciales, las vistas de totales y el trigger que ajusta el stock
   automáticamente cuando registras un ingreso o egreso.
3. Ve a **Settings → API** y copia:
   - `Project URL`
   - `anon public key`
4. Pégalos en `js/config.js`:
   ```js
   window.SUPABASE_URL = "https://xxxxx.supabase.co";
   window.SUPABASE_ANON_KEY = "eyJ...";
   ```

Las políticas RLS del script dejan la base abierta al `anon key` (pensado para
un sistema interno de un solo local). Si más adelante quieres usuarios con
contraseña, se reemplazan esas políticas — avísame cuando llegue ese momento.

## 2. GitHub

```bash
cd fg-belleza-intima
git init
git add .
git commit -m "sistema de stock F&G Belleza Intima"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/fg-belleza-intima.git
git push -u origin main
```

`js/config.js` queda en el repo con tus llaves. Como es solo el `anon key`
(protegido por las políticas RLS) esto es aceptable para un proyecto interno,
pero si prefieres no subirlo, agrega `js/config.js` a un `.gitignore` y
súbelo a mano una vez en Netlify (Site settings → Environment o como archivo
directo, según prefieras).

## 3. Netlify

1. **Add new site → Import an existing project → GitHub** → elige el repo.
2. Build command: (vacío) — Publish directory: `.`
   (ya viene definido en `netlify.toml`, no debería pedir nada más).
3. Deploy. Cada `git push` a `main` vuelve a publicar solo.
4. No hace falta dominio propio: usa el subdominio `algo.netlify.app` que te da Netlify.

## 4. Lector de código de barras

Un lector USB o Bluetooth normal actúa como teclado: al escanear, escribe el
código y presiona Enter. No necesita integración especial — solo pon el
cursor en el campo "Código" del formulario de nuevo artículo (o en el futuro
un campo de búsqueda) y escanea directo.

## Qué hace el sistema hoy

- Tabs por marca (Peter Pan, Andrea Secret, Reyna, Brasieres importados,
  Sin marca, Medias importadas) + botón para agregar una marca nueva.
- Cada marca puede tener una imagen (pegas el link público, ej. copiado de
  Google Imágenes) que se muestra al abrir esa pestaña.
- Cada marca tiene varios artículos, cada uno con sus propias tallas
  (S–3XL) y su propio stock en docenas + unidades sueltas.
- Precio de compra siempre por docena. Precio de venta por unidad, cuarto,
  media docena o docena (cuarto/media docena/docena se calculan automático
  desde el precio unitario, y se pueden editar manualmente desde Supabase
  si un artículo tiene un precio especial).
- Ingresos y egresos quedan en un historial (`stock_movements`) y ajustan el
  stock solos vía trigger — no hay que recalcular nada a mano.
- Debajo de cada marca: total de ingresos/egresos del mes + filtro por
  artículo + últimos movimientos.

## Pendiente / a decidir contigo

- Bloquear o no un egreso si no hay stock suficiente (hoy no bloquea).
- Si quieres precios especiales por presentación editables desde la propia
  interfaz (hoy solo desde Supabase directamente).
- Login/roles si más de una persona va a usar el sistema.
