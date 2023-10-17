
## Configurar red:

**Archivo de Interfaces:**

<code>sudo nano /etc/network/interfaces </code>

Asegúrate de utilizar la dirección IP 192.168.1.x/24 para la red interna y 172.26.x.x para el adaptador puente.

Enp0s3 es mi red interna
Enp0s8 es mi adaptador puente

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/854c6455-7985-4f9f-ae11-8092d1d6ad30)


***Configurar dns:***

**Archivo de DNS:**

<code> nano  /etc/resolv.conf </code>

Puedes usar los servidores DNS de Google:

8.8.8.8
8.8.4.4


![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/c98c049e-54d2-40a6-b9c0-7a4e6d51e916)


<code>sudo systemctl restart networking </code>
<code>sudo systemctl status networking </code>



## Servidor hdcp

<code> sudo apt update </code>
<code> sudo apt install isc-dhcp-server </code>


Edita el archivo de configuración del servidor DHCP:


<code> sudo nano /etc/dhcp/dhcpd.conf </code>

Realiza la siguiente modificación en cada servidor para asignar el principal y secundario:

Address (IP del servidor)
Peer address (IP del otro servidor)

### Configuración en el Servidor Primario:

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/37158cde-4c02-4b6f-ba67-8a9d77c4b170)

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/611af3e4-e304-4364-86ea-9e682b976675)

### Configuración en el Servidor Secundario:

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/50c253cb-92b1-4765-96d8-9afd12f3f2b0)

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/aff22ea6-20d5-45cd-957f-3cdb88b5a0d9)


Edita el archivo de configuración por defecto del servidor DHCP:


<code> sudo nano /etc/default/isc-dhcp-server </code>

Indica la tarjeta de red asignada al DHCP.


![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/2ffd2214-e495-42f9-9d67-741147983b92)

Verifica con systemctl.

El anfitrión tiene una red interna a la que se conecta la máquina del cliente. Asegúrate de configurar los interfaces en VirtualBox. Una vez configurado, abre la máquina.

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/4059dd94-52d9-48f3-b8eb-0a7ad0b420d3)

![image](https://github.com/Scosrom/dhcp-dhcpRelay/assets/114906778/efabce9a-ab17-4503-bca4-4742d0f5ebbc)


## Habilitar el Reenvío de Paquetes:

Abre el archivo de configuración de sysctl:


<code> sudo nano /etc/sysctl.conf </code>

Asegúrate de que la siguiente línea esté descomentada o agrégala si no existe:


<code> net.ipv4.ip_forward=1 </code>

Guarda y cierra el archivo. Luego, aplica los cambios con el siguiente comando:


<code> sudo sysctl -p </code>

**Configurar NAT (Network Address Translation):**

Utiliza iptables para configurar NAT en la interfaz de red que tiene acceso a Internet (puente). Esto permitirá que el tráfico de la red interna se traduzca adecuadamente para salir a Internet.


<code> sudo iptables -t nat -A POSTROUTING -o <interfaz_puente> -j MASQUERADE </code>

<code> sudo iptables-save | sudo tee /etc/iptables/rules.v4 </code>

Asegúrate de reemplazar <interfaz_puente> con el nombre de tu interfaz de red puente (puede ser algo como enp0s3 o similar).

Para hacer que estos cambios sean persistentes después del reinicio, puedes instalar el paquete iptables-persistent:


<code>  sudo apt-get install iptables-persistent </code>


## Configurar el Cliente:
Asegúrate de que el cliente obtenga su configuración de red a través del servidor DHCP y que tenga la puerta de enlace configurada correctamente (que debe ser la interfaz interna del servidor Debian).

**Reinicia los Servicios:**

Reinicia los servicios de red y DHCP:

<code> sudo systemctl restart networking 
sudo systemctl restart isc-dhcp-server </code>



### Error de sintaxis:

Si se presenta un error de sintaxis, puedes verificar la configuración con el siguiente comando.

<code> dhcpd -t -cf /etc/dhcp/dhcpd.conf </code>








