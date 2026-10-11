# Explicación de la práctica y estado de pruebas

## Resumen técnico

La topología enlaza un Cisco 7200 (ISP simulado) con FortiGate y un switch IOSvL2. El switch transporta VLAN 10/20/30 mediante 802.1Q. El Jump Server Windows está en VLAN 20 y el Web Server Alpine pertenece a VLAN 30. La GUI de FortiGate se administra por una red aparte.

## Qué quedó comprobado

1. **ISP:** `show ip interface brief` mostró Fa0/0 y Fa1/0 arriba, y `show ip route` mostró las rutas estáticas configuradas.
2. **Switch:** `show interfaces trunk` mostró Gi0/0 como `on / 802.1q / trunking`, con las tres VLAN activas y en forwarding. Gi0/1, Gi0/2, Gi0/3 y Gi1/0 figuraban conectadas.
3. **DHCP:** USER-PC-1 mostró `10.15.71.2/25` y ruta por defecto hacia `10.15.71.1`.
4. **Acceso HTTPS:** USER-PC-1 recibió respuestas HTTP 302 y 200 al consultar `https://10.15.71.130/RDWeb/` desde Alpine.
5. **GUI:** se observaron las interfaces, las políticas y la entrada del túnel VPN en FortiGate.
6. **RD Web:** se observó el inicio de sesión y la pantalla de recursos publicados desde Windows Server.

## Qué quedó pendiente

- **Servidor web Alpine:** las pruebas desde Jump a `10.15.71.138` por 443 y 22 mostraron `TcpTestSucceeded : False`. La prueba ARP hacia `10.15.71.137` falló, por lo que no se pudo demostrar la aplicación.
- **VPN IPsec:** la GUI mostró `VPN-JUMP` con estado `Inactive`. No se obtuvo evidencia de negociación con USER-PC-2.
- **Políticas de denegación:** la existencia de reglas y una conexión fallida no bastan para atribuir el fallo a la política. Se requeriría un registro de tráfico o contador inequívoco.
- **Usuarios RemoteApp:** las capturas prueban la existencia del portal; no muestran cada restricción de permisos de forma concluyente.

## Consideraciones de seguridad

- La cuenta y las claves de dispositivos no se publican en GitHub.
- El respaldo completo de FortiGate puede contener secretos y debe conservarse en privado.
- La VPN y los protocolos del asistente se usaron en un entorno aislado y necesitan endurecimiento para producción.
- Las pruebas no demuestran seguridad de extremo a extremo ni disponibilidad del túnel externo.

**Video demostrativo:** https://youtu.be/ZyJbGNLJ6dE
