# Imágenes: dónde alojarlas, qué formato, qué tamaños

## Por qué importa dónde vive la imagen

Un correo HTML no lleva las imágenes adentro (salvo casos muy raros de base64, que
además infla muchísimo el peso del archivo y está desaconsejado): cada `<img>` es
solo una referencia a una URL. El navegador o cliente de correo del destinatario
descarga esa imagen por separado cuando abre el correo. Eso significa que la imagen
tiene que seguir existiendo, en esa misma URL, indefinidamente, para que el correo se
vea bien cada vez que alguien lo abre (incluidos reenvíos meses después).

Tres formas de alojar una imagen, y qué pasa con cada una:

- **Ruta local** (`src="assets/banner.jpg"`): funciona perfecto mientras estás
  previsualizando el archivo en tu propio navegador, porque el navegador puede leer
  del disco. En el momento en que ese HTML sale de tu computador (se pega en el ESP,
  se envía), esa ruta ya no significa nada para el destinatario: no hay ningún
  servidor sirviendo `assets/banner.jpg`. El resultado es un ícono de imagen rota.
- **Hosting externo que no controlás** (un link de Google Drive, un CDN de terceros,
  la URL de una imagen que alguien te pasó): puede andar el día que armás el correo y
  dejar de andar cualquier día después, sin ningún aviso, si esa otra persona borra el
  archivo, cambia permisos, o el servicio lo mueve. El correo ya enviado no se puede
  "arreglar" retroactivamente.
- **Administrador de archivos del propio ESP** (en HubSpot: Marketing → Archivos y
  plantillas → Archivos, o directo en `https://app.hubspot.com/files/<hub-id>/`): es
  la única opción con una URL estable a largo plazo, porque el archivo vive en la
  misma infraestructura que sirve el resto de tu cuenta. Es la única forma correcta
  de alojar una imagen que va a salir en un correo real.

## Flujo de trabajo recomendado

1. Mientras se construye/diseña el correo, es normal tener una carpeta `assets/`
   local con las imágenes de referencia, para poder previsualizar en el navegador sin
   depender de tener conexión ni de haber subido nada todavía.
2. **Apenas se sabe que una imagen todavía no está subida, subila primero**: entrá a
   `https://app.hubspot.com/files/<hub-id>/` (o Marketing → Archivos y plantillas →
   Archivos), subí el archivo, y copiá el link que HubSpot te da. Ese es el `src`
   real que va en el correo. No sigas construyendo el resto del correo dando por
   hecho que "ya la subo después": es el paso que más se olvida.
3. Cada `src="assets/..."` se reemplaza por esa URL real.
4. Si en algún punto intermedio no tenés la imagen final todavía (por ejemplo, el
   diseño está pendiente y no hay nada que subir aún), dejá un comentario HTML
   explícito justo antes del `<img>` marcando el pendiente, para que no se te pase:
   ```html
   <!-- OJO: placeholder, falta subir la imagen final a
        https://app.hubspot.com/files/<hub-id>/ y reemplazar este src antes de enviar -->
   <img src="assets/placeholder-banner.jpg" ...>
   ```
   Ese comentario es información interna: va en el `.html` completo, pero conviene
   quitarlo (o resolverlo) antes de armar el fragmento para el ESP.

**Si estás usando Claude para armar o editar el correo**: pedile explícitamente que
te avise cada vez que detecte una imagen sin subir (ruta local, o un placeholder), en
vez de dejarlo pasar en silencio. El correo no debería llegar a "listo para enviar"
con ninguna imagen todavía en ruta local.

## Formato: por qué nunca SVG ni WEBP en el cuerpo

Los navegadores modernos y la mayoría de clientes de correo webmail (Gmail, Apple
Mail) sí renderizan SVG y WEBP sin problema. El punto de fricción real es **Outlook
de escritorio**, que sigue siendo uno de los clientes más usados en entornos
corporativos/B2B, y que usa el motor de renderizado de Word (no un motor de
navegador) para interpretar HTML. Ese motor no soporta SVG ni WEBP de forma
confiable: en el mejor caso la imagen no aparece, en el peor caso rompe el layout de
la tabla que la contiene.

Si el asset original viene en SVG (muy común si sale directo de un design system o de
Figma) o en WEBP (común en sitios web modernos, más liviano que JPG/PNG), convertilo
a PNG (si necesita transparencia) o JPG (si es una foto sin transparencia) antes de
subirlo al ESP. La conversión es rápida con cualquier herramienta de diseño o incluso
en línea, y evita el problema por completo.

## Tamaños de referencia

Estos son los anchos reales usados en la campaña de la que sale esta skill. Sirven
como punto de partida razonable, no como regla fija: ajustalos al ancho total de tu
plantilla de correo (acá, 600px) y a tu propio sistema de diseño.

| Tipo de imagen | Ancho de referencia | `width` HTML | Notas |
|---|---|---|---|
| Banner superior (header, logo integrado) | 600px | `width="600"` fijo | Ocupa todo el ancho del correo, no necesita ser responsivo porque ya es el 100% del contenedor |
| Imagen de cuerpo / ilustración | hasta 520px | `width="100%"` | Ver la regla de la imagen responsiva en el `SKILL.md` principal: el atributo HTML siempre `100%`, el tope real va en `max-width` dentro del `style` |
| Card de recurso (2 lado a lado) | hasta 260px cada una | `width="100%"` | Dos columnas de 50% cada una dentro de una tabla de 2 celdas |
| Ícono de proceso/lista (junto a un texto corto) | 30px | `width="30"` fijo | No debe crecer ni encogerse, siempre acompaña texto al lado |
| Logo aislado (ej. en el footer o una firma) | 220px | `width="220"` fijo | Elemento chico, ancho fijo simple |

La regla general para decidir si un ancho va fijo o responsivo: **si el elemento
ocupa todo el ancho disponible de su columna, va responsivo (`width="100%"` +
`max-width` en el style). Si el elemento tiene un tamaño intencional que no debería
cambiar (un ícono, un logo chico), va fijo.**

## `alt` descriptivo

Varios clientes de correo (Outlook y algunos webmails corporativos, por configuración
de seguridad) bloquean la carga de imágenes por default hasta que el destinatario
elige "mostrar imágenes". Hasta ese momento, lo único visible en el lugar de cada
imagen es su texto `alt`. Un `alt=""` vacío, o un `alt` con el nombre del archivo
(`alt="banner_v3_final.png"`), deja ese espacio literalmente en blanco o con texto sin
sentido para esa persona.

Escribí el `alt` como si describieras la imagen a alguien que no la puede ver: qué
muestra, y si tiene texto integrado, qué dice ese texto. Ejemplo real:

```html
<img src="..."
     alt="¿Qué es un perfil multidimensional? Es un perfil que no depende de un solo dato: documento de identidad, biometría, verificación de número, comportamiento y más se convierten en un contexto vivo de quién es la persona."
     ...>
```

Esto además ayuda a la accesibilidad (lectores de pantalla) y, en menor medida, a
cómo algunos filtros de spam evalúan el contenido de un correo con muchas imágenes.

## Peso de archivo

Comprimí cada imagen antes de subirla: el archivo que sale de una herramienta de
diseño (Figma, Photoshop) casi siempre pesa mucho más de lo necesario para verse bien
en un correo de 600px de ancho. El peso de las imágenes hosteadas no cuenta contra el
límite de 100KB del HTML del correo en sí (se cargan aparte, por URL), pero si son
pesadas el correo tarda visiblemente en terminar de cargar, lo cual es una mala
experiencia igual, sobre todo en conexiones móviles.
