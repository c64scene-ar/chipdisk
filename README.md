# Chipdisk

Se recomienda correrlo en una Commodore 64 de norma PAL-B y SID 8580.

## Cómo compilarlo

- Bajarse [cc65](http://cc65.github.io/cc65/) y ponerlo en el path
- Bajarse [exomizer](http://hem.bredband.net/magli143/exo/) y ponerlo en el path
- Bajarse [VICE](http://vice-emu.sourceforge.net/) y ponerlo en el path

Y luego darle `make`

## Tracks originales

En el directorio [res/orig](res/orig) se encuentran los archivos originales de
algunos de los .SIDs presentes en este chipdisk.  Las extensiones corresponden
a los siguientes trackers:

- `.ct`: CheeseCutter
- `.sng`: GoatTracker
- `.swm`: SID-Wizard

## Internals

### Directorio

- `src`: todo lo que se compila o se incluye: source code, assets listos para
  ser incluidos, SIDs comprimidos (.exo)
- `res`: los assets en formato "original". Ej: archivos .vcharproj, .sid, etc.
- `bin`: el binario compilado

### Memoria

Ver el archivo [chipdisk.cfg](chipdisk.cfg) que muestra cómo se usa la memoria.
Hay 2 lugares para agregar código. "CODE", y "MORECODE". Tratar de que todo
entre ahí para evitar sacarle espacio a posibles nuevos SIDs, y además vamos a
necesitar memoria para los botones.

De dónde sacar memoria en caso que se necesite más:

- Fonts: Estan reservados 2k para los fonts, pero probablemente no se necesite
  tanto.
- Comprimir los botones apretaods.
- Usar la memoria de $0400 a $0800 (1k) para poner código. La idea seria que
  ese código este temporalmente en $1000 y luego se copia a $0400
- El espacio de $1000 a $2800 esta reservado para tocar los sids. pero se puede
  usar cosas temporales (y luego moverlas a otro lado) antes de tocar el primer
  sid.
- El buffer de $1000 a $2800 tiene que ser tan grande como el tamaño del sid
  más grande a tocar. Ejemplo: Si el .sid más grande ocupa 4k, entonces ese
  tamaño se puede reducir de $1000 a $2000, liberando memoria (2k)

### SIDs comprimidos

Los SIDs estan comprimidos con exomizer dentro del binario. Para generar los
archivos .exo, usar este script:
[sid_to_exo.py](https://github.com/ricardoquesada/c64-misc/blob/master/tools/sid_to_exo.py)


## Que falta

### Necesario

- [ ] Los temas! Definir que temás van. Creo que solo hay 4 o 5 de temas de los temas oficiales (el resto de los temas que estan, son de prueba). Y ver si se pueden poner más de 8 temas.
   - [ ] Quedarían dos temas nuevos (uno de @comu y confirmar @munshkr?)
   - [ ] reorganizar la memoria para entren los 2 temas nuevos
   - [ ] El orden de temas lo decidimos intercalados entre músicos para que vaya variando, el orden de músicos es poco importante, se puede sortear.
- [x] Que detecte final de cancion y le de play a la siguiente. Detectar final de cancion se puede hacer que al cabo de N segundos toque la siguiente canción.  Se usa una suerte de "ticks por canción"
- [x] Que use los graficos de los botones apretados. Puede llegar a consumir mucha memoria. Hay que tener precaución al implementar esto.
- [x] Que anime la ruedita con sprites: se necesitan nuevos sprites
- [x] Que el contador este en el lugar correcto. Probablemente se pueda hacer con sprites. Se puede hacer con bitmap también pero el gráfico para el contador debería ser un poco más grande.
- [x] Fonts: Asi no usamos el default.
- [x] Led. Es rojo. En el monitor de tubo se ve casi negro. Por lo tanto cuando se le da Play a un tema tendría que cambiar a rosa para que haya una diferencia.
- [x] Los botones emulen el compartamiento de una casettera. Play tiene que quedar apretado. Stop nunca queda apretado. etc.., etc..
- Known bugs:
   - [x] se esta pisando la sombra del cassette cuando se plotea los nombres. Hay que hacer un _save_ de esa celda y luego _restorearla_
   - [x] cuando avanza automáticamente al siguiente tema, no debería activarse el botón de FF ni "rebobinar" (la animación de las rueditas debería mantenerse como cuando se reproduce música)
   - [x] hay que sacar los sprites de la botonera, ya no son necesario ahora que se anima la presión de los botones
   - [x] no se ve la animación del botón de Stop, creo que hay que retrasar la restauración de la imagen original uno o 2 de frames para que se note
   - [x] al darle Stop, a veces el sonido se queda colgado
   - [x] no se renderea bien la M y W (riq)
   - [ ] problemas de colores con font + fondo: naranja y negro. Todavía quedan algunos pixeles por fixear. hay que multiplexar los sprites, ya que nos quedamos sin.
        - [ ] Determinamos que no hace falta OK - fixear los posibles pixels negros/marrones que aparecerían con los nombres largos
   - [x] cuando rebobina, las rueditas deberían animare al reves (munshkr)
   - [x] la animación de las rueditas debería ser un toque más lento, en el play sobre todo (munshkr)
   - [x] garabatos del renderer de fonts (riq)
   - [x] "Naku" -> "Los Pat Moritas" 
   - [x] "Balloon Country " -> "Balloon Country Bursts"
   - [x] "Yasashisa Ni" -> "Yasashisa Ni Tsutsumareta Nara"
   - [ ] Poner el tiempo de duración de los últimos 7 temas. Ahora estan harcodeados a 1:30 creo.
        - [ ] Leetit 3
        - [x] Pop Goes The World
        - [x] Se Voce Jurar
        - [ ] Mongolongo
        - [ ] Matraca 3
        - [ ] Drogacumbia
        - [ ] Juanelo
   - [ ] Bug found: se desincroniza los canales del sid una vez que empieza a playear de nuevo luego. Esta bueno igual pero hay que avisar que esta programado o fixear o que termine luego de la primer edicion

### Opcional

- [x] Easter Egg: definir que tiene que hacer, y hacerlo
   - [ ] que el estear egg aparezca en el ultimo tema o combinacion de teclas
   - [ ] texto para el scroller del easter egg: @Arlek mandará un preview en lo próximo
   - [ ] que en el medio de entre peron y darth vader, aparezca darth vader con las manos de peron. Es posible?
- [x] Soporte de teclado. En vez que las teclas muevan el cursor como si fuera un mouse, que cicle entre posiciones predefinidas como "Play", "Rew", ... (munshkr)
- [ ] Fade In al comienzo. Usar la memoria de $1000 a $2800 para eso, para no quitar memoria del player
- [ ] Que se mueva la panza del casette a medida que avanza la cinta
- [x] Ruido blanco antes de empezar a sonar el tema (riq)
- [ ] Más información. Ya que la información sobre el autor y nombre del tema está muy limitada, tal vez se podría implementar que presionando la barra espaciadora, o cualquier otra cosa, se pase a una pantalla con información más detallada, tal vez una pequeña reseña del autor, o si es un cover, la información necesaria. Esto podría ser incluso diagramado como si fuera el booklet del casette escrito a mano.
- [ ] Casette. Cuando uno presiona F.FWD y REW además de cambiar la información de tema y autor que cambie el color del casette. Esto no estoy tan convencido, pero por ahí queda bien.

## Licencia

Se distribuye el código fuente bajo la licencia Apache 2.
Ver [LICENSE](LICENSE) para más información.
