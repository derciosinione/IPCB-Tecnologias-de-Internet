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
