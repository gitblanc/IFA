---
title: Ejercicios de la Entrega Final
---

>[!Info]
>Cada ejercicio tiene los siguientes subapartados:
>- **Análisis de la documentación**: en su caso, si fue necesario examinar documentación vinculada a la pregunta/apartado/ejercicio para proceder a su resolución. Indicar qué documentación se examinó/consultó.
>- **Toma de pruebas**: en su caso, cómo se realizó la toma de pruebas justificada con capturas de pantalla, (ej.: imagen forense de una partición de un disco).
>- **Análisis de las pruebas**: a qué procedimientos de análisis se sometieron las pruebas, ej.: búsqueda de ficheros borrados utilizando técnicas de carving, etc.
>- **Resultados obtenidos**: : justificados mediante capturas de pantalla y explicaciones aclaratorias que el alumno@ crea conveniente. Las capturas de pantalla deberán ser claras, visibles (sin necesidad de aumentar el documento al 150%), sin ambigüedades y explicativas de las acciones realizadas, donde quede claro que se realizan bajo la autoría del alumno (por ejemplo, haciendo visible el nombre de la cuenta que utilizó para realizar el ejercicio). Aquellos ejercicios que se realicen mediante línea de comandos, deberán mostrar el prompt donde deberá aparecer OBLIGATORIAMENTE la cuenta con el nombre del alumno.
>- **Conclusiones preliminares**: si las hubiere, que se deduzcan de los análisis practicados (ej.: se encontraron 12 imágenes borradas de tales tipos y tal tamaño).
>- **Conclusiones**: resumen de los resultados obtenidos para cada ejercicio/pregunta/apartado a resolver.


> [!Warning]
> Mi SO utilizado en mi equipo personal es un Debian puro, por lo que todos los ejercicios en los que salgan capturas en el SO Windows serán en una máquina virtual, es decir, los ejercicios donde la máquina host es Windows en mi caso es una máquina virtual.

# Práctica 2

## 21. Adquisición de evidencia en red incluyendo integridad hash con Bash.

Para este ejercicio hay que ceñirse al siguiente diagrama, donde el equipo de la izquierda será nuestro Ubuntu Desktop y el de la derecha será Caine 13.

![](img/Pasted%20image%2020241220162023.png)

Seguiremos los siguientes pasos para la preparación del entorno:

- El dispositivo que se usará será el siguiente USB 2.0 genérico de 2GB:

![](img/Pasted%20image%2020241220173107.png)

- Tenemos que configurar previamente las máquinas de Caine y Ubuntu con el adaptador de red en puente (Bridge)

![](img/Pasted%20image%2020241220162302.png)

![Ubuntu Desktop](img/Pasted%20image%2020241220162337.png)

- Ahora añadiremos un filtro para la unidad USB en la máquina de Ubuntu Desktop:

![Ubuntu Desktop](img/Pasted%20image%2020241220162442.png)

- Ahora arrancaremos las dos máquinas y comprobaremos que se pueden comunicar mediante peticiones `ping`:
	- Comprobaremos las ips de ambos equipos con el comando `ip addr | grep inet`

![Caine 13](img/Pasted%20image%2020241220162921.png)

![Ubuntu Desktop](img/Pasted%20image%2020241220162951.png)

Vemos las siguientes direcciones de red:
- **Caine 13**: 192.168.1.59
- **Ubuntu Desktop**: 192.168.1.57

Procedemos a realizar la comunicación bidireccional:

![Comunicación de Caine a Ubuntu](img/Pasted%20image%2020241220163232.png)

![Comunicación de Ubuntu a Caine](img/Pasted%20image%2020241220163259.png)

Por tanto, queda confirmado que ambos equipos **pueden comunicarse**.

- Comprobamos que el USB se haya montado correctamente con `lsblk`:

![](img/Pasted%20image%2020241220163737.png)

Aparece como *sdb* y tiene dos particiones.

- Ahora como `root`, primero realizaremos un hash del dispositivo del cuál haremos la imagen (usaremos SHA1):

```shell
# Previamente habiendo hecho un: sudo su
sha1sum /deb/sdb > sha1sum_dispositivo.txt & 
```

![](img/Pasted%20image%2020241220165146.png)

- Ahora, en nuestra máquina Caine ejecutaremos este otro comando (como root):

```shell
nc -l -p 666 > ./imagen_remota_dispositivo &
```

![](img/Pasted%20image%2020241220165638.png)

- Entonces, en nuestra máquina Ubuntu ejecutaremos el siguiente comando (como root):

```shell
dd if=/dev/sdb | nc 192.158.1.59 666 &
```

![](img/Pasted%20image%2020241220165607.png)

- Vemos que se creará la imagen del dispositivo de Ubuntu Desktop en nuestra máquina Caine:

![](img/Pasted%20image%2020241220165918.png)

- Por último, realizaremos un hash (utilizando SHA1) de la imagen en Caine para comprobar que coincide con el hash del dispositivo realizado en Ubuntu Desktop (también como root):

```shell
sha1sum imagen_remota_dispositivo > sha1sum_imagen_dispositivo.txt &
```

![](img/Pasted%20image%2020241220170158.png)

Podemos contrastar ambos hashes y comprobar que son idénticos:
- En **Caine**: `4e1a409b4886f091a58c2506736a2240c5c226ec`
- En **Ubuntu**: `4e1a409b4886f091a58c2506736a2240c5c226ec`

