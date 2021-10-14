# H64-TP1 | Serveur dhcp sur docker

![Docker](https://img.macg.co/2016/6/macgpic-1466506477-6907477055716-accroche.jpg)

 1. Installer Docker
```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install docker.io
sudo systemctl daemon-reload
sudo systemctl restart docker.service
sudo systemctl enable docker.service
```

2. Configurer les permissions de notre utilisateur
```
sudo usermod -a -G docker $USER
sudo systemctl restart docker.service
```

3. Télécharger l’image docker dhcp
```
sudo docker run -it networkboot/dhcpd
```
4. Lister les images et les conteneurs
```
sudo docker images
sudo docker container ls -a
```
5. Créer un nouveau dossier **data**
```
sudo mkdir data
```

6. Créer un fichier **data/dhcpd.conf**
```
sudo nano data/dhcpd.conf
```
exemple de contenu:
```
authoritative;
default-lease-time 600;
max-lease-time 7200;

subnet 10.0.0.0 netmask 255.255.255.0 {
 range 10.0.0.100 10.0.0.200;
 option domain-name-servers 8.8.8.8;
}
```

7. Trouver l’interface sur laquelle on lance le serveur dhcp (exemple : **ens32**) et configurer son adresse en statique (exemple: **10.0.0.10**)
```
ip a
```

8. Lancer notre serveur docker dhcp sur la bonne interface
 ```
sudo docker run -it --rm --init --net host -v "$(pwd)/data":/data networkboot/dhcpd ens32
```
9. Du côté client, mettre une interface (exemple: **ens32**) dans le même réseau privé que notre serveur et rafraichir son bail dhcp
 ```
 sudo dhclient ens32
 ```
 La nouvelle adresse IP du client se situe maintenant dans la plage **10.0.0.100-10.0.0.200** tel que configuré dans notre fichier **dhcpd.conf**
