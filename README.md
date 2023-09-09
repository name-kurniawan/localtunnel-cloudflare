## CATATAN MEMBUAT LOCALTUNNEL DENGAN CLOUDFLARE

## Membuat Akun di Cloudflare
* Membuat account Cloudflare di [sini](https://dash.cloudflare.com/sign-up)
* Register domain / beli domain di marketplace
  
## Install dan login Cloudflared
- Download installernya di [sini](https://github.com/cloudflare/cloudflared/releases?ref=kurnia.eu.orrg) . Sesuaikan dengan OS kalian
- (contoh pake Ubuntu)
  ```
  wget https://github.com/cloudflare/cloudflared/releases/download/2023.8.2/cloudflared-linux-amd64
  cloudflared-linux-amd64                     100%[===========================================================================================>]  34.77M  3.28MB/s    in 11s     
  2023-09-09 04:29:36 (3.28 MB/s) - ‘cloudflared-linux-amd64’ saved

  mv cloudflared-linux-amd64 cloudflared
  chmod +x cloudflared
  ./cloudflared tunnel login
  Please open the following URL and log in with your Cloudflare account:

  https://dash.cloudflare.com/argotunnel?aud=&callback=https%3A%2F%2Flogin.cloudflareaccess.org%2F-1JXlCjTJZE1yTwft1YAYc6iiQkQ9WEJAaDfNRr8pmc%3D

  Leave cloudflared running to download the cert automatically.
* buka link yg diberikan dengan browser
* Arahkan pada domain kita yg sudah diregister.
* cek di direktori
  `ls -la` \
* maka akan ada direktori .cloudflare dengan isi certificate
  ```
     ls -la .cloudflared
     total 16
     drwxr-xr-x 2 root root 4096 Sep  9 04:36 .
     drwx------ 7 root root 4096 Sep  9 04:32 ..
     -rw------- 1 root root 1942 Sep  9 04:36 cert.pem

## Membuat tunnel
* buat tunnel dengan perintah :
  ```
  ./cloudflared tunnel create Nama_Tunnel 
  #contoh 
  ./cloudflared tunnel create tunnelku` 
  
* untuk memastikan tunnel sudah terbuat dapat kita cek :
  ```
  ./cloudflared tunnel list

## Membuat File Konfigurasi Tunnel
* buat file config :  
  ```
  nano .clodflared/config.yml

* isikan dengan config sbb :

  ```
  tunnel: 3b703a42-9d81-4641-8d42-6e48c722ae37
  credentials-file: /root/.clodflared/3b703a42-9d81-4641-8d42-6e48c722ae37.json
  #ganti 3b703a42-9d81-4641-8d42-6e48c722ae37 dengan uid kalian
  ingress:
    - hostname: local-serv.example.com
      service: https://localhost:80
    - hostname: ssh.example.com
      service: ssh://localhost:22
    - hostname: db.example.com
      service: tcp://localhost:3306
   # Sesuaikan dengan kebutuhan kalian
    - service: http_status:404

* mendaftarkan mesin ke cloudflare
  ```
  cloudflared tunnel route dns <UUID or NAME> <hostname>
  #contoh
  cloudflared tunnel route dns tunnelku local-serv.leg.com
  
* jalan di mesin kita
  ```
  cloudflared tunnel run <Tunnel-UUID>
  #contoh
  cloudflared tunnel run 3b703a42-9d81-4641-8d42-6e48c722ae37
  
* Atau bisa sekali jalan dengan perintah  
  ```
  cloudflared tunnel ingress validate
* Saatnya kita routing di mesin kita
  ```
  ./cloudflared tunnel route dns <UUID or NAME> example.com
  ./cloudflared tunnel route dns <UUID or NAME> static.example.com
  ./cloudflared tunnel route dns <UUID or NAME> ssh.example.com
  ./cloudflared tunnel route dns <UUID or NAME> db.example.com
  #contoh
  ./cloudflared tunnel route dns tunnelku local-ser.legs.com
  ./cloudflared tunnel route dns tunnelku static.legs.com
  ./cloudflared tunnel route dns tunnelku ssh.legs.com
  ./cloudflared tunnel route dns tunnelku db.legs.com
  
* Jalankan dan cek tunnel
  ```
  ./cloudflared tunnel run <Tunnel-UUID>
  #contoh
  ./cloudflared tunnel run 3b703a42-9d81-4641-8d42-6e48c722ae37
  
