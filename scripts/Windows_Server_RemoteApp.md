# Windows Server 2022 — Jump Server y RemoteApp

## Identificación

- Host: `JUMP-SRV-01.redes.test`
- Dominio de laboratorio: `redes.test`
- Sistema operativo: Windows Server 2022, instalado en VMware Workstation.
- Servicios trabajados: Active Directory Domain Services, DNS, IIS, Remote Desktop Services, RD Web Access y cliente HTML5.
- Interfaz Ethernet1 (VMnet3): `10.15.71.130/29`, conectada a VLAN 20 mediante Cloud1 / SW1 Gi0/3.
- Interfaz Ethernet0 (VMnet8 NAT auxiliar): `192.168.53.10/24`, gateway `192.168.53.2`.

## Direccionamiento y retorno del tráfico

Durante las pruebas, Windows elegía inicialmente la interfaz NAT para responder a la red de usuarios. Se ajustó la ruta persistente a VLAN 10 mediante la interfaz de laboratorio.

En CMD de administrador se verificó:

```bat
route print
route -p add 10.15.71.0 mask 255.255.255.128 10.15.71.129 metric 1 if 25
route -p add 10.15.71.136 mask 255.255.255.248 10.15.71.129 metric 1 if 25
```

Las rutas se registraron durante la práctica. El índice de interfaz `25` es propio de la VM utilizada; no debe copiarse a otra máquina sin revisar su tabla de interfaces.

## RD Web y cliente HTML5

- Portal de laboratorio: `https://JUMP-SRV-01.redes.test/RDWeb/webclient/index.html`
- Se mostró la ventana de autenticación y el portal `Work Resources`.
- Se trabajaron los perfiles de usuario normal y privilegiado, con publicaciones de aplicaciones según permisos.
- La disponibilidad de un icono en el portal no prueba por sí sola la autorización de acceso extremo a extremo desde una VPN externa.

## Prueba desde la VLAN 10

Desde `USER-PC-1` se ejecutó:

```sh
wget -T 5 --no-check-certificate -S -O /dev/null https://10.15.71.130/RDWeb/
```

Resultado observado: `HTTP/1.1 302 Found` y posteriormente `HTTP/1.1 200 OK`, con servidor `Microsoft-IIS/10.0`. Esto verifica conectividad HTTPS al Jump Server, no el establecimiento de la VPN.

## Observaciones

La combinación AD DS + DNS + RDS en una sola VM se usó para el laboratorio; no constituye por sí misma una arquitectura recomendada para producción. Las capturas disponibles no permiten certificar todas las asignaciones por usuario. No se publican credenciales ni certificados privados.
