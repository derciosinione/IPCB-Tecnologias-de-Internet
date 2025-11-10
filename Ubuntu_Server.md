error: 

```yaml
<username> is not in the sudoers file. This incident will be reported.
```

1) Se souberes a password do root

Passa para root:

> su -

Adiciona o utilizador ao grupo sudo:


```bash
adduser ubuntuuser1 sudo
# ou
usermod -aG sudo ubuntuuser1
```

trocar o servername

⚙️ 2️⃣ Rename the user

usermod -l novoNome nomeAntigo

> usermod -l server3 ubuntuserver


🧠 3️⃣ Rename the home directory

> mv /home/ubuntuserver /home/server3
> usermod -d /home/server3 -m server3


| Ação                      | Comando                               |
| ------------------------- | ------------------------------------- |
| Renomear utilizador       | `usermod -l server2 server1`          |
| Renomear diretório home   | `mv /home/server1 /home/server2`      |
| Atualizar caminho home    | `usermod -d /home/server2 -m server2` |
| Renomear grupo (opcional) | `groupmod -n server2 server1`         |

trocar o Hostname

> hostnamectl

🧠 2️⃣ Alterar o hostname para server2

> sudo hostnamectl set-hostname server2

⚙️ 3️⃣ Atualizar o ficheiro /etc/hosts

> sudo nano /etc/hosts


Reinicia:

> reboot

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
ipv6: fe80::a00:27ff:feac:c1e8

-----

user: server2
password: root
ipv4: 192.168.1.116/23
ipv6: fe80::a00:27ff:fe9a:f8c2/64

----

user: server3
password: root
ipv4: 192.168.1.13/23
ipv6: fe80::a00:27ff:fe9a:f8c2/64

---
user: ubuntudesktop
password: root
ipv4: 192.168.1.111/23
ipv6: fe80::a00:27ff:fe9a:f8c2/64

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
