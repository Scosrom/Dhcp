# dhcp - dhcp failover- dhcp relay

El **Protocolo de Configuración Dinámica de Hosts** (en inglés, Dynamic Host Configuration Protocol o DHCP) es un protocolo de red que permite a los dispositivos obtener automáticamente una dirección IP y otros parámetros de configuración de red, como la máscara de subred, la puerta de enlace predeterminada y los servidores DNS, sin necesidad de configurarlos manualmente.

El **DHCP Failover** (o conmutación por fallo de DHCP) es una característica que proporciona redundancia y alta disponibilidad en un entorno DHCP. Esto significa que si un servidor DHCP falla, otro servidor puede asumir sus funciones sin interrupciones en el servicio.

El **DHCP Relay (Relevo DHCP)** es una función que facilita la distribución de direcciones IP y configuraciones de red en redes que están divididas en múltiples subredes o segmentos separados. En lugar de configurar un servidor DHCP en cada segmento de red, puedes utilizar un DHCP Relay para reenviar las solicitudes de configuración desde las subredes hacia un servidor DHCP centralizado


[Configuracion dhcp con Failover](dhcpFailover.md)

[Configuracion dhcp con dhcp relay](dhcpRelay.md)

[Script para la configuración dhcp](script-dhcp.md)