Utilizando una herramienta como [Text Compare](https://text-compare.com/):

![](img/Pasted%20image%2020241220170858.png)

Por tanto, esto nos asegura que no se ha roto la cadena de custodia y por tanto dicha imagen pertenece a dicho dispositivo, y no ha sufrido ningún cambio.

## 35. Análisis de evidencia textual con Bash.

- Primero descargamos el fichero "*Recursos de Prácticas-> Práctica 2-> logs.v3.tar.gz*" tal y como indica el enunciado y lo descomprimimos con el comando `tar -xvzf logs.v3.tar.gz`:

![](img/Pasted%20image%2020241220171410.png)

- Para buscar todas las entradas correspondientes al 13 de Noviembre usaremos el siguiente comando:

```shell
cat messages* | grep ^"Nov[ ]*13"
# Otra alternativa sería usar: cat messages* | grep ^"Nov 13"
```

![](img/Pasted%20image%2020241220171707.png)

![](img/Pasted%20image%2020241220171735.png)

## 36. Análisis de evidencia textual con Bash.

- Para buscar todas las entradas en las que aparezca "Did not receive identification string from" y mostrar para cada una de ellas el mes, día, hora e IP, ejecutaremos el siguiente comando:
	- *El `$NF` es para sacar el elemento de la última columna*

```shell
tac messages* | grep "Did not receive identification string from" | awk '{print $1" "$2" "$3" "$NF}'
```

![](img/Pasted%20image%2020241220172859.png)

## 37. Análisis de evidencia textual con Bash.

- Primero descargaremos la imagen que nos indica el ejercicio en "*Recursos de Prácticas-> Práctica 2->minimagen.dd*"
- Este ejercicio es una simulación del módulo de Autopsy `KeywordSearch`
- Creamos la wordlist `busqueda.txt`:

```txt
50,000
virus
VIRUS
Virus
ransom
RANSOM
Ransom
```

- Ahora ejecutaremos el siguiente comando para buscar coincidencias dentro de la imagen:

```shell
tr '[:cntrl:]' '\n' < miniimagen.dd | grep -abif busqueda.txt > resultado.txt
```

![](img/Pasted%20image%2020241220174859.png)

- Como podemos apreciar, hay tres coincidencias:
	- La primera hace referencia a lo que podría ser un enlace sobre información del virus que se ha ejecutado o quizás esté relacionada con el rescate
	- La segunda y la tercera son amenazas e instrucciones por parte de un supuesto ciberdelicuente o grupo cibercriminal.

# Práctica 3

## 1. Carving de imagen JPG con Bash.

 En este ejercicio se pretende realizar un Carving de forma manual. Se realizará sobre la máquina de Caine 13.

- Primero descargaremos el fichero "*L0_Graphic.dd.bz2*" y lo descomprimiremos con el comando `bunzip2 –f L0_Graphic.dd.bz2`
- Después, buscaremos utilizando **xxd** los fichero JFIF (imágenes) dentro de la imagen:

```shell
xxd L0_Graphic.dd | grep JFIF
```

![](img/Pasted%20image%2020241220175842.png)

- Anotamos el offset del comienzo de la línea donde aparece dicho patrón (`0x01bc8c00`) ejecutando el siguiente comando:

```shell
echo $((0x01bc8c00))
29133824
```

![](img/Pasted%20image%2020241220180042.png)

- Ahora, una vez tenemos localizado el comienzo de la imagen, buscaremos a partir de ese el punto final de la misma (patrón `ffd9` -> magic number correspondiente a imágenes). Para ello, ejecutaremos el siguiente comando:

```shell
xxd -s $(echo $((0x01bc8c00))) L0_Graphic.dd | grep ffd9 | more
```

![](img/Pasted%20image%2020241220180524.png)

- Tenemos esta línea: `01bd7920: f7ce 7185 fd6a ac88 d7a1 ffd9 0000 0000 ..q..j.........`. Podemos calcular el offset de forma que cada dos números es un byte. Por tanto, su offset será `0x1bd7930`
- Ejecutaremos el siguiente comando para recuperar la imagen:

```shell
dd if=L0_Graphic.dd of=imagen_extraida.jpg bs=1 skip=$((0x01bc8c00)) count=$((0x01bd7920 - 0x01bc8c00 + 1))
```

![](img/Pasted%20image%2020241220182735.png)

- Podemos ver como claramente hemos conseguidpo recuperar una imagen mediante carving manual

## 10. Carving de archivos de audio con Autopsy.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- Primero descargaremos el fichero "*Recursos Prácticas -> Práctica3 -> L2_Audio.dd.bz2*"
- Se creará un nuevo caso en **Autopsy** (con Data Source de tipo Disk Image or VM File) con los siguientes módulos de ingestión:
	- **File Type Identification**, **Exif parser** (con la opción *Keep corrupted files* activada -> Ahora se llama **Picture Analyzer**) y **PhotorecCarver**

![](img/Pasted%20image%2020241220184453.png)

- Se indica la información requerida de cada fichero encontrado:

![](img/Pasted%20image%2020241220184650.png)

>[!Note]
>Algunos metadatos se obtienen mediante las propiedades de Windows:
>
>![](img/Pasted%20image%2020241220185809.png)


>[!Note]
>La Tasa de muestreo se ha obtenido con **Exiftool**

| Nombre       | Tamaño del fichero (Bytes) | Tipo MIME      | Autor        | Género      | Duración (HH:MM:SS) | Tasa Muestreo |
| ------------ | -------------------------- | -------------- | ------------ | ----------- | ------------------- | ------------- |
| f0010020.mp3 | 51698                      | audio/mpeg     | -            | -           | 00:00:16            | 44,1 kHz      |
| f0010029.mp3 | 526545                     | audio/mpeg     | Kevin McLeod | Electronica | 00:00:16            | 44,1 kHz      |
| f0022057.wav | 4612660                    | audio/vnd.wave | -            | -           | 00:00:26            | 44,1 kHz      |
| b0069824.wma | 715776                     | audio/x-ms-wma | -            | -           | 00:01:05            | -             |
| f0047085.au  | 9243672                    | audio/basic    | -            | -           | 00:00:52            | 44,1 kHz      |


## 14. Recuperación de evidencias borradas mediante metadatos con Autopsy.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- Primero descargaremos el recurso "*Recursos Prácticas -> Práctica 3 -> dfr-03-
mugt.dd.bz2*"
- Se creará un nuevo caso en **Autopsy** (con Data Source de tipo Disk Image or VM File) con los siguientes módulos de ingestión:
	- **File Type Identification** y **PhotorecCarver**

![](img/Pasted%20image%2020241220191523.png)

![](img/Pasted%20image%2020241220191609.png)

- Se cumplimenta la tabla solicitada:


| Número de partición | Sector de Comienzo | Sector de Finalización | Tipo Sistema de Ficheros |
| ------------------- | ------------------ | ---------------------- | ------------------------ |
| vol1                | 0                  | 127                    | Unallocated              |
| vol2                | 128                | 2091135                | NTFS/exFAT               |
| vol3                | 2091136            | 2097152                | Unallocated              |

- a) ¿Cuantos ficheros de tipo mime text/plain borrados se encuentran en las particiones detectadas en la imagen?
	- Borrado solamente se encuentra 1 (Bunda.txt), aunque hay 5 que comparten dicho tipo MIME

