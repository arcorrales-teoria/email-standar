# html-email-deliverability

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

Cloná este repo (o copiá la carpeta `.claude/skills/html-email-deliverability/`)
dentro de la carpeta `.claude/skills/` de tu proyecto:

```bash
git clone https://github.com/<tu-usuario>/html-email-deliverability.git
cp -r html-email-deliverability/.claude/skills/html-email-deliverability \
      /ruta/a/tu/proyecto/.claude/skills/
```

Claude Code la detecta automáticamente y la puede invocar cuando el pedido sea sobre
crear, editar o auditar un correo HTML de marketing.

## Qué cubre

- **Estructura de archivos**: por qué cada correo vive en dos archivos (documento
  completo para previsualizar + fragmento válido para el ESP) y cómo mantenerlos
  sincronizados.
- **Formato técnico**: qué etiquetas rechaza HubSpot en el módulo de HTML
  personalizado, y las 4 transformaciones exactas entre un archivo y el otro.
- **Imágenes responsivas**: el bug más común (imágenes diminutas en Gmail móvil) y
  la regla que lo evita.
- **Links y UTM**: cuántos links por correo, cómo escaparlos, por qué nunca usar
  acortadores.
- **Personalización segura**: cómo usar merge tags sin que un campo vacío rompa la
  gramática del correo.
- **Asunto y preview text**: el estándar de caracteres que llena la línea de la
  bandeja de entrada, con ejemplos reales.
- **Segmentación por temperatura**: cómo construir una misma pieza para audiencias
  Cold/Warm/Hot sin escribir 3 correos distintos ni sonar repetitivo.
- **Entregabilidad**: SPF, DKIM, DMARC, unsubscribe de un clic, y las señales de
  contenido que activan filtros de spam, explicado en lenguaje simple.

## Estructura del repo

```
.claude/skills/html-email-deliverability/
├── SKILL.md                        ← guía principal, se carga siempre
└── references/
    ├── hubspot-format.md           ← detalle técnico del formato de HubSpot
    ├── deliverability.md           ← autenticación de dominio y spam triggers
    ├── segmentacion.md             ← patrón Cold/Warm/Hot
    └── asunto-preview.md           ← estándar de caracteres, con ejemplos
```

## Licencia

MIT. Usalo, adaptalo, mandale un PR si encontrás algo que falta.
