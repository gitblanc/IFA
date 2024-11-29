---
title: "P5a: Ejercicios de Práctica Forense IV"
---
>[!Info]
>Los smartphones tienen dos formas de ser **localizados**:
>- Mediante el receptor GPS
>- Al estar registrado en una red móvil

>[!Note]
>Microutilidades en [Nirsoft](https://www.nirsoft.net/)
>- Para usar Nirlauncher

## Aplicación de técnicas de forensia en vivo a una máquina Windows

1. a

![](img/Pasted%20image%2020241129135850.png)

2. b

```cmd
time
```

```powershell
get-date -displayhint time
```

3. c

```cmd
ipconfig/all
```

```powershell
get-netipconfiguration
```

4. d

![](img/Pasted%20image%2020241129130452.png)

![](img/Pasted%20image%2020241129130521.png)

5. e

```cmd
route print
```

```powershell
get-netroute -addressfamily ipv4
```

![](img/Pasted%20image%2020241129130710.png)

6. f

```cmd
arp -a
```

```powershell
get-netneighbor -addressfamily ipv4
```

7. ==TRABAJO AUTÓNOMO==
8. ==TRABAJO AUTÓNOMO==
9. ==TRABAJO AUTÓNOMO==
10. a

>[!Error]
>No se hace este ejercicio

11. a

- En el administrador de tareas click derecho en un proceso y Crear volcado de memor

12. ==TRABAJO AUTÓNOMO==
13. ==TRABAJO AUTÓNOMO==
14. ==TRABAJO AUTÓNOMO==
15. ==TRABAJO AUTÓNOMO==
16. ==TRABAJO AUTÓNOMO==
17. ==TRABAJO AUTÓNOMO==
18. ==TRABAJO AUTÓNOMO==
19. ==TRABAJO AUTÓNOMO==
20. ==TRABAJO AUTÓNOMO==
21. ==TRABAJO AUTÓNOMO==
22. ==TRABAJO AUTÓNOMO==
23. ==TRABAJO AUTÓNOMO==
24. 

> [!Error]
> No se hace

25. ==TRABAJO AUTÓNOMO==
26. ==TRABAJO AUTÓNOMO==

## Análisis de un volcado de memoria RAM y búsqueda de trazas de malware con Volatility

27.  No usaremos belkasoft porque da muchos problemas

28. b

- a. 

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem imageinfo
```

![](img/Pasted%20image%2020241129141311.png)

>[!Note]
>`x86` significa *32 bits*

- b.

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem pstree
```

![](img/Pasted%20image%2020241129141619.png)

- c.

```cmd
.\volatility_2.6_win64_standalone.exe -f windowsRAM.vmem --profile=WinXPSP2x86 connscan
```

![](img/Pasted%20image%2020241129141915.png)

- d.
- e.
- f. 856
- g. 

## Herramientas para investigación de fuentes abiertas (OSINT)

> [!Tip]
> Alternativa web a exiftool: [Exif.tools](https://exif.tools/)

29. ==TRABAJO AUTÓNOMO==
30. a

- a. Moscow Idaho
- b. Estados Unidos
- c. Paradise Valley
- d. Universidad de Idaho

31. a

- Es falso, la primera vez que apareció la imagen fue en 2016

32. a

![](img/Pasted%20image%2020241129161111.png)

- a. Sama, España
- b. 80, 443
- c. HTTP y HTTPs
- d. IIS Windows Server 10.0
- ...

33. a

- a. ![](img/Pasted%20image%2020241129154651.png)
- b. Oviedo, España
- c. Servidor dedicado
- d. Han habido 4 ips asociadas a este dominio
- e. 6

![](img/Pasted%20image%2020241129155417.png)

![](img/Pasted%20image%2020241129155605.png)

- f. 

![](img/Pasted%20image%2020241129160111.png)

- g. 

![](img/Pasted%20image%2020241129160331.png)

![](img/Pasted%20image%2020241129160347.png)

- h. Sí

![](img/Pasted%20image%2020241129160637.png)

34. a

![](img/Pasted%20image%2020241129133215.png)

35. a

![](img/Pasted%20image%2020241129133927.png)

36. a
37. a
38. a
39. a

## Análisis de cabeceras de email

>[!Note]
>Herramientas útiles
>- [MHA](https://mha.azurewebsites.net)
>- [mxtoolbox](https://mxtoolbox.com/public/tools/emailheaders.aspx)
>- [centralops](https://centralops.net/co/)
>- [viewdns](https://viewdns.info/)
>- [domaintools](https://research.domaintools.com/)

40. a

![](img/Pasted%20image%2020241129151303.png)

- a. 

![](img/Pasted%20image%2020241129151627.png)

- b. Usar **viewdns.info**.

![](img/Pasted%20image%2020241129151917.png)

![](img/Pasted%20image%2020241129151955.png)

Vodafone

- c. curipacha.conejo@estud

![](img/Pasted%20image%2020241129152052.png)

- d. No está comprometida
- e. No se conoce la dirección
- f. Pertenece a la Universidad de San Francisco de Quito, Ecuador

![](img/Pasted%20image%2020241129152315.png)

- g. Hay 11 saltos en la primera tabla. Por tanto, el primer MTA es este:

![](img/Pasted%20image%2020241129152608.png)

- h. Por 11 (número de saltos = número de MTAs)
- i. 

![](img/Pasted%20image%2020241129152921.png)

- j. 

![](img/Pasted%20image%2020241129153001.png)

- k. Tardó 1 minuto y 32 segs (incluyendo los retardos de cada salto)
- l. En principio no, porque:
	- los retardos son no negativos
	- el timestamp es siempre creciente
- m. Mirar la herramienta MXtoolbox. No la cumple:

![](img/Pasted%20image%2020241129153329.png)

41. b

- a. 69.46.5.138
- b. HighVelocity.net
- c. matt@cgrat.com
- d. Sí

![](img/Pasted%20image%2020241129154130.png)

- e. Sí
- ...
- n. Que estará siendo usada para fines de terceros

42. ==TRABAJO AUTÓNOMO==
43. ==TRABAJO AUTÓNOMO==

## Introducción a la forensia de redes con Wireshark y análisis de movilidad en redes de telefonía móvil

44. a

- a.
- b.
- c.
- d.
- e.
- f. Firefox

![](img/Pasted%20image%2020241122132041.png)

- g. Windows NT 5.1 (*aka* Windows XP)
- h. SUSE

![](img/Pasted%20image%2020241122132244.png)

- i.
- j. Un Hola Mundo!

![](img/Pasted%20image%2020241122132456.png)

- k. 1122
- l. 80
- m. 156.35.151.4 (servidor, pública) 192.168.2.14 (privada)
- n. 
- o.
- p. 00:23:69:29:ba:11 (router) - 00:15:f2:06:07:58 (cliente) - La del servidor no se puede saber (la MAC sólo tiene sentido en una LAN)
- q.

45. ==TRABAJO AUTÓNOMO==

46. a

- a. España, Telefónica (es el mismo imsi-> 214: España, 07: Movistar, 222222222 -> número de teléfono, MSIN)
- b. No (No cambió el CARRIER (MCC -> 214, ni el NET -> 7), aunque sí el área)
- c. Multisim, porque los dos icc coexisten en el tiempo y el IMEI cambia
	- hay 2 icc (aunque el hecho de tener 2 icc no es una garantía de ser multisim)
- d. Hay 3 uplas distinguibles (3 imeis, 2 icc, 1 imsi)
- e. No, porque es UMTS, es 3G (mirar el radio)
- f. Sí porque hay 2 imeis para la misma icc
- g. Porque hay un pequeño movimiento pero está en la misma célula
- h. Castalla, Alicante
- i. Castalla (Alicante), Ibiza, Castalla (Alicante) 
- j. 24/07/2019 09:15:23 (GMT+2, no 11)
- k. Por el Multisim (Ibiza y Alicante)

47. a
- a. 214, España - 01, Vodafone (03, Orange) - 222333444, teléfono
- b. **Portabilidad numérica** (ha cambiado de compañía), porque comparte el mismo imei en dos imsi distintos
- c. Samsung Galaxy A50
- d. Son 3: 2136, 2118, 2630
- e. Hay cambios: UTC+1 (Canarias) y UTC+2 (todos los registros son en verano)
- ==FALTA EL RESTO==


> [!Note]
> Descargar:
> - CAINE 11 ISO
> - windowsram.zip
> - imaenesp5.zip
> - ficherosp5.zip
> - Cabecerasmensajesospechoso1-4.txt
> - volatility_2.6_win64_standalone