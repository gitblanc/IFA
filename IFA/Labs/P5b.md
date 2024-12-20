---
title: "Práctica 5 bis: Ejercicios de Práctica Forense V"
---
>[!Info]
>Los protocolos de red son un elemento que aparece con asiduidad en cualquier escenario forense. El investigador forense debe, en ocasiones, analizar el tráfico de red intercambiado entre los sistemas implicados para detectar si se ha producido un incidente (intrusión, descarga de malware, etc.).

## Forensia de redes con Wireshark

1. a

![](img/Pasted%20image%2020241213122324.png)

Hay dos redes, porque el router marca una frontera de red, un router hace inferencia entre redes. Si no hay router, no hay varias redes. El switch (S1) crea un escenario de difusión.

Hay 4 hosts, un router y un switch.
- Hay una sola red ip (su presencia la determina la existencia del router)
- Dirección de broadcast de 10.0.0.0/24: 10.0.0.255
- Dirección de broadcast de 172.16.0.0/12: 172.31.255.255

>[!Note]
>*Vamos a examinar tráfico* ***ICMP***

Descargamos `minired.py`, le asignamos permisos y ejecutamos lo siguiente:

```shell
chmod ugo+x minired.py
sudo apt update
sudo apt install mininet -y
```

![](img/Pasted%20image%2020241213124037.png)

Lo ejecutamos:

```shell
sudo ./minired.py
```

![](img/Pasted%20image%2020241213124113.png)

Vamos a abrir dos sesiones xterm: 

```shell
xterm H1
xterm H2
```

![](img/Pasted%20image%2020241213124832.png)

![](img/Pasted%20image%2020241213124911.png)

Ahora vamos a capturar tráfico ping de H1 a H2

Ahora abriremos Wireshark desde H1: `wireshark &` y seleccionamos *H1 eth0*. 

Ejecutamos: `ping -c 5 10.0.0.12`:

![](img/Pasted%20image%2020241213125504.png)

Hay tráfico ARP porque necesita saber la MAC de la máquina destino para empaquetar la petición. El ttl es 64, pero no pasa por el router ya que ambos hosts están dentro de mismo switch.

Si ahora lo hacemos a H4, el ttl será 64 y esta vez sí que pasa por el router:

![](img/Pasted%20image%2020241213130747.png)

2. a

HTTP-TCP

==TRABAJO AUTÓNOMO==

3. a

DNS-UDP

![](img/Pasted%20image%2020241213132747.png)

Mi dirección IPv4 es 10.0.2.15/16

Buscamos `www.nasa.com` y vemos el tráfico DNS en Wireshark:

![](img/Pasted%20image%2020241213133541.png)

Vemos que el servidor dns coincide con el que nos muestra el cmd.

4. a

HTTP-TCP, HTTPS-TCP

![](img/Pasted%20image%2020241213134626.png)

Podemos ver en texto claro las credenciales si capturamos la petición de login:

![](img/Pasted%20image%2020241213134905.png)

5. a

FTP/TCP

==TRABAJO AUTÓNOMO==

6. a

TELNET vs SSH

==TRABAJO AUTÓNOMO==

7. a

Descargar `SQL_Lab.pcap`

- a.
- b.
- c.
- d. 441.8 segs
- e. 30
- f. 10.0.2.4 y 10.0.2.15
- g.
- h. 

![](img/Pasted%20image%2020241213140938.png)

- i. 

![](img/Pasted%20image%2020241213141111.png)

- j.

![](img/Pasted%20image%2020241213141250.png)

- n.

![](img/Pasted%20image%2020241213141504.png)

- p. 

![](img/Pasted%20image%2020241213141700.png)

passwd: `charley`

No podemos tener la certeza de que esa sea la password, ya que puede haber otra cadena que comparta el mismo hash.

![](img/Pasted%20image%2020241213140355.png)

8. a

Descargar `nimda.captura.pcap`

Los tres primeros paquetes son el handshake TCP:

![](img/Pasted%20image%2020241213142118.png)

![](img/Pasted%20image%2020241213142326.png)

![](img/Pasted%20image%2020241213142513.png)