![](img/Pasted%20image%2020241220192053.png)

- b) Se procede a rellenar la siguiente información

|           |                |             | MAC times           | por cada            | fichero             | borrado (GMT)       |
| --------- | -------------- | ----------- | ------------------- | ------------------- | ------------------- | ------------------- |
| Nombre    | Tamaño (Bytes) | Partición   | Acceso              | Modificación        | Cambio              | Creación            |
| Bunda.txt | 2107392        | Unallocated | 1999-01-02 08:04:00 | 2000-02-29 18:14:00 | 2011-11-27 01:34:58 | 2011-11-27 02:41:55 |

>[!Tip]
>Importante tener en cuenta que las horas en Autopsy están como CET, así que hay que restar 1 hora a las que aparecen

- c) Por cada fichero de texto plano muestre su línea temporal

![Fichero Bunda.txt](img/Pasted%20image%2020241220193537.png)

# Práctica 4

## 8. Análisis de imagen física de smartphone con Autopsy. (apartados: (bb, vv, xx, ccc, fff, iii, mmm, nnn, ppp, rrr, sss) (1,25 puntos).

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian


- **Perfil de ingestión JTAG Samsung:**
	- Será incremental, por etapas
	- Data Source Type: Disk Image or VM File
	- Ingest Profile (etapas): 
		1. File Type Identification, Extension Mismatch Detection (solamente marcar check all the types), PhotoRec Carver marcar keep corrupted files ==(2-5mins)==
		2. Picture Analyzer, Keyword Search (numbers, ip addresses, email addresses, url, credit cards), Email Parser ==(45 mins)==
		3. Recent Activity, Android Analyzer ==(segs)==

>[!Error]
>La ingestión se realizó al completo pero uno de los módulos duplicó algún contenido

> [!Note]
> Para este ejercicio se usará la ingestión realizada para la clase presencial, no se hará una nueva debido al tiempo de realización que conlleva volver a hacer la ingestión.

- bb) Abra el archivo de mensajes con un visor de SQLite y compruebe si la tabla sms del fichero mmssms.db tiene los mismos registros que los mostrados por Autopsy.

> [!Note]
> Para ver el contenido de la base de datos se usará el visor SQL web [sqliteviewer](https://sqliteviewer.app)

![Tabla sms en sqliteviewer](img/Pasted%20image%2020241220200543.png)

![Tabla sms en Autopsy](img/Pasted%20image%2020241220200844.png)

Sí muestran los mismos registros (dado que la ingestión duplicó algunos registros)

- vv) ¿Cuántos ficheros de vídeo de tipo mp4 fueron localizados por la herramienta?

Un total de 10 ficheros con MIME Type mp4:

![](img/Pasted%20image%2020241220201207.png)

- xx) ¿De qué marca es el ordenador portátil que aparece en el vídeo 20181114_163729.mp4?

El equipo es de la marca DELL:

![](img/Pasted%20image%2020241220201351.png)

- ccc) ¿Cuántos ficheros de tipo jpeg fueron creados, accedidos o modificados en el teléfono entre el 1 de Noviembre de 2018 y el 30 de Noviembre de 2018 inclusive?

Si nos fijamos en el timeline de los objetos cuyo MIME Type es jpeg y configuramos las fechas adecuadamente:

![](img/Pasted%20image%2020241220202312.png)

Hay un total de 602 elementos que fueron creados, accedidos o modificados en el teléfono entre el 1 de Noviembre de 2018 y el 30 de Noviembre de 2018 en ese período temporal:

![](img/Pasted%20image%2020241220203357.png)

- fff) Compruebe si hay algún fichero .jpg en la carpeta de Descargas (userdata/Media/0/Download), y en caso de haber alguno, extráigalo a la carpeta de Export del proyecto.

Sí que hay uno llamado emma-girl.jpg:

![](img/Pasted%20image%2020241220203606.png)

Si lo extraemos al explorador de archivos:

![](img/Pasted%20image%2020241220203658.png)

- iii) Enumere las redes sociales con que trabajaba el usuario.

>[!Note]
>La carpeta típica donde se suelen almacenar las redes sociales es en "userdata/data/"

Facebook:

![](img/Pasted%20image%2020241220203938.png)

![](img/Pasted%20image%2020241220204116.png)

Instagram:

![](img/Pasted%20image%2020241220204210.png)

Pinterest:

![](img/Pasted%20image%2020241220204231.png)

Twitter:

![](img/Pasted%20image%2020241220205053.png)

Por tanto, las redes sociales encontradas instaladas son Facebook, Instagram, Pinterest y Twitter.

- mmm) Localice el fichero accounts.db. Indique en qué carpeta se encuentra. Almacene dicho fichero en la carpeta Export. Abra dicho fichero con DB Browser for SQLite. ¿Cuántas cuentas hay asociadas con el dispositivo?

La base de datos se encuentra dentro de `/img_JTAGsamsungS4.bin/vol_vol32/system/users/0/accounts.db`

![](img/Pasted%20image%2020241220204525.png)

>[!Note]
>Se va a utilizar otro visor de SQLite (**sqliteviewer**) porque el indicado me hacía crashear la máquina virtual

Hay 4 cuentas asociadas:

![](img/Pasted%20image%2020241220205007.png)

- nnn) Según la información obtenida en el apartado mmm), ¿cuál es la cuenta
de Google?

La cuenta usada es `cfttmobile1@gmail.com`

- ppp) Obtenga información sobre los contactos de Facebook almacenados por dicha aplicación. ¿Cuántos contactos hay? ¿Cuáles son sus nombres?

Nos dirigiremos a la carpeta `/img_JTAGsamsungS4.bin/vol_vol32/data/com.facebook.katana/databases` y encontraremos `contacts_db2`:

![](img/Pasted%20image%2020241220205458.png)

Nos descargaremos la base de datos y la abriremos de nuevo en **sqliteviewer**:

![](img/Pasted%20image%2020241220205720.png)

Vemos que hay dos contactos: Jane Smith y John Smith.

- rrr) Averigüe el mes y el día de nacimiento del primero de los contactos de Facebook almacenados en el móvil intervenido.

Nació el 23 de Abril:

![](img/Pasted%20image%2020241220210249.png)

