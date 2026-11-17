# Simulador de materiales · Sistema B1

Producción submarina y conducción a tierra, del yacimiento al flowline.
Muestra qué piezas resisten, cuáles fallan, cómo fallan y por qué, y escoge
el material más adecuado cuando cambian las condiciones.

## Cómo abrirlo en Visual Studio Code

1. Descomprimir la carpeta `simulador-b1`.
2. En VS Code: **Archivo → Abrir carpeta** y elegir `simulador-b1`.
3. Abrir `index.html`:
   - Clic derecho → **Reveal in File Explorer** y doble clic sobre el archivo, o
   - Con la extensión **Live Server**: clic derecho → *Open with Live Server*.

No hay que instalar nada más. Es HTML, CSS y JavaScript sin dependencias.

## Qué hay en cada página

**`index.html`: el sistema completo.** Esquema con el gas y el MEG en movimiento.
Cada parte toma el color de su pieza en peor estado y las que fallan laten.
Al tocar una parte se ve un resumen y el botón para abrir su simulador.

**`componentes/*.html`: un simulador por parte (14 páginas).** Arriba aparece el
dibujo del equipo en corte, como es en la realidad, con el gas circulando y sus
partes móviles en movimiento. El borde de cada pieza lleva el color de su estado
y al tocarla se va a su tarjeta. Debajo, cada pieza tiene:

- Un dibujo animado del corte de su pared: a la izquierda lo que la toca, en el
  centro sus materiales y a la derecha lo que hay al otro lado.
- Un titular con lo que le pasa (se agrieta, se adelgaza, se pica, se endurece,
  se deshace) y la explicación de por qué.
- Los materiales candidatos. Cada uno muestra cómo le iría en los 25 años.
  Al tocar uno se prueba en el dibujo. El recomendado va marcado.
- El detalle técnico con las normas, plegado.

| Página | Parte |
| --- | --- |
| `formacion.html` | 1 · Formación productora |
| `casing.html` | 2 · Casing (revestimientos) |
| `tubing.html` | 3 · Tubing de producción |
| `sssv.html` | VS · Válvula de seguridad del tubing |
| `packer.html` | 4 · Packer y sellos |
| `cabezal.html` | 5 · Cabezal de pozo |
| `arbol.html` | 6 · Árbol de producción submarino |
| `vmaestra.html` | 7 · Válvula maestra: cuerpo y bonete |
| `esparragos.html` | 8 · Espárragos y tuercas de la brida |
| `trim.html` | 9 · Trim de la válvula maestra |
| `jumper.html` | 10 · Jumper / manifold |
| `flowline.html` | 11 · Flowline a tierra |
| `meg.html` | MEG · Sistema de inyección de MEG |
| `llegada.html` | 12 · Válvula de llegada (datos del informe B2) |

Las condiciones y los materiales elegidos se conservan al pasar de una página a otra.

## La válvula de llegada

Es el límite entre el sistema B1 y la planta B2. Sus piezas y condiciones salen del
informe B2 (Tabla 8, etapa 01, y Tabla 18, nivel A):

- Cuerpo y extremos: acero de baja aleación con overlay de Alloy 625. NPS 8, clase 2500, API 6D.
- Trim y asientos: el informe los deja "por fabricante"; el simulador propone uno.
- Junta de aislamiento eléctrico: kit G10 / epoxi, con sus sellos.
- Servicio: gas, condensado, agua agria y MEG; 95 °C de operación y 120 °C máximo.
- Por fuera: aire marino de la costa.

El informe B2 advierte que los 320 bar son la base del escenario en el cabezal, no la
presión medida en la frontera. La presión real de llegada está pendiente del acta B1–B2.
El G10 y el G11 de la junta usan temperaturas guía de fabricantes.

## Cómo leer los dibujos

| Se ve | Significa |
| --- | --- |
| Puntos amarillos | H₂S |
| Puntos grises | CO₂ |
| Puntos azules | Agua |
| Puntos verdes | Sal del agua de formación (desde el año 8) |
| Puntos rosados | MEG |
| Los puntos rebotan | El material resiste |
| Los puntos entran en la pared | El material está siendo atacado |
| Línea roja en zigzag | Grieta |
| Pared comida desde la izquierda | Pérdida de espesor; al llegar al otro lado aparece "Fuga" |
| Huecos redondos en el borde | Picaduras |
| Rayas claras sobre un caucho | Endurecimiento y cuarteo |
| Franja clara que atraviesa el revestimiento | Poro o defecto por donde entra el gas |

