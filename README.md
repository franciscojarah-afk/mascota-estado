# mascota-estado

Puente de solo datos entre el chat de **Mascota IA** (vive en un Artifact de
claude.ai, con estado en la capability `db`) y el widget nativo de iOS
(WidgetKit, no puede leer `db` directamente — solo puede hacer peticiones
HTTP normales).

Este repo público existe únicamente para que `raw.githubusercontent.com`
sirva `estado.json` como una URL fija que el widget consulta en cada
actualización de su timeline.

## Quién escribe acá

Una sesión de Claude Code (con red sin restricciones, a diferencia de la
sesión de Cowork donde vive el chat) lee el estado real vía la herramienta
`db` del artefacto y sobreescribe `estado.json` + hace commit y push. No hay
ningún proceso corriendo 24/7 de forma autónoma todavía — hoy este archivo
es un ejemplo para desarrollar el widget sin depender de la conexión en vivo.

## Esquema de `estado.json`

Espejo exacto de las variables que ya usa la máquina de estados en el
Artifact (`computeMotor` / `pickSprite` en el HTML publicado):

- `tasks[]`: mismos campos que la colección `tasks` de la `db` — `status`
  (`activo` | `pendiente` | `fallido`), `startedAt`, `pendingSince`,
  `failCount`, `kind`.
- `lastActivityAt`: epoch ms de la última actividad — determina si el
  sprite pasa a `durmiendo` tras 2h sin nada.
- `felizUntil`: epoch ms hasta el cual mostrar `completado`. Con el
  refresco de un widget (cada 15–30 min, lo decide iOS) es poco probable
  que se alcance a mostrar el pulso de 5s que sí se ve en el chat en vivo —
  limitación aceptada, no vale la pena resolverla.
- `lastMessage`: opcional, para una línea de detalle bajo el sprite si el
  tamaño del widget lo permite.

## Por qué esto y no una API propia

Armar un backend propio (con auth, hosting, etc.) para esto es
desproporcionado para lo que hace falta: un archivo JSON público de
lectura, actualizado pocas veces al día. GitHub + raw.githubusercontent.com
ya resuelve "hosting + versionado + gratis" sin mantener nada nuevo.
