---
name: email-standar
description: Guía para crear y editar correos HTML de marketing (HubSpot u otro ESP) que cumplen los estándares de la industria de entregabilidad, para que lleguen a la bandeja de entrada y no a spam. Cubre estructura de archivos, formato técnico válido, imágenes responsivas y dónde alojarlas, links y UTM, personalización segura, asunto/preview, y cómo adaptar el copy según el contexto del lector. Úsala siempre que se cree, edite o audite un correo HTML de marketing.
---

# Correos HTML que llegan a la bandeja de entrada

Esta skill resume, en lenguaje simple, todo lo que hay que saber para que un correo
HTML de marketing (1) se vea bien en cualquier cliente de correo, (2) sea válido en
HubSpot (o el ESP que se use), y (3) tenga la mejor probabilidad de llegar a la
bandeja de entrada en vez de spam.

No hace falta leer todo de una vez: cada sección tiene lo esencial, y los archivos en
`references/` tienen el detalle completo para cuando haga falta profundizar.

## Funcionamiento interno de la skill

Esto no son estándares de la industria: son reglas de comportamiento propias de esta
skill, sobre cómo tiene que operar quien (o quién, si es una IA) la esté usando para
armar un correo. Aplican siempre, sin importar el proyecto, y van antes que todo lo
demás porque son las que más fácil se olvidan en el apuro:

1. **Nunca uses guion largo (—, em dash) en el copy de ningún correo.** Ni en
   asunto, ni en preview, ni en cuerpo, ni en CTA. Si hace falta una pausa o una
   aclaración donde normalmente irías con un guion largo, usá una coma, un punto,
   dos puntos, o reformulá la frase.
2. **Si falta una imagen por subir, decilo explícitamente y no sigas de largo.**
   Nunca dejes un correo "terminado" con una imagen en ruta local o en un
   placeholder sin marcar. Avisá el pendiente y recordá subirla primero a
   `https://app.hubspot.com/files/<hub-id>/` (ver sección 4).
3. **Toda edición a un correo va en los dos archivos**, el `.html` completo y el
   `-hubspot.html`, nunca en uno solo (ver sección 1). Son el mismo correo.
4. **Después de cualquier edición al `-hubspot.html`, corré el comando de
   validación** (sección 2) antes de darlo por terminado. No asumas que quedó bien
   solo porque el cambio se veía simple.
5. **Antes de confirmar un asunto o preview como definitivo, contá los caracteres**
   (sección 7). No lo estimes a ojo.

## Estándares de la industria

De acá para abajo, todo lo que sigue sí es estándar general de email marketing y
entregabilidad, no una regla propia de esta skill.

## 1. Estructura de archivos

Cada correo vive en **dos archivos**:

- `nombre-del-correo.html` → documento HTML completo (`<!doctype>`, `<html>`, `<head>`,
  `<body>`). Sirve para previsualizar en el navegador. **Nunca se envía tal cual.**
- `nombre-del-correo-hubspot.html` → solo el contenido de adentro del `<body>`, con
  todo el CSS ya inline. Esto es lo que se pega en el módulo de HTML personalizado de
  HubSpot (o el editor del ESP que uses).

Cada vez que se edita el correo, **el cambio va en los dos archivos**. Son el mismo
correo, solo que uno es documento completo y el otro es el fragmento que de verdad se
envía.

Encabezado recomendado en el `.html` completo, como comentario, para que cualquiera
que abra el archivo entienda el correo sin tener que leer todo el código:

```html
<!--
  NOMBRE DEL CORREO · para qué sirve, en qué punto de la secuencia va
  Asunto:        (el asunto real)
  Preview text:  (el texto de vista previa real)
  Qué es: 2-3 líneas explicando el objetivo y el tono.
  Assets y links reales: lista de URLs usadas, para que quede documentado qué es
  placeholder y qué ya está resuelto.
-->
```

## 2. Formato técnico válido (HubSpot y la mayoría de ESPs)

El módulo de HTML personalizado de HubSpot **rechaza** estas etiquetas si aparecen en
el fragmento que se pega, incluso dentro de un comentario HTML:

- `<!doctype>`, `<html>`, `<head>`, `<body>`
- `<style>` (todo el CSS tiene que ir inline, atributo `style="..."` en cada elemento)
- `<link>` externo (ej. Google Fonts no funciona en el envío real, aunque sirva para
  la previsualización local en el navegador)

**Comando de validación**, después de cada edición del `-hubspot.html`:

