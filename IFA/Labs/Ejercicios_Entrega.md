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


| Nombre       | Tamaño del fichero (Bytes) | Tipo MIME      | Autor        | Género      | Duración (HH:MM:SS) | Tasa Muestreo |
| ------------ | -------------------------- | -------------- | ------------ | ----------- | ------------------- | ------------- |
| f0010020.mp3 | 51698                      | audio/mpeg     | -            | -           | 00:00:16            | -             |
| f0010029.mp3 | 526545                     | audio/mpeg     | Kevin McLeod | Electronica | 00:00:16            | -             |
| f0022057.wav | 4612660                    | audio/vnd.wave | -            | -           | 00:00:26            | -             |
| b0069824.wma | 715776                     | audio/x-ms-wma | -            | -           | 00:01:05            | -             |
| f0047085.au  | 9243672                    | audio/basic    | -            | -           | 00:00:52            | -             |


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


## 2. Análisis de imagen lógica de smartphone con Aleapp. (apartados: aa, bb, cc, dd, ee, ff, gg, hh, ii, jj)



# Práctica 5a

28. 8 (apartados: i, j, k, l, m, n, o, p, q) (1 punto). Análisis de volcado de memoria principal con Volatility.



34. (imágenes: 2, 4 y 15) (0,5 puntos). Análisis de metadatos EXIF.



35. (ficheros: 3, 6 y 9) (0,5 puntos). Análisis de metadatos de ficheros.


42. (apartados: a, b, c, d, e, f, g, h, i) (1 punto). Análisis de cabeceras de correo electrónico incluyendo recursos OSINT.


45. (apartados: a, b, c, d, e, f, g) (1 punto). Análisis de registros de servicio móvil.

# Práctica 5b

3. (todos los apartados) (1 punto). Análisis de resolución DNS mediante sondeo de interfaz de red en cliente web con Wireshark.