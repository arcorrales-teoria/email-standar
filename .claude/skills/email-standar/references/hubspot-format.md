# Formato técnico: documento completo vs. fragmento de HubSpot

## Por qué existen dos archivos

Un correo HTML completo necesita `<!doctype>`, `<html>`, `<head>` (con metaetiquetas,
fuentes, un bloque `<style>` para las reglas `@media` que hacen el diseño responsive)
y `<body>`. Eso es indispensable para que el archivo se pueda abrir directo en un
navegador y previsualizarlo.

Pero HubSpot (y la mayoría de plataformas de email con editor de "HTML personalizado")
no reciben un documento: reciben un **fragmento** que se inserta dentro de una
plantilla que la plataforma ya arma por su cuenta (con su propio `<head>`, sus propias
reglas de tracking, su propio pie de página de compliance). Si el fragmento trae su
propio `<html>`/`<head>`/`<body>`/`<style>`, choca con la plantilla del ESP: por eso el
validador los rechaza, y los rechaza incluso si están dentro de un comentario HTML,
porque escanea el texto crudo del archivo, no el HTML ya interpretado.

## Validación

Después de cualquier edición al `-hubspot.html`:

```bash
grep -in "<html\|<head\|<body\|<style\|<link\|doctype" archivo-hubspot.html
```

Vacío = válido. Cualquier línea que aparezca hay que resolverla antes de pegar el
archivo en HubSpot.

## Las 4 transformaciones exactas

### 1. `class="px-mobile"` desaparece

En el documento completo, el `<head>` define:

```css
@media screen and (max-width:600px) {
  .px-mobile { padding-left:24px !important; padding-right:24px !important; }
}
```

Esa clase le da más aire a los márgenes laterales en pantallas chicas. El fragmento de
HubSpot **no lleva ningún `@media`** (viene de la plantilla del ESP, no del correo),
así que la clase `class="px-mobile"` no tiene ningún efecto ahí: se puede dejar sin
que rompa nada, pero lo correcto es quitarla, porque es peso muerto en el HTML.

### 2. Las tablas ganan `mso-table-lspace` y `mso-table-rspace`

```html
<!-- documento completo -->
<table role="presentation" style="...">

<!-- fragmento hubspot -->
<table role="presentation" style="...; mso-table-lspace:0pt; mso-table-rspace:0pt;">
```

Es un fix específico para Outlook de escritorio (que usa el motor de renderizado de
Word para HTML, no un motor de navegador real), que agrega espaciado fantasma entre
celdas de tabla si no se le dice explícitamente que no lo haga.

### 3. Las imágenes ganan `outline:none` y `-ms-interpolation-mode:bicubic`

```html
<img ... style="...; outline:none; -ms-interpolation-mode:bicubic;">
```

`outline:none` evita el borde azul de foco que algunos clientes le agregan a
imágenes dentro de links. `-ms-interpolation-mode:bicubic` es específico de Internet
Explorer/Outlook: sin esto, las imágenes redimensionadas se ven pixeladas ahí.

### 4. Los links ganan `text-decoration:none`

```html
<a ... style="...; text-decoration:none;">
```

Algunos clientes de correo (sobre todo en Android y algunos webmails) ignoran el
`text-decoration:none` que viene de una hoja de estilos y solo respetan el que está
declarado directo en el atributo `style` del elemento. Ponerlo inline en cada link
asegura que ninguno aparezca subrayado por accidente.

## El bloque de comentario del header

Solo va en el documento completo, nunca en el fragmento (ahí sí sería un comentario
legítimo, no rompe nada técnicamente, pero es información interna que no tiene
sentido llevar al correo real). Documentar ahí mismo:

- Asunto y preview text reales (para no tener que ir a buscarlos en HubSpot)
- Qué hace este correo y en qué punto de una secuencia va
- Lista de assets y links reales usados, marcando explícitamente cuáles son
  placeholders pendientes (`{{URL_LO_QUE_SEA}}`) y cuáles ya están resueltos

Esto evita que alguien (incluida una futura sesión de Claude) tenga que releer todo
el HTML para entender el contexto del correo.
