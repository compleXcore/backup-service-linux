# backup-service-linux
Yedek alma &amp; Servis açma işlemi

# 1. Adım Shell Script dosya oluşturma
Kendinize göre yolunu değiştirebilirsiniz. Aşağıda önce dosyayı oluşturuyoruz sonra ise içerisine kodlarını yazıyoruz.

```ruby
// Dosya oluşturma komutu
sudo nano /usr/bin/script.sh
```
```ruby
// script.sh 'ın içerisinde olması gerek kodlar
#!/bin/bash

# Yedek almak için fonksiyon
backup_files() {
    local source_dir="$1"
    local backup_dir="$2"
    local current_time=$(date +"%Y%m%d%H")
    local backup_filename="${current_time}_backup.tar.gz"

    tar -czf "${backup_dir}/${backup_filename}" -C "${source_dir}" .
    echo "Yedekleme tamamlandı: ${backup_dir}/${backup_filename}"
}

# Saat başı yedeklemeyi kontrol eder
# Şuanki dakika bilgisini al
current_minute=$(date +"%M")

# Eğer saat başı ise yedekleme işlemini gerçekleştir
if [[ "$current_minute" == "00" ]]; then
    # Yedekleme işlemini gerçekleştir
    source_directory="/path/to/your/scripts"  # Scriptlerin olduğu dizin
    backup_directory="/path/to/your/backup"  # Yedeklerin kaydedileceği dizin
    backup_files "$source_directory" "$backup_directory"
fi
```
```ruby
// Dosyaya yetki verme komutu
sudo chmod +x /usr/bin/script.sh 
```

# 2. Adım Systemd'de servis dosyası oluşturalım
```ruby
// systemd'de servis dosyası oluşturma komutu
sudo nano /lib/systemd/system/shellscript.service 
```

Yukarıda kod systemd'de servis dosyası oluşturuyor. Allta vereceğim kodu içerisine yapıştırabilirsiniz. Allta ki kodda bir kaç değişiklik yapabilirsiniz.
NOT: "ExecStart" kısmını değiştirmeniz gerekebilir!. Eğer shell script dosyamızı farklı bir klasörde oluşturduysak yolunu yazmanız gerekiyor.

```ruby
[Unit]
Description=Bu kısmı istediğiniz yazabilirsiniz genellikle servis ne iş yaparsa o yazılır. Yedek Alma servis

[Service]
ExecStart=/usr/bin/script.sh

[Install]
WantedBy=multi-user.target
```
# 3. Adım Oluşturduğumuz Servisi Aktif Hale Getirme

Yeni bir servis dosyayı oluşturduğumuz için önce dosyamızı görebilmesi için yenilememiz gerekiyor.

```ruby
sudo systemctl daemon-reload
```

Alttaki komutları sırayla yazmamız gerekiyor. İlk komut servisimizi aktif hale getirecek. İkinci komutumuz ise servisimizi başlatıcak.

```ruby
sudo systemctl enable shellscript.service 
sudo systemctl start shellscript.service 
```

Son komutumuz ise servisimiz aktif olup olmadığı ve çalışıp çalışmadığını anlamamız için bir log tarzı çıktı veriyor.
```ruby
sudo systemctl status shellscript.service 
```

Sizde de buna benzer bir çıktı çıktıysa çalışıyordur.
```ruby
○ shellscript.service - Yedek alma programi
     Loaded: loaded (/lib/systemd/system/shellscript.service; enabled; preset: disabled)
     Active: inactive (dead) since Tue 2023-08-01 03:36:19 EDT; 35min ago
   Duration: 7ms
    Process: 1954 ExecStart=/home/kali/siber-sh/yedek-servis.sh (code=exited, status=0/SUCCESS)
   Main PID: 1954 (code=exited, status=0/SUCCESS)
        CPU: 6ms

Aug 01 03:36:19 kali systemd[1]: Started shellscript.service - Yedek alma programi.
Aug 01 03:36:19 kali systemd[1]: shellscript.service: Deactivated successfully.
```

Okuduğunuz için teşekkürler.