- sss) Averigüe el mes y el día de nacimiento del último de los contactos de Facebook almacenados en el móvil intervenido.

Nació el 12 de Diciembre como se ve en la imagen del apartado anterior.

## 9. Análisis de imagen lógica de smartphone con Aleapp.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

> [!Note]
> Para este ejercicio se usará la ingestión realizada para la clase presencial, no se hará una nueva debido al tiempo de realización que conlleva volver a hacer la ingestión con ALEAPP.

- Se usará el siguiente recurso: "*Recursos Prácticas->Práctica 4 -> Pixel3-
Data.tar*", que se corresponde con un tar de la carpeta data de un móvil
modelo Google Pixel 3.

- aa) ¿Cuántos marcadores (bookmarks) tiene registrados Chrome?

Solamente tiene uno: `https://www.cfreds.nist.gov`

![](img/Pasted%20image%2020241220212646.png)

- bb) ¿A qué página/s corresponden?

Se corresponde con la página del NIST concretamente: *Computer Forensic Reference DataSet Portal*

![](img/Pasted%20image%2020241220213212.png)

- cc) ¿Qué expresiones o cadenas de búsqueda se han empleado en la app de Chrome?

Se ha empleado únicamente "Cult of Mac":

![](img/Pasted%20image%2020241220213448.png)

- dd) ¿Cuántas veces se ha buscado la expresión "Cult of Mac"?

Solamente una vez, como se aprecia en la captura anterior.

- ee) ¿Cuántas entradas tiene la lista de sitios más visitados desde la app de Chrome?

Tiene dos entradas:

![](img/Pasted%20image%2020241220213730.png)

- ff) ¿Cuáles fueron los sitios más visitados?

Los sitios más visitados fueron: `https://www.espn.com/` y `https://www.starwars.com`

- gg) ¿Cuántas entradas tiene el historial de sitios visitados en la app de Chrome?

Tiene un total de 30 entradas:

![](img/Pasted%20image%2020241220213911.png)

- hh) ¿Cuál es la URL más reciente visitada desde la app de Chrome?

La más reciente, si las filtramos por orden descendente es `https://en.m.wikipedia.org/wiki/The_Mandalorian`:

![](img/Pasted%20image%2020241220214229.png)

- ii) ¿Qué URL fue la más visitada desde la app de Chrome?

La web más visitada fue: `https://www.cultofmac.com/`:

![](img/Pasted%20image%2020241220214441.png)

- jj) En qué fecha/hora se realizó la última visita a la URL `https://www.starwars.com/` desde la app de Chrome.

Al subdirectorio `/news/star-wars-squadron-is-here` se accedió en 2020-10-04 14:14:15 mientras que a la web como tal `https://www.starwars.com/` se accedió en 2020-10-04 14:13:41

# Práctica 5a

## 28. (apartados: i, j, k, l, m, n, o, p, q) (1 punto). Análisis de volcado de memoria principal con Volatility.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- i) Suponga que alguna de las IPs detectadas en el apartado d) se encuentra en una blacklist. Si el equipo está infectado por un Troyano, es normal que éste haya añadido una clave al registro de Windows para asegurarse de que se ejecutará en cada reinicio del sistema. Busque información sobre el comando printkey y aplíquelo para buscar información sobre la clave del registro "Microsoft\Windows NT\CurrentVersion\Winlogon"

Se usará el siguiente comando:

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 printkeys -K "Microsoft\Windows NT\CurrentVersion\Winlogon"
```

![](img/Pasted%20image%2020241220225351.png)

![](img/Pasted%20image%2020241220225605.png)

Podemos apreciar los programas `userinit.exe` y `sdra64.exe`

- j) Si ha realizado con éxito el apartado anterior, fíjese en el segundo valor de la subclave UserInit. Busque en internet información sobre ese ejecutable.

El archivo sdra64.exe un componente del software de _desconocido_ propiedad de _desconocido_.

**sdra64** son las siglas de Trojan.Zbot

La mayoría de los programas antivirus identifican sdra64.exe como malware, como Microsoft lo identifica como un _VirTool:Win32/VBInject.gen!FU_, y Kaspersky lo identifica como un _Trojan.Win32.Scar.dowx_.

Más información en [file.net](https://www.file.net/process/sdra64.exe.html)

- k) Haga un volcado de los procesos que detectó en el apartado f) que no se correspondan con navegadores web. Utilice para ello el comando de volatility que permite extraer no solo el contenido de la memoria sino cualquier contenido en disco asociado a dicho proceso.

Podemos sacar los procesos ejecutando el siguiente comando:


```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 connscan
```

![](img/Pasted%20image%2020241129141915.png)

Vemos que hay dos procesos. Ejecutaremos el siguiente comando para extraer contenido de memoria y disco asociado a dicho proceso:

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 dumpfiles -p 856 -D .\
```

![](img/Pasted%20image%2020241220230529.png)

![](img/Pasted%20image%2020241220230552.png)

- l) Obtenga la firma hash de el/los fichero/s donde ha almacenado el volcado de/los proceso/s. Utilice para ello HashMyFiles que puede encontrar en la subcarpeta Nirsoft del CD de Caine.

![](img/Pasted%20image%2020241220231137.png)

- m) Compruebe en la página Web de VirusTotal (https://www.virustotal.com/gui/home/search) si se reconoce la firma hash del/los fichero/s volcados como software malicioso.

![](img/Pasted%20image%2020241220231828.png)

![](img/Pasted%20image%2020241220231917.png)

![](img/Pasted%20image%2020241220231957.png)

![](img/Pasted%20image%2020241220232127.png)

![](img/Pasted%20image%2020241220232235.png)

Hay varios de ellos que contienen Malware.

- n) Los mutex son variables de exclusión mútua que se utilizan para serializar el acceso a una sección crítica en programación concurrente. Hay software malicioso que crea mutex con nombre para asegurarse que una sola instancia del programa malicioso se está ejecutando en el sistema. Utilice el comando mutant de Volatility para obtener todos los objetos KMUTANT pertenecientes a mutex con nombre.