```bash
grep -in "<html\|<head\|<body\|<style\|<link\|doctype" archivo-hubspot.html
```

Si el comando no devuelve nada, el archivo es válido. Si devuelve una línea, hay que
arreglarla antes de pegarlo en HubSpot.

Diferencias exactas entre el `.html` completo y el `-hubspot.html` (ver
`references/hubspot-format.md` para el detalle):

| En el `.html` completo | En el `-hubspot.html` |
|---|---|
| `class="px-mobile"` para el padding responsive vía `@media` | Se quita (los fragmentos no llevan `@media`, así que ese padding no tiene efecto) |
| `<table style="...">` | Se agrega `mso-table-lspace:0pt; mso-table-rspace:0pt;` (fix para Outlook) |
| `<img style="...">` | Se agrega `outline:none; -ms-interpolation-mode:bicubic;` |
| `<a style="...">` | Se agrega `text-decoration:none;` |

## 3. La imagen responsiva (el bug más común)

**Regla de oro: toda imagen de cuerpo lleva `width="100%"` como atributo HTML**, con
el tamaño máximo real solo en el `style`:

```html
<img src="..." alt="..."
     width="100%"
     style="width:100%; max-width:520px; height:auto; display:block; border:0;">
```

Si en vez de `width="100%"` se pone un número fijo como `width="520"`, la imagen se ve
correcta en el navegador y en Gmail de escritorio, pero **diminuta** en Gmail para
iOS/Android, porque los fragmentos de HubSpot no llevan `@media` y esos clientes
respetan el atributo `width` del HTML por encima del `max-width` del CSS.

Excepciones donde SÍ va un ancho fijo (elementos que no deben crecer):
- Banner superior de la marca: `width="600"`
- Iconos pequeños de proceso: `width="30"`
- Logo aislado: `width="220"`

## 4. Dónde alojar las imágenes y qué tamaños usar

- Toda imagen va **subida al administrador de archivos del ESP** (en HubSpot:
  Marketing → Archivos y plantillas → Archivos, o directo en
  `https://app.hubspot.com/files/<hub-id>/`), nunca en una ruta local
  (`assets/imagen.jpg`) ni en un hosting externo que no controlás. Una ruta local
  deja de existir apenas el correo sale de tu computador (el destinatario ve un
  ícono roto), y un hosting externo de terceros se puede mover, expirar o
  bloquearse, rompiendo el correo días o semanas después de enviado, sin aviso.
- **Si todavía no tenés la imagen final subida, decilo explícitamente en vez de
  seguir con un placeholder silencioso.** Si estás armando el correo con ayuda de
  Claude y falta una imagen, Claude tiene que recordarte: subila primero a
  `https://app.hubspot.com/files/<hub-id>/` y compartí el link que te da HubSpot,
  para poder ponerlo en el `src` real. Nunca dejar un correo "terminado" con una
  imagen en ruta local sin marcarlo como pendiente.
- Durante la construcción es normal tener una carpeta `assets/` local para
  previsualizar en el navegador. Antes de pegar el fragmento en el ESP, cada
  `src="assets/..."` se reemplaza por la URL real ya subida.
- **Formato: PNG o JPG, nunca SVG ni WEBP** en el cuerpo del correo. Outlook de
  escritorio (todavía muy usado en B2B) no los renderiza de forma confiable, y el
  ícono simplemente no aparece. Si el asset original viene en SVG o WEBP (común
  si sale de un design system), convertilo a PNG antes de subirlo.
- **Tamaños de referencia** (medidas reales de esta campaña; ajustá según tu
  diseño, pero mantené la misma lógica: fijo para lo que no debe crecer,
  responsivo para lo que sí):

  | Tipo de imagen | Ancho de referencia | Notas |
  |---|---|---|
  | Banner superior (header, logo integrado) | 600px | Ancho fijo, ocupa todo el correo |
  | Imagen de cuerpo / ilustración | max-width 520px | Responsivo, `width="100%"` (ver sección 3) |
  | Card de recurso (2 lado a lado) | max-width 260px | Cada una, la mitad del ancho del correo |
  | Ícono de proceso/lista | 30px | Ancho fijo, no debe crecer |
  | Logo aislado | 220px | Ancho fijo |

- **`alt` descriptivo siempre**: muchos clientes de correo bloquean imágenes por
  default hasta que el destinatario decide cargarlas. El `alt` es lo único que ve
  esa persona hasta ese momento, y además es lo que usan los lectores de pantalla.
  Un `alt=""` vacío o con el nombre del archivo no sirve para ninguna de las dos
  cosas.
