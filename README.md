El **Protocolo de Configuración Dinámica de Hosts** (en inglés, Dynamic Host Configuration Protocol o DHCP) es un protocolo de red que permite a los dispositivos obtener automáticamente una dirección IP y otros parámetros de configuración de red, como la máscara de subred, la puerta de enlace predeterminada y los servidores DNS, sin necesidad de configurarlos manualmente.

El **DHCP Failover** (o conmutación por fallo de DHCP) es una característica que proporciona redundancia y alta disponibilidad en un entorno DHCP. Esto significa que si un servidor DHCP falla, otro servidor puede asumir sus funciones sin interrupciones en el servicio.

Existen diferentes formas de implementar la conmutación por fallo en DHCP:

**1. Hot Standby:** En este modo, un servidor DHCP está activo y el otro está en espera (standby). El servidor activo responde a las solicitudes de los clientes mientras que el servidor en espera se mantiene en un estado de inactividad, listo para tomar el control si el servidor activo falla.

**2. Load Balancing:** En este modo, ambos servidores DHCP están activos y distribuyen las solicitudes de los clientes entre ellos. Esto ayuda a distribuir la carga de trabajo y mejora el rendimiento del servicio DHCP.

**3. Round Robin:** Similar al load balancing, pero en lugar de basarse en la carga, los servidores DHCP responden en un ciclo secuencial.

**4. Manual Failover:** En este caso, la conmutación por fallo se lleva a cabo manualmente por un administrador cuando uno de los servidores DHCP falla.

La elección del método de conmutación por fallo depende de los requisitos de disponibilidad y carga de trabajo de la red. Cada método tiene sus propias ventajas y consideraciones. Por ejemplo, el hot standby proporciona una conmutación rápida y automática en caso de fallo, pero requiere un servidor en espera que no esté en uso. Load balancing distribuye la carga, pero requiere una configuración más compleja.

En resumen, DHCP y DHCP Failover son herramientas esenciales en la gestión de direcciones IP en una red, y el failover es una estrategia importante para garantizar la disponibilidad del servicio en caso de fallos.