Ejecutaremos el siguiente comando:

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 mutantscan
```

![](img/Pasted%20image%2020241220232642.png)

Se han encontrado los siguientes mutex: ZonesLockedCache, ZonesCounterMutex, ZonesCacheCounterMutex, SingleSesMutex, TpVcW32ListMutex, ShimCacheMutex, ExplorerisShellMutex, InteraciveLogonMutex, VMwareGuestCopyPasteMutex, WindowsUpdateTracingMutex, WininetStartupMutex y VMwareGuestDnDDataMutex

- o) Observe la lista de mutex con nombre que aparecen en la salida del comando de la opción anterior. Muestre solamente las líneas en las que aparece la palabra AVIRA.

Se ejecutará el siguiente comando:

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 mutantscan | findstr /i "AVIRA"
```

![](img/Pasted%20image%2020241220233320.png)

- p) Busque en internet información sobre aquellas cadenas en las que figure AVIRA como subcadenas. ¿De qué tipo de software malicioso se trata?

*Información extraída de [avira.com](https://www.avira.com/en/blog/the-claws-of-evilcode-gauntlet-xworm-rat)*

Los **mutex** (objetos de sincronización en sistemas operativos) son utilizados por diversos tipos de software malicioso para garantizar que solo una instancia del malware se ejecute en el sistema. Algunos de estos mutex contienen la subcadena "AVIRA". Sin embargo, la presencia de "AVIRA" en el nombre del mutex no implica necesariamente una relación directa con el software de seguridad Avira.

Por ejemplo, el ransomware **Money Message** crea un mutex único al iniciarse para evitar que otras instancias del malware se ejecuten simultáneamente. Este comportamiento es común en muchos tipos de malware, incluyendo troyanos de acceso remoto (RATs) como **XWorm**, que se propaga a través de correos electrónicos de spam y puede infectar sistemas con ransomware u otras amenazas.

- q) Compruebe si el FireWall está deshabilitado ya que o bien lo tenía deshabilitado el usuario o bien fue deshabilitado por un software malicioso. Para ello compruebe el valor de la clave de registro "ControlSet001\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile". ¿Estaba el FireWall de Windows deshabilitado?