- Comprimí cada imagen antes de subirla (el archivo final para email, no el
  original de diseño). No cuenta contra el límite de 100KB del HTML en sí (las
  imágenes se cargan aparte), pero si son pesadas el correo tarda en verse
  completo, lo cual sí afecta la experiencia.

Ver `references/imagenes.md` para el detalle completo, incluyendo qué hacer cuando
todavía no tenés la imagen final subida.

## 5. Links, UTM y conteo

- **3-5 links de contenido real** por correo es el punto ideal. Hasta 6-7 es aceptable
  si hay una razón clara (ej. un CTA de ventas + varios íconos de producto). Más que
  eso empieza a verse spammy y diluye el CTA principal.
- Todo link de contenido lleva UTM: `?utm_source=email&amp;utm_medium=<canal>&amp;utm_campaign=<campaña>&amp;utm_content=<slug-del-correo>`.
  **Usa `&amp;`, no `&` suelto**, porque el documento es XHTML y un `&` sin escapar
  puede romper el parseo en algunos clientes de correo.
- **Si estás en HubSpot, mejor usa su generador de tracking URLs** en vez de armar el
  UTM a mano: `https://app.hubspot.com/settings/<hub-id>/tracking-urls` (el número de
  la URL es el ID de tu cuenta). HubSpot arma la URL con los parámetros correctos, la
  asocia automáticamente a una campaña existente (o te deja crear una), y esa
  asociación es lo que después te deja ver reportes de atribución reales dentro de
  HubSpot en vez de solo parámetros sueltos que nadie cruza con nada. Reservá el UTM
  armado a mano para links que no pasan por HubSpot.
- **Nunca un acortador de links** (`hubs.ly`, `bit.ly`, etc.). Los acortadores son una
  señal de spam para varios filtros, y ocultan el destino real al lector. Siempre la
  URL directa y completa.
- No repitas el mismo link ni la misma acción dos veces en un correo (ej. un botón que
  invita a suscribirse y, más abajo, otro texto que invita a lo mismo). Se siente
  repetitivo y ninguno de los dos brilla.

## 6. Personalización segura (merge tags / HubL)

Cualquier propiedad de contacto que pueda estar vacía necesita una guarda condicional,
o el correo le llega a esa persona con un hueco o una coma huérfana.

Mal (rompe si el contacto no tiene el nombre cargado):
```
Hola {{ contact.firstname }},
```
→ le llega a alguien sin nombre como: `Hola ,`

Bien:
```
Hola{% if contact.firstname %} {{ contact.firstname }}{% endif %},
```

Mismo patrón para cualquier otra propiedad opcional (empresa, cargo, lo que sea):
```
{% if company.name %} en {{ company.name }}{% endif %}
```

No asumas en el copy nada que no puedas verificar con una propiedad real. Si la base
mezcla contactos de distintos orígenes (eventos, formularios, listas compradas), no
digas "como ya vimos en el correo anterior" a menos que el flujo garantice que todos
los que reciben esa pieza sí recibieron la anterior.

## 7. Asunto y preview text

- **Combinado, 110-125 caracteres**: es lo que llena la línea completa en Gmail de
  escritorio sin cortarse ni dejar espacio en blanco.
- **Asunto: 45-58 caracteres**, con la idea principal en los primeros ~40 (en mobile
  se corta antes).
- **Preview: 55-68 caracteres.** Nunca repite el asunto con otras palabras: lo
  complementa, agrega información nueva, o remata la idea.
- El asunto tiene que funcionar solo, sin depender de que el lector recuerde algo
  puntual de la marca o de un correo anterior. Cuanta menos memoria/contexto exija,
  mejor abre.
- Evita palabras y patrones que activan filtros de spam (ver
  `references/deliverability.md` para la lista completa): mayúsculas sostenidas,
  signos de exclamación múltiples, "gratis", "urgente", "última oportunidad", exceso
  de símbolos ($$$, %%%).

## 8. Lo que de verdad decide el copy: ¿tiene contexto del producto o no?

La pregunta que más cambia cómo hay que escribir un correo no es "qué tan seguido
abre" ni ningún score de engagement: es **si el destinatario ya sabe qué es tu
producto/marca, o no tiene ni idea**. Todo lo demás (tono, gancho, cuánto explicar) se
deriva de esa única pregunta.

