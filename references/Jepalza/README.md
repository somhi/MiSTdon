### [Re: Adaptador "casi" compatible con MIST](http://www.forofpga.es/viewtopic.php?f=141&t=289#p1199)

- [Citar](http://www.forofpga.es/posting.php?mode=quote&f=141&p=1199)

[Mensaje ](http://www.forofpga.es/viewtopic.php?p=1199#p1199)por **[jepalza](http://www.forofpga.es/memberlist.php?mode=viewprofile&u=48)** » 04 Oct 2019, 10:37

Elementos necesarios:

-----PRIMERO-------
Placa principal, ARM de ATMEL, AT91SAM7S256, yo la compré directamente en OLIMEX (entiendo que es original), pero a un precio elevado, de 35€ (con gastos):
https://www.olimex.com/Products/ARM/Atmel/SAM7-H256/

Pero luego, un compañero (Fran, no doy mas datos, por que no le he pedido permiso para indicarlo)
me dijo que había una mas barata en Aliexpress, a un tercio de lo que yo pagué, por solo 12€:
[https://es.aliexpress.com/item/32826609 ... 2f01uWpreO](https://es.aliexpress.com/item/32826609273.html?spm=a219c.12010612.8148356.4.7e0a2f01uWpreO)

No es la misma, y cambian algunos pines, pero entiendo que es compatible 100%
Esta placa es necesario programar con el FIRM original de la MIST. Ver siguiente mensaje para ver cómo, por que , a pesar de ser fácil, tiene su complicación.


-----SEGUNDO----------
Lector de SD, esto, es para gustos. Yo he comprado uno con SD "normal" (no "micro") por tan solo 1€!!
Si el lector lleva pines WP y CD mejor, por que es mas compatible con el MIST, si no los lleva (como es mi caso), no pasa nada, se añaden puentes.


------TERCERO--------
Y por último, lector de USB a SPI, con Chip MAX3421E, solo 3€:
[https://es.aliexpress.com/item/32735946 ... b201603_52](https://es.aliexpress.com/item/32735946108.html?spm=a2g0o.productlist.0.0.3d4c75fayGLxSa&algo_pvid=f2781575-cd6f-4b94-9d33-b83c690725f4&algo_expid=f2781575-cd6f-4b94-9d33-b83c690725f4-0&btsid=d79b6ae9-3c1a-436a-b1cd-66ce543bb138&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_52)

(nota, si lo buscas por separado, no busques MAX3421 por que no aparece casi nada, busca por "Host USB compatible con Google ADK para Arduino")

De esta placa, hay oficiales, y clones. Los clones han invertido por error, la serigrafía de los pines MOSI y SCK. Hay mucha info por la red sobre este fallo. Yo lo corroboro, por que miré los pines uno a uno, y realmente están invertidos ambos.
Imaginaros el problema que tuve durante días, por que no funcionaba. Le dí mil vueltas al tema sin saber por que no funcionaba. Hasta que leí lo del fallo. Fue cambiar un pin por otro, y a la primera funcionó.
Ademas, en estas placas MAX3421 Clón hay otro problema, y es que, vienen "atadas" a 3.3v fijos para alimentar el USB, lo que hace que muchos teclados no funcionen por que no loes llega los 5v. necesarios. En cambio, los ratones a 3.3v funcionan la mayoría.
La solución está en internet, en muchas páginas, pero resumo diciendo, que solo es cortar una pista y alimentar ese pin por separado a +5v.
Lo indico en el dibujo esquemático.

En total, solo 35+1+3=39€ (en mi caso, al comprar el ARM mas caro), pero si compras el ARM barato, solo 12+1+3=15 euros!!!!

Notas sobre el "esquema" (por llamarlo de algún modo, por que es un simple BMP hecho a mano con PAINT):
Su construcción es simple, lo mas importante, son los pines MISO, MOSI y SCK, por que deben ir de módulo en módulo, o sea, de la FPGA, al ARM, a la SD y al USB-MAX (no importa el orden). Luego, cada módulo SPI lleva su "SS", que son: (según esquema MIST)
SS0=USB, SS1=SD, SS2=FPGA y SS3=OSD-ATMEL-ARM. Luego, tenemos el SS4, que yo NO he conectado, por que es para acceso directo a SD desde la FPGA, para que algunos cores usen la SD sin pasar por el ARM, pero sinceramente, no he visto ningún core (de los que he probado) que lo use, por lo tanto, a pesar de que lo tengo reservado en el conector, no lo tengo cableado.

El MIST necesita pines de la SD llamados CD y WP (Card Detect y Write Protect), y como mi lector SD de un euro no lleva, tengo puenteados a GND (activos) el del CD, pero el del WP lo tengo "abierto", por que entiendo que de ese modo se permite grabar en la SD (no lo he probado).

La placa USB-SPI lleva un pin RST que activado es a +3.3v. En la placa Clón de 3€, he visto (y leído por la red) que DEBE estar siempre activo, o sea, puenteado a +3.3v para que se inicie siempre en modo 12mhz de velocidad (reloj externo). No sé si esto es cierto, pero lo que si es verdad, es que, sin ese puente a +3.3, la placa no se detecta por el ARM, y los USB no funcionan. Por lo tanto, puente a +3.3 "con la clónica", por que con la oficial debe de llevar un "pull-up" con resistencia y no es necesario.

Hablando de "pull-up", la placa MIST lleva tres pulsadores y dos puentes en placa. Realmente NO SON necesarios para nosotros, peeerooo... si no los llevamos a +3.3 (con resistencias de 4k7, 4 en total), el menú se vuelve inestable. Sin el "pull-up", al entrar al menu con el teclado USB (tecla F12), una de cada tres, se sale del menú sin motivo. Por eso, en la versión final he añadido las cuatro resistencias, a los 4 pines AD de la placa ARM (AD4 al 7). Y de paso, he puesto dos pulsadores y dos puentes. Uno de los pulsadores es MENU (AD6), el otro es programable por el CORE (AD7), y los puentes son AD4 y 5 son para programación de la placa ARM desde su conector o para depuración (por ejemplo, el AD5 puenteado, hace que el core del Atari ST aparezca un contador de direcciónes.)
Sinceramente, solo veo útil el del Menú. El resto, no pondría nada. De hecho , el AD7 "NO" lo tengo conectado (pero si llevado a su R4k7) por que en la placa olimex ARM mia, el pin AD7 está "fuera" del conector, y es necesario soldar un cable aparte, y como no lo uso, no lo he soldado.

En cuanto a alimentación, se puede hacer TODO a 3.3v, pero como ya he comentado, hay teclados USB que no funcionan a 3.3v. Y luego, si lo hacemos todo a 3.3, la placa FPGA se resiente del consumo. Por eso, es mejor como he hecho yo, que es, usar el regulador de 5v a 3.3 de cada módulo. Los módulos de SD y ARM, ambos llevan regulador a 3.3, por eso, los alimento a +5v. cogidos de la FPGA. En cambio, la placa USB-SPI solo funciona a 3.3v, pero cortando la pista indicada, puedo meter 5v. directos al USB, y la placa alimentarla a 3.3v, cogidos del propio ARM (de su salida de 3.3v)

El RESET "no" lo he conectado. En la MIST hay un pulsador RST para todo (FPGA, ARM, MAX3421), pero yo no los uso. Para mi, resetear es "apagar y encender", mas sencillo, menos cables.... (así de simple)

El conector que he preparado para la FPGA, es compatible con la IV y la V (las de "aliexpress", claro).

Y de eso precisamente hablo ahora, de las FPGA: compilar CORES de la MIST para este "invento mio" es ultrasimple.
Coger los fuentes de la MIST, cambiar de FPGA (de cyclone III a cyclone IV o V), cambiar reloj de 27 a 50mhz, y poner estos pines:

CÓDIGO: [SELECCIONAR TODO](http://www.forofpga.es/viewtopic.php?f=141&t=289#)

```
set_global_assignment -name FAMILY "Cyclone IV E
set_global_assignment -name DEVICE EP4CE22F17C8
#
set_location_assignment PIN_E1 -to CLOCK_50
set_location_assignment PIN_M8 -to LED
set_location_assignment PIN_J16 -to botones[0]
set_location_assignment PIN_J15 -to botones[1]
#
# VGA 666 (LA MARCA DEL DIABLO)
set_location_assignment PIN_F8 -to VGA_R[0]
set_location_assignment PIN_G1 -to VGA_R[1]
set_location_assignment PIN_L3 -to VGA_R[2]
set_location_assignment PIN_E6 -to VGA_R[3]
set_location_assignment PIN_G5 -to VGA_R[4]
set_location_assignment PIN_L4 -to VGA_R[5]
#
set_location_assignment PIN_L8 -to VGA_B[0]
set_location_assignment PIN_K5 -to VGA_B[1]
set_location_assignment PIN_L1 -to VGA_B[2]
set_location_assignment PIN_N1 -to VGA_B[3]
set_location_assignment PIN_P1 -to VGA_B[4]
set_location_assignment PIN_R1 -to VGA_B[5]
#
set_location_assignment PIN_L7 -to VGA_G[0]
set_location_assignment PIN_M10 -to VGA_G[1]
set_location_assignment PIN_L2 -to VGA_G[2]
set_location_assignment PIN_N2 -to VGA_G[3]
set_location_assignment PIN_P2 -to VGA_G[4]
set_location_assignment PIN_T2 -to VGA_G[5]
#
set_location_assignment PIN_K1 -to VGA_HS
set_location_assignment PIN_K2 -to VGA_VS
#
# Control de la placa compatible MIST
set_location_assignment PIN_B16 -to SPI_DO
set_location_assignment PIN_C15 -to SPI_DI
set_location_assignment PIN_C16 -to SPI_SCK
set_location_assignment PIN_D15 -to SPI_SS2
set_location_assignment PIN_F15 -to SPI_SS3
set_location_assignment PIN_D16 -to CONF_DATA0
#set_location_assignment PIN_F16 -to SPI_SS4 # no cableado, pero tampoco he visto que se use!!!
#
# SONIDO
set_location_assignment PIN_J2 -to AUDIO_L
set_location_assignment PIN_J1 -to AUDIO_R
#
# SDRAM Unamiga
set_location_assignment PIN_B10 -to SDRAM_A[0]
set_location_assignment PIN_A11 -to SDRAM_A[1]
set_location_assignment PIN_B11 -to SDRAM_A[2]
set_location_assignment PIN_A12 -to SDRAM_A[3]
set_location_assignment PIN_D14 -to SDRAM_A[4]
set_location_assignment PIN_D12 -to SDRAM_A[5]
set_location_assignment PIN_D11 -to SDRAM_A[6]
set_location_assignment PIN_C14 -to SDRAM_A[7]
set_location_assignment PIN_C11 -to SDRAM_A[8]
set_location_assignment PIN_C9 -to SDRAM_A[9]
set_location_assignment PIN_A10 -to SDRAM_A[10]
set_location_assignment PIN_C8 -to SDRAM_A[11]
set_location_assignment PIN_C6 -to SDRAM_A[12]
#
set_location_assignment PIN_A2 -to SDRAM_DQ[0]
set_location_assignment PIN_B3 -to SDRAM_DQ[1]
set_location_assignment PIN_A3 -to SDRAM_DQ[2]
set_location_assignment PIN_B4 -to SDRAM_DQ[3]
set_location_assignment PIN_A4 -to SDRAM_DQ[4]
set_location_assignment PIN_B5 -to SDRAM_DQ[5]
set_location_assignment PIN_A5 -to SDRAM_DQ[6]
set_location_assignment PIN_B6 -to SDRAM_DQ[7]
set_location_assignment PIN_A14 -to SDRAM_DQ[8]
set_location_assignment PIN_B13 -to SDRAM_DQ[9]
set_location_assignment PIN_A13 -to SDRAM_DQ[10]
set_location_assignment PIN_B12 -to SDRAM_DQ[11]
set_location_assignment PIN_D6 -to SDRAM_DQ[12]
set_location_assignment PIN_D5 -to SDRAM_DQ[13]
set_location_assignment PIN_C3 -to SDRAM_DQ[14]
set_location_assignment PIN_D3 -to SDRAM_DQ[15]
#
set_location_assignment PIN_A15 -to SDRAM_CKE
set_location_assignment PIN_B14 -to SDRAM_CLK
set_location_assignment PIN_A7 -to SDRAM_nCAS
set_location_assignment PIN_D8 -to SDRAM_nRAS
set_location_assignment PIN_B7 -to SDRAM_nWE
set_location_assignment PIN_E8 -to SDRAM_nCS
#
set_location_assignment PIN_E9 -to SDRAM_BA[0]
set_location_assignment PIN_D9 -to SDRAM_BA[1]
#
set_location_assignment PIN_A6 -to SDRAM_DQML
set_location_assignment PIN_E11 -to SDRAM_DQMH
```

Aquí indico los de la Cyclone IV del UnAmiga con la "txapela" de Antonio Villena, para tener acceso a la VGA y al sonido. El teclado, lógico, sería por USB, lo mismo que los mandos de juego.

Los pines mas importantes y distintos a lo conocido, son los "compatibles" SPI del MIST


Nota: no se pueden subir cores con el menú, por que no está cableados los pines JTAG de la MIST. Es mas, juraría que no se puede, por que uno de los pines queda oculto bajo la FPGA (si me equivoco, corregidme), pero se podría intentar, mirando el esquema MIST, se pueden llevar los cables del ARM al JTAG, y ver que pasa. (quizás un día me anime yo ha probarlo)


actualización:
comentar, que para "compilar" (sintetizar) un core propio de la MIST, ademas de los pines indicados aquí arriba, y de cambiar el reloj "clock_27" por uno de 50, probablemente, haya que añadir un "reset" a la línea que corresponda dentro del código. En este caso, yo he llamado "botones" a los dos pulsadores de la FPGA. Sería añadir a la línea que sea, esto: " || ~botones[0]" para que ademas de los puntos de "reset" del core, como el del OSD, o el botón físico de la MIST, tengamos el nuestro.
Ejemplo, en el core del "PLUS_TOO" (el del mac128), la línea original es:

CÓDIGO: [SELECCIONAR TODO](http://www.forofpga.es/viewtopic.php?f=141&t=289#)

```
		if(!pll_locked || status[0] || status_reset || buttons[1] ||
			rom_download || (last_mem_config != status_mem)) 
```

Y al añadir "nuestro" reset:

CÓDIGO: [SELECCIONAR TODO](http://www.forofpga.es/viewtopic.php?f=141&t=289#)

```
		if(!pll_locked || status[0] || status_reset || ~botones[0] || buttons[1] ||
			rom_download || (last_mem_config != status_mem)) 
```

Si os fijáis, en el original tenemos un "buttons[1]" que es el boon de reinicio de la MIST. Este botón lo podemos añadir físicamente en nuestra placa, o usar los de la FPGA, que es mas sencillo.


Y creo que está todo explicado....

(bueno, una cosa mas, que los USB se pueden enchufar en "caliente", o sea, puedes arrancar un juego de CPC usando el teclado, y cuando el juego está en marcha, cambiar el teclado por un mando USB, y funciona)





### [Re: Adaptador "casi" compatible con MIST](http://www.forofpga.es/viewtopic.php?f=141&t=289#p1200)

- [Citar](http://www.forofpga.es/posting.php?mode=quote&f=141&p=1200)

[Mensaje ](http://www.forofpga.es/viewtopic.php?p=1200#p1200)por **[jepalza](http://www.forofpga.es/memberlist.php?mode=viewprofile&u=48)** » 04 Oct 2019, 10:38

Programar ATMEL At91SAM7

Primero, poner jumper J1, enchufar arm y esperar 10 segundos.
soltar arm, dejar J1 abierto de nuevo, y enchufar otra vez. nos detecta puerto com nuevo
arrancar sam-ba, cargar el firm "mist-binaries-master/firmware/firmware_190820.bin"
en "scripts" elegir "erase all flash", y pulsar execute, nos pregunta si queremos desbloquear regiones
decimos SI, ahora, pulsamos en send file, se envia, tarda muy poco, y nos pregunta de nuevo
si queremos bloquear regiones, decimos que NO, y listo, ya esta

sam-ba 2.12
https://armbedded.taskit.de/node/8
http://download.armbedded.eu/tools/sam-ba_2.12.exe

mist firm:
https://github.com/mist-devel/mist-binaries

mist instrucciones flash arm:
[https://github.com/mist-devel/mist-boar ... heFirmware](https://github.com/mist-devel/mist-board/wiki/HowToInstallTheFirmware)
[https://github.com/mist-devel/mist-boar ... er-windows](https://github.com/mist-devel/mist-board/wiki/HowToInstallTheFirmware#updating-the-io-controller-via-sam-ba-under-windows)
(buscar seccion SAM-BA, los pasos son correctos al 100% en todo, incluso lo del J1)

Esto que parece sencillo en un principio, me llevo una hora entera, por que el PC no me detectaba la placa, por mas que probé a puentear J1.
Al final, de esas cosas que dices "que conyo ha pasado aquí!!!", tanto enchufa, desenchufa, puentea J1, despuentea J1, en una de esas, lo cogió.
Por lo tanto, no puedo dar fé de que el proceso sea sencillo y directo a la primera. Solo indico, que al final, tras detectarlo el PC, el resot, fue coser y cantar, a la primera, y en menos de un minuto estaba lista. Pero hasta que llegó ese minuto, fue desesperante.!!!!