Ejecutaremos el siguiente comando:

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 printkeys -K "ControlSet001\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile"
```

![](img/Pasted%20image%2020241220234007.png)

Como hay un 0, el Firewall estaba deshabilitado.


## 34. (imágenes: 2, 4 y 15). Análisis de metadatos EXIF.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- Primero descargaremos el fichero *imagenesP5.zip* del Campus Virtual.
- Se van a utilizar las herramientas online **exif.tools** (ya que la versión en local me hace crashear la máquina virtual) y **metadatatogo.com**

**Imagen 2**:
- Fecha en la que fue tomada la imagen: 2024:07:30 09:19:20 +02:00
- Ubicación. En caso de que dicha información no esté presente en los metadatos, trate de averiguarla a través de la búsqueda inversa de imágenes:  las coordenadas no están disponibles, por lo que se usará la extensión de google Reverse Image Search para buscar en distintos navegadores como Yandex o Google Lens. Se corresponde con el Palacio Ducal de Venecia, geolocalizado en 45.43383, 12.34039:

![](img/Pasted%20image%2020241221105307.png)

![](img/Pasted%20image%2020241221105350.png)

- Marca de la cámara: Samsung
- Modelo de la cámara: SM-M135F
- Modelo del teléfono en caso de haberse realizado con un Smartphone: No se indica, pero si en Google Buscamos el teléfono asociado a dicha cámara se corresponde con el Samsung Galaxy A13
	- [Fuente](https://ultimainformatica.com/samsung-galaxy-a13-sm-a135f-ds-168-cm-66-android-12-4g-usb-tipo-c-3-gb-32-gb-5000-mah-negro.html?utm_source=chatgpt.com)
- Año de lanzamiento del teléfono: 2022
- Características de la imagen:
	- Dimensión (ancho x alto) de la imagen en pixels: 4080x3060
	- Resolución: 72 píxeles por pulgada tanto en X como en Y
	- Bits de color por pixel: 8x3=24
- Tamaño del archivo: 3 MiB

![](img/Pasted%20image%2020241221105050.png)

**Imagen 4**:
- Fecha en la que fue tomada la imagen: 2024:08:02 12:36:20 +02:00
- Ubicación. En caso de que dicha información no esté presente en los metadatos, trate de averiguarla a través de la búsqueda inversa de imágenes: las coordenadas no están disponibles, por lo que se usará la extensión de google Reverse Image Search para buscar en distintos navegadores como Yandex o Google Lens. Resulta ser el Palazzo della Ragione, en Verona, concretamente 45.44323, 10.99776:

![](img/Pasted%20image%2020241221103359.png)

![](img/Pasted%20image%2020241221103502.png)

- Marca de la cámara: Samsung
- Modelo de la cámara: SM-M135F
- Modelo del teléfono en caso de haberse realizado con un Smartphone:  No se indica, pero si en Google Buscamos el teléfono asociado a dicha cámara se corresponde con el Samsung Galaxy A13
	- [Fuente](https://ultimainformatica.com/samsung-galaxy-a13-sm-a135f-ds-168-cm-66-android-12-4g-usb-tipo-c-3-gb-32-gb-5000-mah-negro.html?utm_source=chatgpt.com)
- Año de lanzamiento del teléfono: 2022
- Características de la imagen:
	- Dimensión (ancho x alto) de la imagen en pixels: 4080 x 3060
	- Resolución: 72 píxeles por pulgada tanto en X como en Y
	- Bits de color por pixel: 8x3=24 bits
- Tamaño del archivo: 3MiB

![](img/Pasted%20image%2020241221103127.png)

**Imagen 15**:
- Fecha en la que fue tomada la imagen: no aparece la fecha de creación sólo las MAC Times, por lo que vamos a tomar como su fecha de creación la MAC de modificación (la más fiable) 2024:12:21 09:40:49 +00:00
- Ubicación. En caso de que dicha información no esté presente en los metadatos, trate de averiguarla a través de la búsqueda inversa de imágenes: De nuevo tampoco aparece la ubicación, por lo que usaremos navegadores como Google Lens o Yandex para averiguar su geolocalización. Se corresponde con la Librería pública de Brooklyng (Adams Street Library), situada en 40.70449, -73.98828:

![](img/Pasted%20image%2020241221104434.png)

![](img/Pasted%20image%2020241221104729.png)

- Marca de la cámara: -
- Modelo de la cámara: -
- Modelo del teléfono en caso de haberse realizado con un Smartphone: -
- Año de lanzamiento del teléfono: -
- Características de la imagen:
	- Dimensión (ancho x alto) de la imagen en pixels: 719x1600
	- Resolución: 1 píxel por pulgada tanto en X como en Y
	- Bits de color por pixel: 8x3=24
- Tamaño del archivo: 162 KiB

![](img/Pasted%20image%2020241221104114.png)

# 35. (ficheros: 3, 6 y 9). Análisis de metadatos de ficheros.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- Primero descargaremos el fichero *imagenesP5.zip* del Campus Virtual.
- Se va a utilizar la herramienta **metadatatogo.com**

**Fichero 3**:

- Aplicación con la que se creó el archivo: Microsoft Word para Microsoft 365
- Versión de la aplicación con la que se creó el fichero: 3.1-701
- Autor: Rosa Fernández Tiesta
- Empresa/organización donde se crea el documento: -
- Fecha/hora de creación: 2024:01:31 09:55:56 +01:00
- Fecha/hora modificación:  2024:01:31 09:55:56 +01:00
- Fecha/hora modificación metadatos:  2024:01:31 09:55:56 CET
- Número de páginas: 2
- Tamaño del archivo: 183 kB

![](img/Pasted%20image%2020241221110619.png)

**Fichero 6**:

- Aplicación con la que se creó el archivo: microsoft Office PowerPoint
- Versión de la aplicación con la que se creó el fichero: 16
- Autor: Usuario
- Empresa/organización donde se crea el documento: -
- Fecha/hora de creación: 2013:07:03 17:11:32Z
- Fecha/hora modificación: 2018:10:05 14:11:15Z
- Fecha/hora modificación metadatos: 2018:10:05 14:11:15Z
- Número de páginas: 7
- Tamaño del archivo: 126 kB

![](img/Pasted%20image%2020241221111049.png)

**Fichero 9**:

- Aplicación con la que se creó el archivo: Microsoft Office Word
- Versión de la aplicación con la que se creó el fichero: 16
- Autor: BELEN DIEZ GONZALEZ
- Empresa/organización donde se crea el documento: -
- Fecha/hora de creación: 2020:07:06 11:42:00Z
- Fecha/hora modificación: 2024:10:11 08:18:00Z
- Fecha/hora modificación metadatos: 2024:10:11 08:18:00Z
- Número de páginas: 11
- Tamaño del archivo: 13 MB

![](img/Pasted%20image%2020241221111626.png)

## 42. Análisis de cabeceras de correo electrónico incluyendo recursos OSINT.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

> [!Info]
> Se dispone de las siguientes urls para el ejercicio:
> - [https://mha.azurewebsites.net/](https://mha.azurewebsites.net/) y [mxtoolbox](https://mxtoolbox.com/public/tools/emailheaders.aspx) para analizar cabeceras de correo
> - [viewdns](https://viewdns.info/) para averiguar las ips
> - [abuseipdb](https://www.abuseipdb.com) para saber si se trata de un sitio web calificado como malicioso
> - [haveibeenpwned](https://haveibeenpwned.com) para comprobar si una dirección ha sido comprometida

- Primero descargaremos el recurso **CabecerasMensajeSospechoso-2.txt** del Campus Virtual

![](img/Pasted%20image%2020241221112608.png)

- a) ¿Desde qué dirección IP se envió el mensaje?

Desde la 156.35.11.135

![](img/Pasted%20image%2020241221112843.png)

- b) ¿Qué ISP gestiona el rango de IPs en el que está incluida dicha IP?

Lo gestiona RedIris.es

![](img/Pasted%20image%2020241221113026.png)

- c) Averigüe a qué IPs estuvo vinculado el dominio desde el que se envió originalmente el correo.

Analizaremos `dsr.ch`, y vemos que estuvo vinculado a la ip 212.40.14.9 en Suíza.

![](img/Pasted%20image%2020241221114100.png)

- d) ¿Cuántos dominios figuran vinculados a dicha IP en el momento actual?¿Figura el dominio desde el cual se envió el correo entre ellos?

El correo se envió en 2019, por lo que si filtramos en viewdnsinfo nos sale un total de 5 dominios vinculados:

![](img/Pasted%20image%2020241221114449.png)

- e) ¿A qué organización está asociada la IP que hace de hosting del dominio investigado?

Está asociada a ORG-VA3-RIPE (212.40.14.9):

![](img/Pasted%20image%2020241221114550.png)

- f) ¿Dónde está radicada el ISP correspondiente a la red anterior?

Está en St. Alban-Anlage 44, Basilea (Suíza), correspondiente al ISP VTX Datacom AG.

![](img/Pasted%20image%2020241221114837.png)

- g) ¿Quién aparentemente es el remitente del correo?

Se corresponde con `admin@dsrlab.ch`

![](img/Pasted%20image%2020241221115031.png)

- h) ¿Puede haber sido comprometida la dirección de correo que figura como remitente del mensaje?

No podemos asegurar que haya sido comprometida:

![](img/Pasted%20image%2020241221115155.png)

- i) Comprueba si existe la dirección de correo del remitente del mensaje.

Se utilizará la herramienta [email-checker](https://email-checker.net/check):

![](img/Pasted%20image%2020241221115355.png)

No podemos garantizar que la dirección de correo exista.

## 45. Análisis de registros de servicio móvil.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian

- Primero descargaremos el fichero *p5_tr_cell.csv* del Campus Virtual y lo importaremos:

![](img/Pasted%20image%2020241221121326.png)

- a) Identificación única internacional de la línea móvil (país y proveedor del servicio de origen y número de línea).

Todos comparten el mismo **IMSI** -> 214 identifica como país a España, 07 indica como proveedor de servicio a movistar y 099999999 indica el  el número de teléfono (MSIN)

- b) ¿Cuántos terminales móviles distintos aparecen registrados?

Hay dos **IMEI** distintos: `862551036005121` y `862551036005121`, por lo que hay dos teminales móviles diferentes.

- c) Identificación única internacional del terminal o terminales móviles utilizados (modelo, fabricante y si está incluido en la Blacklist de España).

Para ello, se usará la web [imeicheck](https://imeicheck.com).

- Vemos que el IMEI `862551036005121` se corresponde a un Huawei P8 Lite 2017:

![](img/Pasted%20image%2020241221122550.png)

- Comprobamos que no está en una BlackList:

![](img/Pasted%20image%2020241221122844.png)

- Vemos que el IMEI `862551036005121` se corresponde a un Motorola Defy:

![](img/Pasted%20image%2020241221122708.png)

- Comprobamos que no está en una BlackList:

![](img/Pasted%20image%2020241221122758.png)

- d) Indicar el soporte, especificado por el fabricante, en términos de tecnologías de acceso móvil 2G, 3G, 4G y 5G, para el terminal o terminales móviles implicados.

La información relativa al Huawei se obtuvo en [Xataka](https://www.xatakamovil.com/otras/huawei-p8-lite-2017-inesperado-y-mejor-en-todo-que-el-original):
- Soporta **2G** (GSM 850/900/1800/1900 MHz), **3G** (HSPA 850/900/1900/2100 MHz) y **4G** (LTE 1/3/7/8/20).

La información relativa al Motorola se obtuvo en [Xataka](https://www.xataka.com/moviles/motorola-defy-caracteristicas-precio-ficha-tecnica):
- Soporta **2G** (GSM 850/900/1800/1900 MHz),  **3G** (UMTS/HSPA 850/900/1900/2100 MHz) y  **4G** (LTE 1/3/5/7/8/20/28/38/40/41).

- e) Enumerar los países en los que se ha registrado la línea móvil junto con los proveedores de servicio implicados en cada uno de ellos.

Hay 4 **MCC**: 214 (España) con proveedor (columna Net) 1 que corresponde a Movistar y proveedor 7 que corresponde a Orange, 268 (Colombia) con proveedor 1 que corresponde a Claro Colombia, 748 (Chile) con proveedor 7 que corresponde a Entel Chile y 208 (Francia) con proveedor 1 que corresponde a Orange francia y proveedor 23 que corresponde a SFR (Société Française du Radiotéléphone)

- f) Indicar si se ha producido roaming, nacional o internacional, y los países implicados.

Sí se ha producido roaming internacional porque ha cambiado varias veces el CARRIER (MCC y NET) con los mencionados en el apartado anterior.

- g) Indicar el recorrido posicional, en sentido temporal creciente e identificando con la máxima precisión posible dicha localización, que se ha registrado para la línea móvil entre el 23 de junio y el 29 de junio, ambos inclusive y correspondientes al año 2018.

	- **Coordenada 1:**  
	    **Latitud:** 43.356857, **Longitud:** -5.830819  
	    **Ubicación:** se sitúa en el norte de España, cerca de la ciudad de Oviedo, en la región de Asturias.
	- **Coordenada 2:**  
	    **Latitud:** 43.377796, **Longitud:** -5.811434  
	    **Ubicación:** Muy cerca de la anterior, en la misma región de Asturias.
	- **Coordenada 3:**  
	    **Latitud:** 43.374768, **Longitud:** -5.806961  
	    **Ubicación:** Sigue en la región de Asturias, desplazándonos ligeramente dentro de la misma área.
	- **Coordenada 4:**  
	    **Latitud:** 41.236495, **Longitud:** -8.657913  
	    **Ubicación:** Se desplaza hacia el suroeste, cerca de Oporto, en Portugal.
	- **Coordenada 5:**  
	    **Latitud:** 38.724746, **Longitud:** -9.194870  
	    **Ubicación:** Al sur de Lisboa, en Portugal.
	- **Coordenada 6:**  
	    **Latitud:** -34.844724, **Longitud:** -56.191399  
	    **Ubicación:** Ahora en el hemisferio sur, en Uruguay, cerca de la ciudad de Paysandú.
	- **Coordenada 7:**  
	    **Latitud:** -34.854829, **Longitud:** -56.157214  
	    **Ubicación:** Muy cerca de la anterior, en la misma región de Uruguay.
	- **Coordenada 8:**  
	    **Latitud:** -33.376917, **Longitud:** -58.100671  
	    **Ubicación:** En el noreste de Uruguay, cerca de la frontera con Argentina.

# Práctica 5b

## 3. Análisis de resolución DNS mediante sondeo de interfaz de red en cliente web con Wireshark.

>[!Warning]
>Este ejercicio se realiza con una máquina virtual de Windows 10 en un host Debian, ya que en clase en lugar de usar Caine se nos indicó usar Windows

- a) Rellenar información de la tabla proporcionada.

| Descripción                     | Configuración     |
| ------------------------------- | ----------------- |
| Dirección IPv4                  | 10.0.2.15/16      |
| Dirección MAC                   | 08-00-27-4A-FF-73 |
| Dirección IPv4 de la pasarela   | 10.0.2.2          |
| Dirección IPv4 del servidor DNS | 10.0.2.3          |

![](img/Pasted%20image%2020241221130442.png)

- b) En una ventana de terminal introduce wireshark & para iniciar Wireshark. Haga clic en Aceptar para continuar.

Se va a abrir directamente en Windows:

![](img/Pasted%20image%2020241221130947.png)

- c) En la ventana de Wireshark selecciona con doble clic, en el apartado Captura, el interfaz desde el cual va a capturar los paquetes.

Capturaremos la interfaz Ethernet:

![](img/Pasted%20image%2020241221131029.png)

- d) Abre el navegador web y dirígete a www.google.com.

![](img/Pasted%20image%2020241221131206.png)

- e) Haga clic en Stop (Detener) para detener la captura de Wireshark cuando vea la página de inicio de Google.

![](img/Pasted%20image%2020241221131235.png)

- f) En la ventana principal de Wireshark, escriba dns en el campo Filter (Filtro). Haga clic en Apply (Aplicar).

![](img/Pasted%20image%2020241221131306.png)

- g) En el panel de lista de paquetes (sección superior) de la ventana principal, localice el paquete que incluye Standard query (Consulta estándar) y A www.google.com. Observe la trama 19 anterior como ejemplo.

![](img/Pasted%20image%2020241221131423.png)

- h) Los campos del paquete, resaltados en color gris, se muestran en el panel de detalles del paquete (sección media) de la ventana principal. En la primera línea del panel de detalles del paquete, la trama 19 tiene 85 bytes de datos transmitidos (on wire). Esta es la cantidad de bytes que se necesitó para enviar una consulta DNS a un servidor con nombre que está solicitando las direcciones IP de www.google.com. Si utilizaste otra dirección web, la cantidad de bytes podría ser diferente.

En mi caso tiene 73 bytes de datos transmitidos (on wire):

![](img/Pasted%20image%2020241221131546.png)

- i) La línea Ethernet II muestra las direcciones MAC de origen y destino. La dirección MAC de origen proviene de su máquina virtual porque su máquina virtual fue la que originó la consulta DNS. La dirección MAC de destino proviene del gateway predeterminado porque esta es la última parada antes de que esta consulta salga de la red local. ¿Es la dirección MAC de origen la misma que la registrada en la Parte 1 para la VM?

En Wireshark la MAC se corresponde con `08:00:27:4a:ff:73` idéntica a la del apartado.

- j) En la línea del Protocolo de Internet Versión 4 (IPv4), la captura del paquete IP Wireshark indica que la dirección IP de origen de esta consulta de DNS es 192.168.22.25 (en este ejemplo) y la dirección IP de destino es 192.168.22.1 (en este ejemplo). ¿Puede identificar la dirección IP y dirección MAC de origen y destino de este paquete?

| Dispositivo                                 | Dirección IP | Dirección MAC     |
| ------------------------------------------- | ------------ | ----------------- |
| Máquina virtual cliente                     | 10.0.2.15    | 08:00:27:4a:ff:73 |
| Destino servidor DNS/Gateway predeterminado | 10.0.2.3     | 52:55:0a:00:02:03 |

![](img/Pasted%20image%2020241221131546.png)

![](img/Pasted%20image%2020241221132019.png)

- k) El paquete IP y el encabezado encapsulan el segmento de UDP. El segmento de UDP contiene la consulta de DNS como datos. Haga clic en la flecha contigua a User Datagram Protocol para ver los detalles. Observa que solo hay cuatro campos. El número del puerto de origen en este ejemplo es 39303. La MV generó de manera aleatoria el puerto de origen utilizando números de puerto que no están reservados. El puerto de destino es 53. El puerto 53 es un puerto conocido reservado para el uso con DNS. Los servidores DNS esperan en el puerto 53 las consultas de DNS de los clientes.

![](img/Pasted%20image%2020241221132446.png)

![](img/Pasted%20image%2020241221132454.png)

En este ejemplo, la longitud del segmento de UDP es de 51 bytes. La longitud del
segmento UDP de su ejemplo puede ser diferente. De los 51 bytes, 8 bytes se utilizan
como encabezado. Los datos de la consulta de DNS utilizan los otros 43 bytes. Los 43
bytes de los datos de consulta DNS están el panel de bytes del paquete (sección
inferior) de la ventana principal de Wireshark.

![](img/Pasted%20image%2020241221132517.png)

En este ejemplo, la dirección de destino es la del servidor DNS.

En mi caso se corresponde con lo siguiente:

![](img/Pasted%20image%2020241221132421.png)

- l) Haz clic en la flecha que se encuentra a la izquierda de los Flags. Un valor de 1 significa que el flag está definido. Localice el flag que está definido en este paquete.

Está definido el flag de "Recursion desired":

![](img/Pasted%20image%2020241221132641.png)

- m) El checksum es usado para determinar la integridad del encabezado de UDP después de haber atravesado Internet. El encabezado de UDP tiene poca sobrecarga porque UDP no tiene campos que estén asociados con el protocolo de enlace de tres vías en TCP. Cualquier problema de confiabilidad de la transferencia de datos que ocurra debe ser manejado por la capa de aplicación. Expanda lo necesario para ver los detalles. Registre sus resultados de Wireshark en la tabla siguiente:

| Descripción        | Resultado Wireshark |
| ------------------ | ------------------- |
| Tamaño de la trama | 39                  |
| MAC origen         | 08:00:27:4a:ff:73   |
| MAC destino        | 52:55:0a:00:02:03   |
| IP origen          | 10.0.2.15           |
| IP destino         | 10.0.2.3            |
| Puerto origen      | 59155               |
| Puerto destino     | 53                  |

![](img/Pasted%20image%2020241221132421.png)

- n) ¿Es la dirección IP de origen la misma que la dirección IP de la MV que registró en la parte 1?

Sí, ambas direcciones son la `10.0.2.15`.

- o) ¿Es la dirección IP de destino la misma que la puerta de enlace predeterminada (gateway) que observó en la parte 1?

Sí, ambas direcciones son la `10.0.2.3`.

- p) En la trama Ethernet II para la respuesta de DNS, ¿qué dispositivo es la dirección MAC de origen y qué dispositivo es la dirección MAC de destino?

En la trama Ethernet II para la respuesta de DNS, ¿qué dispositivo es la dirección
MAC de origen y qué dispositivo es la dirección MAC de destino?

En mi caso, la MAC de origen es `52:55:0a:00:02:03` (de la puerta de enlace) y la MAC destino es `08:00:27:4a:ff:73` (de mi máquina Windows).

![](img/Pasted%20image%2020241221133230.png)

- q) Observe las direcciones IP de origen y destino en este paquete IP. ¿Cuál es la dirección IP de destino? ¿Cuál es la dirección IP de origen? ¿Qué sucedió con los roles de origen y destino correspondientes a la VM y al gateway predeterminado?

![](img/Pasted%20image%2020241221133431.png)

En ese paquete, la dirección IP de destino es la `192.168.22.25` y la dirección IP de origen es la `192.168.22.1`. En resumen, la dirección IP de origen (`192.168.22.1`) parece corresponder al **gateway predeterminado**, mientras que la dirección IP de destino (`192.168.22.25`) parece corresponder a un dispositivo (probablemente una **VM**) dentro de la misma red. Esto indica que el paquete está siendo enviado desde el gateway hacia la VM o dispositivo de destino.

- r) En el segmento UDP, el rol de los números de puerto también se invirtió. El número del puerto de destino es 39303. El número de puerto 39303 es el mismo puerto que generó la MV cuando se envió la consulta DNS al servidor DNS. La MV espera una respuesta DNS en este puerto. El número del puerto de origen es 53. El servidor DNS espera una consulta de DNS en el puerto 53 y luego envía una respuesta de DNS con un número de puerto de origen 53 al originador de la consulta de DNS. Al expandirse la respuesta de DNS, observa las direcciones IP resueltas para www.google.com en la sección Answers (Respuestas) y captura la pantalla resaltando dicha información.

Vemos que hay una respuesta de `142.250.200.131`

![](img/Pasted%20image%2020241221133852.png)