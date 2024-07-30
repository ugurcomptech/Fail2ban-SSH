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







