# Nginx Web Sunucusuna SSL/TLS Sertifikası Nasıl Kurulur?

Merhabalar, bu yazıda sizlere Nginx web sunucusuna SSL/TLS sertifikasının nasıl kurulacağını adım adım anlatacağım. SSL/TLS sertifikaları, web sitenizin güvenliğini sağlamak ve ziyaretçilerinizin bilgilerini korumak için kritik öneme sahiptir. Nginx'i kullanarak bu sertifikaları nasıl kurabileceğinizi bu yazı ile öğrenebilirsiniz.

Ayrıca bu konu üzerine hazırlanmış youtube videosuna da göz atabilirsiniz -->
[ Nginx Web Sunucusuna SSL/TLS Sertifikası Nasıl Kurulur? (Kali Linux)](https://youtu.be/J4oHVKxeBTI)


---

### Nginx Kurulumu ve Web Sitesi Ayarları

#### 1. Nginx Kurulumu

```bash
sudo apt-get install nginx
```
Bu komut, Debian veya Ubuntu tabanlı bir Linux dağıtımında Nginx'in kurulumunu sağlar. "apt-get" paket yöneticisi aracılığıyla "nginx" paketini kurarak Nginx'i sistemimize yükler.

---

#### 2. Web Sitesi Dizini Oluşturma

```bash
sudo mkdir -p /var/www/example.com/html
```
Bu komut, "/var/www/example.com/html" dizinini oluşturur. Bu dizin, "example.com" adlı bir web sitesinin dosyalarının saklanacağı yerdir. "-p" bayrağı, varsa üst dizinleri de oluşturur.

---
#### 3. Kullanıcı ve İzin Ayarları

```bash
sudo chown $USER:$USER /var/www/example.com
```
Bu komut, "/var/www/example.com" dizinini kullanıcının ($USER) sahipliğine ve grubuna atanır. Bu, kullanıcının bu dizinde dosya oluşturmasına ve değiştirmesine izin verir.
```bash
sudo chmod 755 /var/www/example.com
```
Bu komut, "/var/www/example.com" dizinine izinler atar. "755" izinleri, dizini sahibinin okuma, yazma ve çalıştırma izni verir; diğer kullanıcıların ise sadece okuma ve çalıştırma izni vardır.

---
#### 4. Ana Sayfa Oluşturma

```bash
nano /var/www/example.com/html/index.html
```
Bu komut, bir metin düzenleyici olan "nano" ile "/var/www/example.com/html/index.html" dosyasını açar. Bu dosya, web sitesinin ana sayfasının içeriğini içerecektir. İlgili HTML kodu bu dosyaya eklenir.

---
#### 5. Sanal Sunucu Yapılandırma Dosyasını Açma

```bash
nano /etc/nginx/sites-available/example.com
```
Bu komut, Nginx'in sanal sunucu yapılandırma dosyasını düzenlemek için "nano" metin düzenleyicisini kullanarak "/etc/nginx/sites-available/example.com" dosyasını açar. Bu dosya, "example.com" web sitesinin yapılandırmasını içerecektir.

---
#### 6. Sanal Sunucuyu Etkinleştirme

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled
```
Bu komut, "/etc/nginx/sites-available/example.com" dosyasını "/etc/nginx/sites-enabled" dizinine sembolik bir bağlantı oluşturur. Bu, Nginx'in yapılandırılmış sanal sunucularını etkinleştirmesine yardımcı olur.

---
#### 7. Host Dosyasını Düzenleme

```bash
sudo nano /etc/hosts
```

```
127.0.1.1       example.com www.example.com
```
Bu komut, "/etc/hosts" dosyasını düzenlemek için "nano" metin düzenleyicisini açar. Bu dosya, IP adreslerini host isimleriyle eşlemek için kullanılır. Burada, "example.com" ve "[www.example.com](http://www.example.com/)" host isimleri için yerel IP adresi atanır.

---
#### 8. SSL Sertifikası Oluşturma

```bash
openssl genpkey -algorithm RSA -out server.key
```
Bu komut, RSA algoritmasını kullanarak bir özel anahtar olan "server.key" dosyasını oluşturur. Bu özel anahtar, web sunucumuzun SSL/TLS sertifikası oluşturulurken kullanılacaktır.
```bash
openssl req -new -key server.key -out server.csr
```
Bu komut, "server.key" dosyasını kullanarak bir sertifika imza isteği (CSR) olan "server.csr" dosyasını oluşturur. Bu CSR, web sunucumuzun SSL/TLS sertifikasını almak için bir sertifika yetkilisine (CA) gönderilecektir.
```bash
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```
Bu komut, CSR dosyasını kullanarak bir x509 sertifikası olan "server.crt" dosyasını oluşturur. Bu sertifika, web sunucumuzun SSL/TLS bağlantılarını sağlamak için kullanılacaktır.

---
#### 9. SSL Sertifikası Dizinini Oluşturma ve Taşıma

```bash
sudo mkdir /etc/nginx/ssl
```
Bu komut, "/etc/nginx/ssl" dizinini oluşturur. Bu dizin, Nginx'in SSL/TLS sertifikalarını saklayacağı yerdir.
```bash
sudo mv server.crt server.key /etc/nginx/ssl
```
Bu komut, oluşturulan SSL/TLS sertifikası olan "server.crt" ve özel anahtar olan "server.key" dosyalarını "/etc/nginx/ssl" dizinine taşır.

---
#### 10. Nginx Ana Yapılandırma Dosyasını Düzenleme

```bash
sudo nano /etc/nginx/nginx.conf
```
Bu komut, Nginx'in ana yapılandırma dosyasını "nano" metin düzenleyicisi ile açar. Burada, HTTPS trafiğini yönlendirecek bir sanal sunucu bloğu eklenir.
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
http kod bloku altına bu komutlar eklenir.

---
#### 11. Nginx'i Yeniden Başlatma

```bash
sudo systemctl restart nginx
```
Bu komut, Nginx'i yeniden başlatır, bu da yapılandırma değişikliklerinin uygulanmasını sağlar.

---
#### 12. Nginx Durumunu Kontrol Etme

```bash
sudo systemctl status nginx
```
Bu komut, Nginx'in çalışma durumunu kontrol eder ve durumunu rapor eder. Bu, Nginx'in başarıyla başlatılıp başlatılmadığını kontrol etmek için kullanılır.

---

Kurulum bu kadardı :)
https://example.com gibi kendi oluşturduğunuz adresi tarayıcınıza yazarak ssl sertifikanızı test edebilirsiniz.
