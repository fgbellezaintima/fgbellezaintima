# F&G Belleza Intima — sistema de stock

Sitio estático de un solo archivo (`index.html`, con el CSS y el JS ya
incluidos adentro) que lee y escribe directamente en Supabase. No necesita
build ni backend propio: Netlify solo sirve el archivo.

**Lo único que subes a GitHub es `index.html` y este `README.md`.**
`schema.sql` no va al repo — se pega una sola vez en el SQL Editor de Supabase.

## 1. Supabase

1. Crea un proyecto nuevo en https://supabase.com.
2. Ve a **SQL editor** → pega el contenido completo de `supabase/schema.sql` → **Run**.
   Esto crea las tablas (`brands`, `products`, `product_sizes`, `stock_movements`),
   las 6 marcas iniciales, las vistas de totales y el trigger que ajusta el stock
   automáticamente cuando registras un ingreso o egreso.
3. Ve a **Settings → API** y copia el `Project URL` (algo como
   `https://xxxxx.supabase.co`).
4. Ábrelo en `index.html`, busca estas dos líneas cerca del final del
   archivo y reemplaza el Project URL (el `anon public key` ya está puesto):
   ```js
   window.SUPABASE_URL = "https://TU-PROYECTO.supabase.co"; // <-- reemplazar
   window.SUPABASE_ANON_KEY = "sb_publishable_bw3v1omMqTfN74jYvtZliQ_7BJsGrGA";
   ```

Las políticas RLS del script dejan la base abierta al `anon key` (pensado para
un sistema interno de un solo local). Si más adelante quieres usuarios con
contraseña, se reemplazan esas políticas — avísame cuando llegue ese momento.

## 2. GitHub

Solo necesitas subir `index.html` y `README.md` — literal, arrastra esos dos
archivos al repo (por la web de GitHub o con git, como prefieras).

El `anon/publishable key` queda escrito dentro del `index.html`. Es el key
pensado para estar en el navegador (no es secreto, y las políticas RLS de
`schema.sql` son las que realmente protegen la base), así que no hay
problema en que esté a la vista en el repo.

## 3. Netlify

1. **Add new site → Import an existing project → GitHub** → elige el repo.
2. Build command: (vacío) — Publish directory: `.` (la raíz del repo, donde está `index.html`).
3. Deploy. Cada vez que actualices `index.html` en GitHub, Netlify vuelve a publicar solo.
4. No hace falta dominio propio: usa el subdominio tipo `fgbellezaintima.netlify.app`
   que puedes elegir en Site settings → Change site name.

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
