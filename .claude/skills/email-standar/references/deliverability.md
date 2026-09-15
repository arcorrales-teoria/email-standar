# Entregabilidad: por qué un correo cae en spam (y cómo evitarlo)

Entregabilidad es, en simple, la probabilidad de que un correo que enviaste llegue a
la bandeja de entrada del destinatario en vez de a la carpeta de spam (o directamente
se rechace). No depende de una sola cosa: es la suma de señales técnicas (¿de verdad
sos quien decís ser?) y señales de contenido (¿este correo se parece a los que la
gente marca como spam?).

## 1. Autenticación del dominio: demostrar que sos quien decís ser

Los tres mecanismos trabajan juntos, y cada uno resuelve una pregunta distinta que se
hace el servidor de correo que recibe tu mensaje:

### SPF (Sender Policy Framework)
**Pregunta que responde**: "¿este servidor tiene permiso para mandar correos en
nombre de este dominio?"
Es un registro DNS tipo texto que lista qué servidores están autorizados a enviar
como `@tudominio.com`. Si el correo llega desde un servidor que no está en esa lista,
es una señal fuerte de suplantación.
**Quién lo configura**: la mayoría de ESPs (HubSpot incluido) lo configuran
automático apenas conectás y verificás el dominio de envío.

### DKIM (DomainKeys Identified Mail)
**Pregunta que responde**: "¿el contenido de este correo se modificó en el camino?"
Es una firma criptográfica que se agrega a cada correo enviado. El servidor de
destino usa una clave pública (publicada en el DNS) para verificar que la firma es
válida y que el correo no fue alterado después de salir del servidor de origen.
**Quién lo configura**: igual que SPF, automático con la mayoría de ESPs al conectar
el dominio.

### DMARC (Domain-based Message Authentication, Reporting & Conformance)
**Pregunta que responde**: "si un correo falla SPF o DKIM, ¿qué hacer con él?"
Es una política que vos publicás en el DNS: puede decir "no hagas nada" (`p=none`,
solo para monitorear), "mandalo a spam" (`p=quarantine`), o "rechazalo directamente"
(`p=reject`). Sin un DMARC publicado, cada proveedor de correo decide por su cuenta
qué hacer con los correos que fallan SPF/DKIM, lo cual es menos predecible.
**Quién lo configura**: a diferencia de SPF/DKIM, esto normalmente **hay que
agregarlo a mano** en el DNS del dominio. El ESP no lo hace solo, aunque algunos dan
instrucciones paso a paso.

## 2. Unsubscribe de un clic (RFC 8058)

Desde 2024, Gmail y Yahoo exigen que los correos masivos (más de ~5000 al día a esos
dominios) incluyan un mecanismo de baja de un solo clic, implementado a nivel de
cabecera del correo (`List-Unsubscribe` + `List-Unsubscribe-Post`), no solo un link
visible en el footer. Los ESPs grandes (HubSpot incluido) lo agregan automático a los
correos de tipo "marketing". Lo que sí depende de quien arma el correo:

- Poner el link real de baja del ESP en el footer (en HubSpot, el merge tag
  `{{ unsubscribe_link }}`), nunca un link inventado o un `mailto:`.
- No poner el link de baja detrás de un formulario largo ni pedir que inicien sesión:
  cuanto más fricción, peor señal para los filtros y peor experiencia.

## 3. Dirección postal en el footer

Exigencia legal (CAN-SPAM en Estados Unidos, y equivalentes en la mayoría de países
con regulación de email marketing): todo correo comercial necesita una dirección
postal física válida del remitente. Además de ser un requisito legal, su ausencia es
una señal de spam para varios filtros automáticos.

## 4. Señales de contenido que activan filtros de spam

Ningún filtro moderno usa solo una lista de palabras prohibidas (los filtros actuales
son mucho más sofisticados, con machine learning), pero ciertos patrones siguen
correlacionando fuerte con spam real y conviene evitarlos:

- **Mayúsculas sostenidas** en el asunto o el cuerpo ("ÚLTIMA OPORTUNIDAD")
- **Signos de exclamación múltiples o repetidos** ("¡¡¡Oferta!!!")
- **Palabras gatillo clásicas**: "gratis", "urgente", "última oportunidad", "haz clic
  aquí ahora", "gana dinero", "sin costo", "100% garantizado"
- **Exceso de símbolos de dinero o porcentaje**: "$$$", "50% OFF!!!"
- **Desbalance extremo entre mayúsculas y minúsculas**, o texto en un solo color
  brillante sin variación
- **Un solo link repetido muchas veces**, o **acortadores de link** (`bit.ly`,
  `hubs.ly`): varios filtros los tratan como señal de ocultamiento del destino real

## 5. Ratio texto/imagen y peso del correo

- **Ratio texto/imagen ideal: ~60% texto / 40% imagen.** Un correo que es casi solo
  una imagen grande (a veces con todo el mensaje escrito adentro de la imagen, para
  "engañar" a los filtros de texto) es uno de los patrones más viejos y más
  penalizados de spam.
- **Peso total del correo: menos de 100KB** (sin contar imágenes externas, que se
  cargan por link, no van embebidas). Un correo pesado tarda en renderizar y varios
  clientes lo recortan (Gmail, por ejemplo, corta correos de más de 102KB con un
  "Ver mensaje completo").
- Las imágenes van siempre **hosteadas externamente** (en el propio dominio, o en el
  administrador de archivos del ESP) y referenciadas por URL, nunca embebidas en
  base64 dentro del HTML: eso infla el peso del correo enormemente.

## 6. Antes de cada envío masivo

1. Mandar el correo de prueba a una cuenta propia real (no solo la vista previa del
   editor del ESP).
2. Revisarlo en al menos: Gmail de escritorio, Gmail app (iOS o Android), y Outlook
   de escritorio si tu audiencia lo usa.
3. Confirmar que el dominio de envío tiene SPF, DKIM y DMARC configurados (la mayoría
   de ESPs tiene una pantalla de "estado del dominio" que lo muestra).
4. Revisar que el asunto y preview no disparen ninguna de las señales de la sección 4.
