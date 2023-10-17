#!/bin/bash

# Configuracion de la red

read -p "Ingresa la tarjeta de red (enp0s3) red interna:" tredI
read -p "Ingresa la IP red interna: " ipIN
read -p "Ingresa la netmask red interna: " mredIN

read -p "Ingresa la tarjeta de red (enp0s3) adaptador puente:" tredP
read -p "Ingresa la IP adaptador puente:" ipP
read -p "Ingresa la netmask adaptador puente:" mP
read -p "Ingresa la gateway adaptador puente:"  gatP


# Preguntar por los datos de configuración dhcp
read -p "Ingresa la subnet (ej. 192.168.1.0): " subnet
read -p "Ingresa la netmask (ej. 255.255.255.0): " netmask
read -p "Ingresa el router (ej. 192.168.1.1): " router
read -p "Ingresa el servidor de nombres de dominio (ej. 8.8.8.8): " dns_server
read -p "Ingresa el rango de direcciones (ej. 192.168.1.4 192.168.1.10): " address_range
read -p "Ingresa un domain-name 'example.org': " domain
read -p "Ingresa un domain-name-servers ns1.example.org, ns2.example.org; :" dname
read -p "Ingresa la tarjeta de red (adaptador puente)" : interfaz

# Configurar la red

sudo bash -c "cat <<EOL > /etc/network/interfaces

auto $tredI
iface $tredI inet static
address $ipIN
netmask $mredIN

auto $tredP
iface $tredP inet static
address $ipP
netmask $mP
gateway $gatP"


# Instalar el servidor DHCP
sudo apt install -y isc-dhcp-server


# Crear un respaldo del archivo de configuración actual
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak


# Escribir la nueva configuración en el archivo
sudo bash -c "cat <<EOL > /etc/dhcp/dhcpd.conf


# dhcpd.conf
#
# Sample configuration file for ISC dhcpd
#


# option definitions common to all supported networks...
option domain-name \"$domain\";
option domain-name-servers $dname;


default-lease-time 600;
max-lease-time 7200;






subnet $subnet netmask $netmask {
option routers $router;
option domain-name-servers $dns_server;
option subnet-mask $netmask;
option broadcast-address $(echo $subnet | awk -F. '{print $1"."$2"."$3".255"}');
}


# The ddns-updates-style parameter controls whether or not the server will
# attempt to do a DNS update when a lease is confirmed. We default to the
# behavior of the version 2 packages ('none', since DHCP v2 didn't
# have support for DDNS.)
ddns-update-style none;


# If this DHCP server is the official DHCP server for the local
# network, the authoritative directive should be uncommented.
#authoritative;


# Use this to send dhcp log messages to a different log file (you also
# have to hack syslog.conf to complete the redirection).
#log-facility local7;


# No service will be given on this subnet, but declaring it helps the
# DHCP server to understand the network topology.


#subnet 10.152.187.0 netmask 255.255.255.0 {
#}


# This is a very basic subnet declaration.


#subnet 10.254.239.0 netmask 255.255.255.224 {
#  range 10.254.239.10 10.254.239.20;
#  option routers rtr-239-0-1.example.org, rtr-239-0-2.example.org;
#}


# This declaration allows BOOTP clients to get dynamic addresses,
# which we don't really recommend.


#subnet 10.254.239.32 netmask 255.255.255.224 {
#  range dynamic-bootp 10.254.239.40 10.254.239.60;
#  option broadcast-address 10.254.239.31;
#  option routers rtr-239-32-1.example.org;
#}


# A slightly different configuration for an internal subnet.
#subnet 10.5.5.0 netmask 255.255.255.224 {
#  range 10.5.5.26 10.5.5.30;
#  option domain-name-servers ns1.internal.example.org;
#  option domain-name "internal.example.org";
#  option routers 10.5.5.1;
#  option broadcast-address 10.5.5.31;
#  default-lease-time 600;
#  max-lease-time 7200;
#}


# Hosts which require special configuration options can be listed in
# host statements.   If no address is specified, the address will be
# allocated dynamically (if possible), but the host-specific information
# will still come from the host declaration.


#host passacaglia {
#  hardware ethernet 0:0:c0:5d:bd:95;
#  filename "vmunix.passacaglia";
#  server-name "toccata.example.com";
#}


# Fixed IP addresses can also be specified for hosts.   These addresses
# should not also be listed as being available for dynamic assignment.
# Hosts for which fixed IP addresses have been specified can boot using
# BOOTP or DHCP.   Hosts for which no fixed address is specified can only
# be booted with DHCP, unless there is an address range on the subnet
# to which a BOOTP client is connected which has the dynamic-bootp flag
# set.
#host fantasia {
#  hardware ethernet 08:00:07:26:c0:a5;
#  fixed-address fantasia.example.com;
#}


# You can declare a class of clients and then do address allocation
# based on that.   The example below shows a case where all clients
# in a certain class get addresses on the 10.17.224/24 subnet, and all
# other clients get addresses on the 10.0.29/24 subnet.


#class "foo" {
#  match if substring (option vendor-class-identifier, 0, 4) = "SUNW";
#}


#shared-network 224-29 {
#  subnet 10.17.224.0 netmask 255.255.255.0 {
#    option routers rtr-224.example.org;
#  }
#  subnet 10.0.29.0 netmask 255.255.255.0 {
# option routers rtr-29.example.org;
#  }
#  pool {
#    allow members of "foo";
#    range 10.17.224.10 10.17.224.250;
#  }
#  pool {
#    deny members of "foo";
#    range 10.0.29.10 10.0.29.230;
#  }
#} "


# Reiniciar el servicio DHCP
sudo systemctl restart isc-dhcp-server


echo "Configuración de DHCP completada."


# Configurar salida a internet de los clientes.


# Verificar si la línea está presente y descomentada


if grep -q '^net.ipv4.ip_forward=1' /etc/sysctl.conf;
then
    echo "La línea net.ipv4.ip_forward=1 ya está presente y descomentada."
else
    # Verificar si la línea está presente pero comentada
    if grep -q '^#net.ipv4.ip_forward=1' /etc/sysctl.conf;
then
        # Descomentar la línea
        sudo sed -i 's/^#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
        echo "La línea net.ipv4.ip_forward=1 estaba comentada y ha sido descomentada."
else
        # Añadir la línea al final del archivo
        echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
        echo "La línea net.ipv4.ip_forward=1 ha sido añadida."
    fi
fi


# Aplicar los cambios
sudo sysctl -p


echo "Configuración de ip_forward completada."
sudo iptables -t nat -A POSTROUTING -o $interfaz -j MASQUERADE
sudo iptables-save | sudo tee /etc/iptables/rules.v4
sudo apt-get install iptables-persistent -y
sudo systemctl restart isc-dhcp-server


# Por si da error de sintaxis


dhcpd -t -cf /etc/dhcp/dhcpd.conf