## Cómo escoge el material

Cada pieza pertenece a un grupo de candidatos (`B1.grupos` en `js/datos.js`).
El simulador prueba cada candidato año por año durante los 25 años y escoge:

1. El que termina en mejor estado (resiste > en riesgo > falla).
2. Entre los empatados, el de menor costo relativo.
3. Si aún empatan, el del inventario.

Si una pieza no toca el gas en esa condición, no propone cambio.

## Archivos

| Archivo | Para qué sirve |
| --- | --- |
| `js/datos.js` | **Lo que más se edita:** condiciones base, materiales con sus normas, límites y costos, grupos de candidatos e inventario. |
| `js/modelo.js` | Reglas de evaluación, textos de cómo y por qué falla, y selección de material. |
| `js/equipos.js` | Dibujo realista y animado de cada equipo (uno por componente). |
| `js/animacion.js` | Dibujo animado de cada pieza. |
| `js/componente.js` | Página de cada componente. |
| `js/app.js` | Página del sistema completo. |
| `js/memoria.js` | Conserva condiciones y materiales entre páginas; escenarios rápidos. |
| `css/` | `estilos.css` (base), `componente.css`, `equipos.css`, `animacion.css`, `mapa.css`. |

Las páginas de `componentes` necesitan las carpetas `js` y `css`; no funcionan si se copia el `.html` solo.

## Normas que usa

- **Servicio amargo en aceros:** ISO 15156-2 (región de severidad SSC, dureza máx. 22 HRC, tabla A.3 para tubulares).
- **Servicio amargo en aleaciones:** ISO 15156-3, anexo A (límites de temperatura, pH₂S, cloruros y pH por aleación).
- **Normas de producto:** API 5CT / ISO 11960, ISO 13680, API 5L / ISO 3183, API 6A, API 6ACRA, ASTM de cada aleación.
- **Cauchos y plásticos:** ISO 23936-1 y -2.
- **Cemento:** API 10A / ISO 10426-1 y API RP 10B-2.
- **Exterior:** DNV-RP-B401, F103 y F112.

Cada material lleva su norma de producto y su norma de límite en `js/datos.js`, y
aparecen en el "Detalle técnico y normas" de cada pieza.

## Lo que hay que confirmar antes de citarlo en el informe

Es una herramienta de cribado para clase. No reemplaza la norma.

1. **Límites de ISO 15156-3.** Están cargados como referencia de trabajo, no copiados
   de la norma. Revisar fila por fila contra la edición 2020. Los más delicados:
   N08926, 316/316L, dúplex y superdúplex, 13Cr, 17-4PH, Alloy 28 / 825 / G-3 / C-276
   trabajados en frío (tabla A.14) y Alloy 718 / 925 (tabla A.32).
2. **Costos.** Son una escala relativa de 1 a 10 puesta a criterio. Cambian la
   recomendación, así que conviene ajustarlos con datos reales.
3. **Candidatos.** Los materiales alternativos no vienen del informe: son opciones
   habituales de la industria añadidas para poder comparar.
4. **Cauchos.** Temperaturas y resistencia al H₂S son valores guía de fabricantes.
   ISO 23936-2 define el ensayo, no un límite.
5. **Región SSC.** Las fronteras de la figura 1 de ISO 15156-2 están aproximadas con rectas.
6. **Corrosión del acero.** Modelo de de Waard, Lotz y Milliams (1991), solo CO₂.
   Con tanto H₂S el número es una cota, no una predicción.
7. **Temperatura y pH.** Se usa la temperatura máxima en todas las piezas mojadas y
   el mismo pH antes y después del año 8.
8. **Monel 400.** Queda "en riesgo" porque la norma solo cubre agrietamiento y no hay
   dato de pérdida de masa en H₂S húmedo. Es una duda real del diseño, no un cálculo.
