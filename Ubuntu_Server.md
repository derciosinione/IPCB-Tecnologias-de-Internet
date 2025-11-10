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

No server 2 configurar

⚙️ 1️⃣ Editar o ficheiro de configuração de zonas

Abre o ficheiro:

```bash
zone "danune.cb" {
    type master;
    file "/etc/bind/db.danune.cb";
};
```

⚙️ 2️⃣ Criar o ficheiro de zona

Cria o ficheiro:

```bash
sudo nano /etc/bind/db.danune.cb
```

E coloca o conteúdo seguinte:

```bash
;
; Zona principal do domínio danune.cb
;
$TTL 20
@       IN  SOA   milk.danune.cb. root.milk.danune.cb. (
                2025111001   ; Serial (AAAA/MM/DD/NN)
                5M           ; Refresh (5 minutos)
                1M           ; Retry (1 minuto)
                1D           ; Expire (1 dia)
                20           ; TTL (20 segundos)
)
        IN  NS    milk.danune.cb.
        IN  NS    cow.danune.cb.

; -----------------------------
; Registos A (endereços IPv4)
; -----------------------------
milk            IN  A   192.168.1.116
cow             IN  A   192.168.1.13
dns             IN  A   192.168.1.73
cliente         IN  A   192.168.1.111
server1         IN  A   192.168.1.73
server2         IN  A   192.168.1.116
server3         IN  A   192.168.1.13
ubuntudesktop   IN  A   192.168.1.111

; -----------------------------
; Registos CNAME (aliases)
; -----------------------------
www             IN  CNAME  milk.danune.cb.
ftp             IN  CNAME  milk.danune.cb.
```


✅ 4️⃣ Verificar e reiniciar

Verifica a sintaxe:

```bash
sudo named-checkzone danune.cb /etc/bind/db.danune.cb
```

Depois reinicia o serviço:

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

🔍 5️⃣ Testar a resolução

Agora testa diretamente no server2:

```bash
dig @192.168.1.116 milk.danune.cb
dig @192.168.1.116 cliente.danune.cb
dig @192.168.1.116 www.danune.cb
```


# 3) Configuração do servidor secundário

🧩 1️⃣ Configurar o servidor secundário (server3)

O servidor secundário vai replicar automaticamente a zona do primário (server2).
No Ubuntu Server 16, tudo se faz editando o ficheiro /etc/bind/named.conf.local.

Abre-o no server3:

```bash
sudo nano /etc/bind/named.conf.local
```

Adiciona esta configuração:

```bash
zone "danune.cb" {
    type slave;
    masters { 192.168.1.116; };      # IP do servidor primário (milk.danune.cb)
    file "/var/cache/bind/db.danune.cb";
};
```

```plaintext
💡 Nota:
O ficheiro é guardado automaticamente em /var/cache/bind (não em /etc/bind) porque o BIND precisa de permissões de escrita para receber a cópia da zona.
```

⚙️ 2️⃣ Reinicia o serviço BIND9

```bash
sudo systemctl restart bind9
sudo systemctl status bind9
```

🔍 3️⃣ Verificar se a replicação ocorreu

Após alguns segundos/minutos, verifica se o ficheiro foi transferido:

```bash
ls -l /var/cache/bind/
```

Se quiseres confirmar o conteúdo:

```bash
sudo cat /var/cache/bind/db.danune.cb
```

🧱 4️⃣ Testar resolução DNS a partir do secundário

No próprio server3, executa:

```bash
dig @127.0.0.1 milk.danune.cb
dig @127.0.0.1 cow.danune.cb
```

🧩 5️⃣ Criar um novo registo no primário

Agora volta ao server2 (primário) e abre o ficheiro da zona:

```bash
sudo nano /etc/bind/db.danune.cb
```

Adiciona esta nova entrada:

```arduino
iogurte     IN  A   192.168.1.120
```

🔁 6️⃣ Atualiza o número de série

No topo do ficheiro (linha Serial), incrementa o valor:

```arduino
2025111003 ; Serial
```arduino

Guarda e verifica:

sudo named-checkzone danune.cb /etc/bind/db.danune.cb

Se estiver OK:

sudo systemctl restart bind9

🔄 7️⃣ Verificar replicação no secundário

Após uns segundos, no server3 (secundário):

sudo cat /var/cache/bind/db.danune.cb | grep iogurte


✅ Se aparecer:

iogurte     IN  A   192.168.1.120


então a replicação está a funcionar perfeitamente!

🧠 8️⃣ Testar a partir do cliente

No cliente: 

dig @192.168.1.13 iogurte.danune.cb

;; ANSWER SECTION:
iogurte.danune.cb.   IN  A   192.168.1.120