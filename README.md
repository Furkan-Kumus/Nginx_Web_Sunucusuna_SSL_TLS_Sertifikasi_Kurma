### Nginx Kurulumu ve Web Sitesi Ayarları

#### 1. Nginx Kurulumu

```bash
sudo apt-get install nginx
```

#### 2. Web Sitesi Dizini Oluşturma

```bash
sudo mkdir -p /var/www/example.com/html
```

#### 3. Kullanıcı ve İzin Ayarları

```bash
sudo chown $USER:$USER /var/www/example.com
sudo chmod 755 /var/www/example.com
```

#### 4. Ana Sayfa Oluşturma

```bash
nano /var/www/example.com/html/index.html
```

#### 5. Sanal Sunucu Yapılandırma Dosyasını Açma

```bash
nano /etc/nginx/sites-available/example.com
```

#### 6. Sanal Sunucuyu Etkinleştirme

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled
```

#### 7. Host Dosyasını Düzenleme

```bash
sudo nano /etc/hosts
```

```
127.0.1.1       example.com www.example.com
```

#### 8. SSL Sertifikası Oluşturma

```bash
openssl genpkey -algorithm RSA -out server.key
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

#### 9. SSL Sertifikası Dizinini Oluşturma ve Taşıma

```bash
sudo mkdir /etc/nginx/ssl
sudo mv server.crt server.key /etc/nginx/ssl
```

#### 10. Nginx Ana Yapılandırma Dosyasını Düzenleme

```bash
sudo nano /etc/nginx/nginx.conf
```

```
server {
    listen 443 ssl;
    ssl_certificate /etc/nginx/ssl/server.crt;
    ssl_certificate_key /etc/nginx/ssl/server.key;

    root /var/www/example.com/html;
    index index.html;
    
    server {
        listen 80;
        return 301 https://$host$request_uri;
    }
}
```

#### 11. Nginx'i Yeniden Başlatma

```bash
sudo systemctl restart nginx
```

#### 12. Nginx Durumunu Kontrol Etme

```bash
sudo systemctl status nginx
```

---

Bu adımlar, Nginx web sunucusunu kurmayı, bir web sitesinin dosya yapısını oluşturmayı, SSL/TLS sertifikası eklemeyi ve HTTP trafiğini HTTPS'e yönlendirmeyi içerir.
