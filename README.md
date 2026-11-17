# Destajos en vivo — maqueta

Maqueta de cómo se vería repartir el trabajo entre dos pantallas:

- **Oficina, en web.** Administra el catálogo de conceptos de la semana: da de
  alta, corrige cantidades y precios. Es la pantalla grande, con su tabla.
- **Obra, en el teléfono.** El residente nada más revisa los destajos y reporta
  avance. En la maqueta se ve a la derecha, como testigo de lo que ya llegó.

Lo que se cambia de un lado aparece del otro al momento: nadie guarda, nadie
recarga, nadie manda el PDF.

**No es la app.** Los datos son de ejemplo y viven dentro de la página; no hay
base de datos ni usuarios. Es para ver la idea y decidir si se arma. Proyecto
aparte de `rocal-mobile-pwa`, no toca nada de la PWA.

## Verla

`index.html` es un solo archivo, sin build y sin dependencias. Doble clic, o
con un servidor estático si se quiere ver desde otra máquina:

```
npx --yes serve .
```

Conviene verla en pantalla ancha: abajo de 1040 px el teléfono se acomoda
debajo de la tabla, en lugar de a un lado.

## El despliegue de cada destajo

Al abrir un trabajo, lo primero es la frase: por cada m² se pagan tanto,
pagarle a la gente cuesta tanto, quedan tanto. Verde si deja, naranja si está
apretado, rojo si pierde. Debajo viene el desglose con la misma forma que la
matriz de OPUS, para quien lo quiera revisar:

- **Análisis del precio unitario**: los insumos arriba (mano de obra,
  herramienta, materiales, equipos) y abajo el renglón de la cuadrilla con su
  clave, unidad jornada, cantidad, rendimiento, costo unitario y total.
- **La cuadrilla desglosada**: cabo de oficios, maestro albañil, albañil y
  ayudante general, cada uno con su clave, su cantidad y su jornal. La
  cantidad y el rendimiento son recíprocos: el cabo que anda en diez frentes
  entra con 0.100000 y rendimiento 10.000000.
- **La cantidad de la cuadrilla es 1 entre el rendimiento**, igual que en
  OPUS: si saca 22.08 M2 al día, cada M2 se lleva 0.045290 de jornada.
- **Herramienta menor**, el 3% de la mano de obra.

Lo que OPUS no trae y aquí sí:

- **Índice de variabilidad**, que castiga el rendimiento de la matriz con lo
  que la obra se come: clima, retrabajos, acarreo largo, esperar material.
- **Medido en obra**: el rendimiento real de las últimas semanas, sacado de
  las jornadas que ya reportó el residente, y el índice que sale solo de ahí.
  El botón lo aplica; de ahí en adelante se recalcula con cada avance.
- **Jornales por municipio**: todas las obras son de Tlaxcala, así que lo que
  cambia es el municipio. El factor no es el salario mínimo de la zona: es lo
  que de verdad mueve el jornal dentro del estado, el traslado de la gente y
  que haya o no cuadrillas libres cerca. Calpulalpan jala para arriba por la
  cercanía con el Edomex.

Los conceptos, unidades y precios unitarios salen del catálogo SPM 2026. Las
cuadrillas, rendimientos y lo medido son de la maqueta.

## La entrada (`login.html`)

Es por donde se empieza. Correo, contraseña y una pregunta: si entras desde
**Oficina** o desde la **Obra**. Oficina llega al tablero (`index.html`) y
obra al teléfono (`movil.html`). El tablero manda de regreso al login si no
hay nadie dentro, y el botón **Salir** de la barra de arriba cierra la
sesión.

No hay cuentas de verdad todavía: la sesión es una llave en el navegador y
lo único que se valida es que el correo traiga arroba y la contraseña no sea
de tres letras, para que se vea cómo avisa cuando algo falta. Cuando haya
back, esto lo reemplaza la sesión de Supabase.

## Las tres vistas de la web

- **Semana en curso**: el catálogo de la semana con el despliegue de cada
  destajo. Es lo que oficina administra día con día.
- **Catálogo**: los conceptos establecidos con su precio vigente, desde
  cuándo y de dónde salió. Arriba, los cambios por revisar: propuestas que
  salen solas de lo que la obra mide (un rendimiento que no se cumple, un
  precio al que ya nadie toma el destajo, un concepto que el residente
  capturó y no existe en el catálogo). Cada una dice con cuántas jornadas
  medidas viene. El interruptor decide el trato: apagado, todo espera a que
  alguien lo apruebe; encendido, lo que trae 20 jornadas o más se aplica solo
  y nada más se avisa. Lo aprobado baja al concepto de la semana y la obra lo
  ve en el teléfono.
- **Obras**: dónde se está llevando el destajo y quién lo lleva. Por obra:
  municipio, residente, avance de la semana, conceptos abiertos y hace cuánto
  fue la última captura, con la que lleva el día sin capturar marcada en rojo.
  En la maqueta las obras están a mano; de verdad saldrían del padrón de
  obras, y aquí nada más se le colgaría el destajo de cada una.

- **Mercado**: de dónde se alimenta el jornal. Una tarjeta por fuente, con lo
  que trajo, cuándo se leyó y su liga: vacantes de la zona (Indeed), Data
  México / ENOE, el mínimo de Tlaxcala, y lo pactado con las cuadrillas, que
  todavía no se captura y por eso sale en rojo. Abajo, lo que proponen entre
  todas: mover el jornal base, no el precio concepto por concepto — ese lo
  recalcula la matriz. También se listan las fuentes que NO se leen y por qué
  (los blogs de tabuladores se contradicen solos; los directorios de
  cotización mezclan material con mano de obra). La lectura corre una vez al
  mes, nada de aquí se aplica solo, y un movimiento mayor al 5% se detiene.

## La pantalla del residente (`movil.html`)

La maqueta del teléfono, aparte, para abrirla en el celular. Está escrita para
quien captura en la obra, no para quien hizo la matriz: sin "rendimiento de
cálculo" ni índices ni porcentajes.

- **La semana**: cada trabajo con su nombre en palabras ("Levantar muro de
  tabique"), lo que lleva, lo que falta y lo que va ganado.
- **Un trabajo abierto**: cuánto le pagan por unidad, quiénes trabajan ("2
  albañiles, 2 ayudantes, y el cabo a ratos") y cuánto cuesta ese día de
  gente.
- **Dos preguntas, nada más**: cuánto hicieron hoy (con botones de +1, +5,
  +10, +25 para no teclear) y cuánta gente trabajó (media cuadrilla, la
  cuadrilla, y media, dos).
- **El veredicto, en una frase**: *El día salió* en verde, o *El día no
  salió* en rojo, con los tres números que lo sostienen — lo que vale lo
  hecho, lo que cuesta la gente y la diferencia — y dos barras comparando el
  ritmo de hoy contra lo normal.

## Qué se puede tocar

- **Cambia una cantidad o un precio** en la tabla: el importe del renglón, el
  total de la semana y el teléfono se mueven en el mismo momento.
- **+ Concepto** da de alta uno nuevo; aparece también en el teléfono.
- **El residente reporta avance** es el camino de regreso: sube la cantidad
  desde la obra y el renglón se marca en naranja con quién lo movió. Pasa solo
  cada 12 segundos, para que no se vea una pantalla quieta.

## Si se decide armarla de verdad

- Una tabla de conceptos en Supabase publicada en Realtime, y las dos
  pantallas suscritas a la obra.
- Permisos por papel: oficina edita el catálogo, la obra solo reporta avance.
- La cola de lo reportado sin señal, que es lo que de veras cuesta en obra.
