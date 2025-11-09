🧩 1️⃣ Verificar se a firewall (UFW) está ativa

sudo ufw status

⚙️ 2️⃣ Desligar a firewall

Para desativar completamente:
> sudo ufw disable

caso for necessario instalar 

sudo apt update
sudo apt install ufw -y

# Servidores
user: server1
password: root
ipv4: 192.168.1.73/23

-----

user: server2
password: root
ipv4: 192.168.1.116/23

----

user: server3
password: root
ipv4: 192.168.1.13/23

---
user: ubuntudesktop
password: root
ipv4: 192.168.1.111/23^

## Mountar a pasta compartilhada

caso ja tiver tudo configurado


```bash
sudo modprobe vboxsf
sudo mount -t vboxsf VirtualBoxShared /mnt/VirtualBoxShared
```


> sudo apt update
> sudo apt install virtualbox-guest-utils -y

Then confirm the kernel module is loaded:
> lsmod | grep vboxsf

If it’s empty, load it manually:
> sudo modprobe vboxsf


🧠 2️⃣ Mount the folder manually

The folder name in your VirtualBox settings is VirtualBoxShared.
So mount it manually:

```bash
sudo mkdir -p /mnt/VirtualBoxShared
sudo mount -t vboxsf VirtualBoxShared /mnt/VirtualBoxShared
ls /mnt/VirtualBoxShared
```

🔐 3️⃣ Fix permissions

Add your user to the vboxsf group:
```bash
sudo usermod -aG vboxsf $USER
```

Then reboot:

Add your user to the vboxsf group:

```bash
sudo reboot
```

After reboot, try again:

```bash
ls /mnt/VirtualBoxShared
```
