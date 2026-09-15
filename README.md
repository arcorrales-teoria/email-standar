# email-standar

Skill de [Claude Code](https://claude.com/claude-code) para crear y editar correos
HTML de marketing (HubSpot u otro ESP) que cumplen los estándares de la industria de
entregabilidad: formato técnico válido, imágenes responsivas, links y UTM, asunto y
preview text con el largo correcto, y todo lo necesario para que un correo llegue a
la bandeja de entrada en vez de a spam.

Nace de la experiencia real construyendo una secuencia de nutrición completa para
[Truora](https://www.truora.com) en HubSpot: cada regla acá adentro salió de un
problema real que se encontró y se resolvió en el camino (imágenes que se veían
diminutas en Gmail móvil, correos rechazados por HubSpot por tags no permitidos,
asuntos que no llenaban la línea de la bandeja de entrada, copy que asumía contexto
que el lector no tenía, etc.), no de una lista genérica copiada de internet.

## Instalación

Cloná este repo (o copiá la carpeta `.claude/skills/email-standar/`)
dentro de la carpeta `.claude/skills/` de tu proyecto:

```bash
git clone https://github.com/<tu-usuario>/email-standar.git
cp -r email-standar/.claude/skills/email-standar \
      /ruta/a/tu/proyecto/.claude/skills/
```

Claude Code la detecta automáticamente y la puede invocar cuando el pedido sea sobre
crear, editar o auditar un correo HTML de marketing.

## Qué cubre

- **Funcionamiento interno**: las reglas de comportamiento propias de la skill (nunca
  guion largo, avisar imágenes pendientes, validar después de cada edición, etc.),
  separadas con claridad de los estándares generales de la industria.
- **Flujo de trabajo paso a paso**: qué hacer desde que alguien pide "necesito un
  correo de X" hasta entregar los dos archivos listos, partiendo siempre de la
  plantilla incluida en vez de empezar de cero.
- **Estructura de archivos**: por qué cada correo vive en dos archivos (documento
  completo para previsualizar + fragmento válido para el ESP) y cómo mantenerlos
  sincronizados.
- **Formato técnico**: qué etiquetas rechaza HubSpot en el módulo de HTML
  personalizado, y las 4 transformaciones exactas entre un archivo y el otro.
- **Imágenes**: el bug más común de imágenes diminutas en Gmail móvil, dónde alojar
  cada imagen (nunca en ruta local ni hosting externo no controlado), formatos
  compatibles, y tamaños de referencia por tipo de imagen.
- **Links y UTM**: cuántos links por correo, cómo escaparlos, por qué nunca usar
  acortadores, y por qué conviene armarlos con el generador de tracking URLs de
  HubSpot en vez de a mano.
- **Personalización segura**: cómo usar merge tags sin que un campo vacío rompa la
  gramática del correo.
- **Asunto y preview text**: el estándar de caracteres que llena la línea de la
  bandeja de entrada, con ejemplos reales.
- **Contexto del lector**: el principio general que decide el tono de un correo (si
  ya conoce el producto o no), con un ejemplo de cómo se ve aplicado a un sistema de
  3 niveles de engagement.
- **Entregabilidad**: SPF, DKIM, DMARC, unsubscribe de un clic, y las señales de
  contenido que activan filtros de spam, explicado en lenguaje simple.

## Estructura del repo

```
.claude/skills/email-standar/
├── SKILL.md                        ← guía principal, se carga siempre
├── templates/
│   ├── _template.html              ← documento completo, punto de partida de cualquier correo nuevo
│   └── _template-hubspot.html      ← el mismo, ya como fragmento válido para el ESP
└── references/
    ├── hubspot-format.md           ← detalle técnico del formato de HubSpot
    ├── deliverability.md           ← autenticación de dominio y spam triggers
    ├── imagenes.md                 ← dónde alojar imágenes, formatos, tamaños
    ├── segmentacion.md             ← el principio de contexto vs. sin contexto
    └── asunto-preview.md           ← estándar de caracteres, con ejemplos
```

## Licencia

MIT. Usalo, adaptalo, mandale un PR si encontrás algo que falta.