- **Sin contexto**: no podés asumir que sabe qué hacés, ni usar jerga interna, ni
  referenciar contenido previo tuyo. El copy tiene que ganarse la apertura solo
  (con un gancho fuerte o siendo más explicativo de lo normal) y explicar desde la
  base. Nunca digas "como ya viste" o "como sabes" sobre algo que no podés verificar
  que efectivamente vio.
- **Con contexto**: ya sabe qué es tu producto y para qué sirve. Ahí el copy puede ir
  directo, encuadrar desde la perspectiva propia de la marca, y saltarse la
  explicación desde cero, siempre que no suene a guion frío tipo "Sabes que...".

Si además estás enviando la misma pieza a varias audiencias (por ejemplo, un sistema
de scoring tipo Cold/Warm/Hot, o listas separadas por origen), el diseño, las
imágenes y los links se mantienen idénticos entre versiones; solo cambia el texto
puntual (la apertura del cuerpo, el CTA, a veces el asunto), siempre gobernado por la
misma pregunta de contexto. Ver `references/segmentacion.md` para un ejemplo
detallado de cómo se ve esto aplicado a un sistema de 3 niveles, pero esa
implementación puntual es secundaria: lo que hay que llevarse de acá es el principio
de arriba.

## 9. Entregabilidad: lo que decide si llega a spam

Ver `references/deliverability.md` para el detalle técnico completo (autenticación
del dominio, ratio texto/imagen, peso del correo). Resumen rápido:

- **SPF y DKIM** (firman el correo para que los servidores de destino confirmen que
  de verdad lo mandó quien dice mandarlo): la mayoría de ESPs (HubSpot incluido) los
  configura automático al conectar el dominio.
- **DMARC** (la política de qué hacer si un correo falla SPF/DKIM): normalmente hay
  que agregarlo a mano en el DNS del dominio, el ESP no lo hace solo.
- **Unsubscribe de un clic (RFC 8058)**: HubSpot lo aplica automático a correos de
  marketing. De todas formas, el footer siempre lleva el link real de baja del ESP
  (`{{ unsubscribe_link }}` en HubSpot), nunca un link inventado.
- **Dirección postal completa** en el footer: la exige la ley (CAN-SPAM en EE.UU.,
  equivalentes en otros países) y su ausencia es una señal de spam.
- **Ratio texto/imagen**: apunta a 60% texto / 40% imagen. Un correo casi todo imagen
  (o una sola imagen enorme sin texto) es un patrón clásico de spam.
- **Peso total del correo**: menos de 100KB. Correos pesados tardan en cargar y varios
  filtros los penalizan.

## 10. Checklist antes de enviar

- [ ] Cero guion largo (—) en todo el copy
- [ ] `grep` de tags prohibidos en el `-hubspot.html` da vacío
- [ ] Todas las imágenes de cuerpo en `width="100%"` (salvo banner/ícono/logo)
- [ ] Cero imágenes en ruta local (`assets/...`) o en hosting externo no controlado:
      todas subidas al administrador de archivos del ESP
- [ ] Cero imágenes en SVG o WEBP en el cuerpo del correo (todas PNG/JPG)
- [ ] Todas las imágenes con `alt` descriptivo
- [ ] Todos los links de contenido llevan UTM con `&amp;`
- [ ] Cero acortadores de link
- [ ] Todo merge tag opcional (nombre, empresa) tiene su `{% if %}`
- [ ] Asunto + preview dentro del rango de caracteres, y el asunto funciona sin
      contexto previo
- [ ] Footer con dirección postal y link real de unsubscribe
- [ ] Correo de prueba enviado a una cuenta propia, revisado en escritorio Y en móvil
- [ ] Dominio autenticado (SPF/DKIM/DMARC) en el ESP

## Referencias

- `references/hubspot-format.md`: detalle técnico completo de las diferencias entre
  el documento completo y el fragmento, y por qué cada una existe.
- `references/deliverability.md`: guía completa de autenticación, spam triggers, y
  todo lo que decide bandeja de entrada vs. spam.
- `references/imagenes.md`: dónde alojar cada imagen, formatos compatibles, tamaños
  de referencia, y qué hacer mientras no tenés el asset final.
- `references/segmentacion.md`: el principio de contexto vs. sin contexto, y un
  ejemplo de cómo se ve aplicado a un sistema de 3 niveles.
- `references/asunto-preview.md`: la lógica completa detrás del estándar de
  caracteres, con ejemplos reales.
