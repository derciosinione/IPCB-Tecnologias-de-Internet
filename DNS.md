

1) Configuração do servidor primário do domínio de topo “cb”

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc -y
```

⚙️ 2️⃣ Editar o ficheiro principal de configuração

Abrir:
```bash
nano /etc/bind/named.conf.local
```

Adicionar no final:

```bash
zone "cb" {
    type master;
    file "/etc/bind/db.cb";
};
```

> Isso diz ao BIND9 que este servidor é o mestre (primário) do domínio “cb” e vai usar o ficheiro de zona /etc/bind/db.cb.


🧠 3️⃣ Criar o ficheiro de zona do domínio “cb”

Cria o ficheiro baseado no modelo padrão:

```bash
cp /etc/bind/db.local /etc/bind/db.cb
nano /etc/bind/db.cb
```

Apaga o conteúdo antigo e substitui por:

```bash
$TTL 20
@   IN  SOA dns.cb. root.cb. (
        1        ; Serial
        10M      ; Refresh (10 minutos)
        1M       ; Retry (1 minuto)
        1D       ; Expire (1 dia)
        20       ; Negative Cache TTL
)
; Servidor DNS primário
    IN  NS   dns.cb.
; Registo A do servidor DNS
dns IN  A    192.168.1.73

```


💡 Explicação:

@ representa o domínio “cb”.

root.cb. é o email do administrador (equivalente a root@cb).

Os tempos (10M, 1M, etc.) estão nos valores pedidos na ficha.

O registo A liga o nome dns.cb ao IP do servidor.

Esta configuracao esta salva na pasta compartilhada e pode ser copiada para o local desejado 

```bash
cp /mnt/VirtualBoxShared/server1/etc/bind/db.cb /etc/bind/db.cb
```

⚙️ 4️⃣ Verificar configuração

Executa:

```bash
named-checkconf
named-checkzone cb /etc/bind/db.cb
```

🔁 5️⃣ Reiniciar o serviço

```bash
systemctl restart bind9
systemctl enable bind9
```