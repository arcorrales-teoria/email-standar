# Asunto y preview text: el estándar de caracteres, explicado

## Por qué importa el largo exacto

En la bandeja de entrada de Gmail de escritorio (el cliente más usado en B2B), cada
correo se muestra en una sola línea: remitente, luego asunto, luego preview text
(también llamado "texto de vista previa" o "preheader"), todo concatenado hasta que
se corta el ancho disponible. Si el asunto es muy corto, el preview queda con espacio
de sobra y el correo se ve "vacío" al lado de otros en la bandeja. Si es muy largo, se
corta a la mitad de una palabra o de una idea, lo cual se ve descuidado y puede
confundir.

## El estándar

- **Combinado (asunto + preview): 110-125 caracteres.** Es el rango que llena la
  línea completa en Gmail de escritorio sin cortarse ni dejar hueco.
- **Asunto: 45-58 caracteres.** La idea principal tiene que estar en los primeros
  ~40 caracteres, porque en mobile (donde se corta antes, entre 30-40 caracteres
  según el dispositivo) es lo único que se alcanza a leer.
- **Preview text: 55-68 caracteres.**

## El preview NO repite el asunto

Es el error más común: escribir un preview que dice básicamente lo mismo que el
asunto con otras palabras. El preview es espacio extra, no un resumen del asunto:
tiene que agregar información nueva, dar el "por qué seguir leyendo", o rematar la
idea que el asunto dejó abierta.

**Mal** (preview repite el asunto):
- Asunto: "Qué es la identidad digital"
- Preview: "Te explicamos qué es la identidad digital"

**Bien** (preview complementa):
- Asunto: "Qué es la identidad digital (y por qué es importante)"
- Preview: "La base de toda la transformación digital de las empresas hoy"

## El asunto tiene que funcionar solo

No puede depender de que el lector recuerde algo puntual (un evento al que fue, un
correo anterior que quizás no le llegó, contenido que quizás no leyó). Cuanto menos
contexto previo exige, mayor la probabilidad de que lo abra alguien con cero memoria
de la marca.

**Mal** (asume que recuerda a la marca):
"Por qué todo en [Marca] empieza por la identidad digital"

**Bien** (funciona sin ningún contexto previo):
"Fraude, onboarding y firma digital son la misma historia"

## Evitar palabras que activan spam en el asunto específicamente

El asunto es lo primero que analizan los filtros, así que ahí el cuidado es doble:
nada de mayúsculas sostenidas, signos de exclamación múltiples, ni palabras gatillo
("gratis", "urgente", "última oportunidad"). Ver `deliverability.md` para la lista
completa.

## Ejemplos reales, con conteo

| Asunto | Chars | Preview | Chars | Total |
|---|---|---|---|---|
| Qué es la identidad digital (y por qué es importante) | 55 | La base de toda la transformación digital de las empresas hoy | 63 | 119 |
| La Identidad y el fraude son procesos complementarios | 55 | Son los que permiten construir perfiles multidimensionales | 60 | 116 |
| ¿Más seguridad o más conversión? Es la pregunta equivocada | 60 | La respuesta está en cómo armas el flujo, no elegir entre uno y otro | 70 | 131 |

(El último se pasa un poco del rango ideal, pero se mantuvo porque el asunto es una
pregunta completa con fuerza propia: cortarlo le hubiera quitado el gancho. El
estándar es una guía, no una regla absoluta cuando hay una razón de peso para
romperla.)

## Cómo medir mientras se escribe

Un one-liner rápido para contar caracteres sin depender de ninguna herramienta
externa (sirve en cualquier terminal con Python instalado):

```bash
python3 -c "s='ASUNTO AQUÍ'; p='PREVIEW AQUÍ'; print(len(s), len(p), len(s)+len(p)+1)"
```

El `+1` es por el separador (guion, punto, o el espacio) que Gmail agrega entre
asunto y preview al concatenarlos en la vista de bandeja de entrada.
