# Fail2ban-SSH
Merhaba, bu yazımızda Fail2ban kullanarak nasıl SSH bağlantılarını sınırlayacağımızı ve engelleyeceğimizi anlatacağım.

## SSH İçin Özel Bir Jail Oluşturalım:
Fail2Ban konfigürasyon dosyası olan /etc/fail2ban/jail.local veya /etc/fail2ban/jail.d/ssh.conf dosyasını açın. Eğer dosya yoksa, oluşturun ve aşağıdaki gibi yapılandırın:

```conf
[sshd]
enabled = true
port    = 22
logpath = /var/log/auth.log
bantime = 6h
findtime = 10m
maxretry = 3
```

Bu yapılandırma, belirli bir süre içinde (findtime) 3'ten fazla başarısız giriş denemesi (maxretry) olan IP adreslerini 6 saat boyunca (bantime) yasaklayacaktır.

### Fail2ban Restart

```
sudo systemctl restart fail2ban
```

### Test

Terminale aşağıdaki kodu yazarak SSH jailimizin hangi IP leri banladığını görüntüleyebilirsiniz

```
root@ubuntu:~# fail2ban-client status sshd

Status for the jail: sshd
|- Filter
|  |- Currently failed: 0
|  |- Total failed:     0
|  `- File list:        /var/log/auth.log
`- Actions
   |- Currently banned: 28
   |- Total banned:     28
   `- Banned IP list:   103.142.87.177 103.152.18.138 103.163.119.106 103.36.84.107 104.168.96.248 114.218.66.207 119.18.55.67 125.142.39.13 128.199.117.9 14.29.142.199 154.221.22.77 165.232.136.32 167.114.96.239 167.99.70.85 185.28.154.221 190.167.237.191 195.154.179.50 202.125.94.146 210.91.154.187 213.194.140.33 4.223.102.236 43.133.54.108 45.180.136.12 5.11.145.151 61.72.55.130 80.94.95.81 85.209.11.254 89.208.105.254
```


## SSH Portunu Değiştirmek

Öncelikle, SSH için kullanmak istediğiniz yeni bir port numarası seçin. Varsayılan port olan 22'nin yerine, 1024-65535 arasında bir port kullanmanız önerilir. Örneğin, 3333 portunu kullanacağız.
 
Bunun için `/etc/ssh/sshd_config` dosyasına gidip **port 22** yazılı kısma seçmiş olduğumuz Port numarasını yazıyoruz.

```conf
port 3333
```

### Güvenlik Duvarı Ayarları

Yeni portun güvenlik duvarı tarafından engellenmediğinden emin olun. ufw kullanıyorsanız, aşağıdaki komutları çalıştırınız:


#### UFW

```conf
sudo ufw allow 3333/tcp
sudo ufw reload
```
#### İPTables

```conf
sudo iptables -A INPUT -p tcp --dport 3333 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### SSH Servisini Yeniden Başlatmak

```
sudo systemctl restart ssh
```

Veya


```
sudo systemctl restart sshd
```

`ssh -p 3333 kullanıcı_adı@sunucu_ip_adresi` yazarak test sağlayabilirsiniz.


## NOT

Bu döküman, SSH güvenliğini artırmak amacıyla port değişikliği ve Fail2Ban kullanımı hakkında genel bilgiler sağlamaktadır. Ancak, güvenlik katmanlarını artırmak için ek önlemler almanız önemlidir. Örneğin, SSH anahtar tabanlı kimlik doğrulama kullanarak parola girişlerini devre dışı bırakabilir, iki faktörlü kimlik doğrulama ekleyebilir ve güvenlik politikalarınızı düzenli olarak gözden geçirebilirsiniz.







