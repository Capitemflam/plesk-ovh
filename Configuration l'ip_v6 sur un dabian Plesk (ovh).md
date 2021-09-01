##### Configuration l'ip_v6 sur un dabian Plesk (ovh)	

Se connecter au serveur via SSH 

```
ssh debian@ipv4duserveur
```

Le mot de passe est dans le mail de OVH.

La méthode la plus souvent préconisée est de créer un fichier de configuration dans le répertoire `/etc/network/interfaces.d/`:

```
nano /etc/network/interfaces.d/51-cloud-init-ipv6.cfg
```

Cela vous permet de séparer la configuration IPv6 et de rétablir facilement les modifications en cas d’erreur.

Ajoutez les lignes suivantes au fichier. Remplacez les éléments génériques (c’est-à-dire *YOUR_IPV6*, *IPV6_PREFIX* et *IPV6_GATEWAY*) ainsi que l’interface réseau (si votre serveur n’utilise pas **eth0**) par vos valeurs personnalisées.

```
auto eth0
iface eth0 inet6 static
mtu 1500
address YOUR_IPV6
netmask IPV6_PREFIX
post-up /sbin/ip -6 route add IPV6_GATEWAY dev eth0
post-up /sbin/ip -6 route add default via IPV6_GATEWAY dev eth0
pre-down /sbin/ip -6 route del default via IPV6_GATEWAY dev eth0
pre-down /sbin/ip -6 route del IPV6_GATEWAY dev eth0
```

Voici un exemple concret :

```
iface eth0 inet6 static
address 2001:41d0:xxx:xxxx::999
netmask 128
post-up /sbin/ip -6 route add 2001:41d0:xxx:xxxx::111 dev eth0
post-up /sbin/ip -6 route add default via 2001:41d0:xxx:xxxx::111 dev eth0
pre-down /sbin/ip -6 route del default via 2001:41d0:xxx:xxxx::111 dev eth0
pre-down /sbin/ip -6 route del 2001:41d0:xxx:xxxx::111 dev eth0
```

Redémarrez ensuite votre service réseau avec l’une des commandes suivantes :

```
service networking restart
systemctl restart networking
```

Et ensuite ajouter l'ip_v6 dans le serveur plesk (réglages > ip serveur) manuellement.