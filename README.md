# **Modul 3**

## **kelompok 10**



### **Soal Praktikum**

buat SubDomain dev.vm.local dengan ketentuan:

* Menggunakan Ansible
* Menggunakan lxc yang sama dengan vm.local
* folder harus menggunakan var/www/html/dev{nama_app}

### **Problem Solving**

* masuk ke directory dengan ansible
  - <img src= "https://github.com/acid99/Sistem-Administrasi-Server/blob/main/assets/laprak3/2021-12-15_1.png?raw=true">
  ```
  ---
  - hosts: all
    become : yes
    tasks:
      - name: install bind9 dan dnsutils
        apt:
         pkg:
           - bind9
           - dnsutils
  ```

* Install sublaravel.yml dengan ansible
  ```
  ansible-playbook -i hosts sublaravel.yml -k
  ```
* Buat file config.yml
  - <img src= "https://github.com/acid99/Sistem-Administrasi-Server/blob/main/assets/laprak3/2021-12-15_3.png?raw=true">

  ```
  ---
  - hosts: all
    become : yes
    tasks:
     - name: membuat direktori
       file:
        path: /var/www/html/dev/landing
        state: directory
     - name: copy file vm.local
       copy:
        src: /etc/bind/vm/vm.local
        dest: /var/www/html/dev/landing
     - name: mengganti konfigurasi
       replace:
        path: /var/www/html/dev/landing/vm.local
        regexp: 'www'
        replace: 'dev'
     - name: copy file named.conf.local
       copy:
        src: /etc/bind/named.conf.local
        dest: /etc/bind/named.conf.local
     - name: mengganti konfigurasi conf local
       replace:
        path: /etc/bind/named.conf.local
        regexp: '/etc/bind/vm/vm.local'
        replace: '/var/www/html/dev/landing/vm.local'
     - name: mengganti konfigurasi conf local part2
       replace:
        path: /etc/bind/named.conf.local
        regexp: '/etc/bind/vm/115.168.192.in-addr.arpa'
        replace: '/var/www/html/dev/landing/115.168.192.in-addr.arpa'
     - name: copy file 115.168.192.in-addr.arpa
       copy:
        src: /etc/bind/vm/115.168.192.in-addr.arpa
        dest: /var/www/html/dev/landing
     - name: copy file resolv.conf
       copy:
        src: /etc/resolv.conf
        dest: /etc/resolv.conf
     - name: copy file named.conf.options
       copy:
        src: /etc/bind/named.conf.options
        dest: /etc/bind/named.conf.options
     - name: restart nginx
       service:
        name: nginx
        state: restarted
     - name: restart bind9
       action: service name=bind9 state=restarted
  ```

* install config1.yml
  ```
  ansible-playbook -i hosts config.yml -k
  ```

* tambah dev.vm.local ke /etc/hosts
  - ![image](https://user-images.githubusercontent.com/93419670/146424348-8d15b0b0-aacc-470d-9a42-78ca75c5770d.png)

* buka vm.local dan tambahkan baris www.
  - ![image](https://user-images.githubusercontent.com/93419670/146424892-92be3cff-ec0b-4e99-ace8-f093b5ba715a.png)
  
* edit vm.local dalam directory /etc/nginx/sites-enabled/
  - ![image](https://user-images.githubusercontent.com/93419670/146428656-ab252b47-9c31-46bf-bd3e-5abdc64cf5ff.png)

* edit vm.local didalam directory /etc/bind/vm/
  - ![image](https://user-images.githubusercontent.com/93419670/146429316-24456d69-5b26-47e0-89c9-542a21a9e34d.png)

* Restart bind9

  <p align="center">
        	<img src= "https://github.com/acid99/Sistem-Administrasi-Server/blob/main/assets/laprak3/2021-12-16_1.png?raw=true">
  </p>

- buka pengaturan wifi (windows), masukkan dns server 
  - ![image](https://user-images.githubusercontent.com/93419670/146425587-fd581a75-d2b4-4af0-8cee-6dfbd3d79af4.png)
  
- nonaktifkan IPv6
  - ![sas1](https://user-images.githubusercontent.com/93419670/146426507-0b52f05b-775b-4656-9698-d4b82f378781.PNG)

- reconnect wifi, lalu cek dev.vm.local
  - ![image](https://user-images.githubusercontent.com/93419670/146427572-8f932e7e-eedb-426f-9448-f4fdb8a433f6.png)
