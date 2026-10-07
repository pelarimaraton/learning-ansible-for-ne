# Melihat running-config

Kelima switch memakai image `vrnetlab/cisco_iol:L2-17.18.02`. Login: `admin/admin`. IP management sudah ditetapkan di topology dan inventory.

## Jalankan
Aktifkan virtualenv sesuai README utama, lalu:
```bash
cd /home/rabilnugraha/03-containerlab-ansible-playbook/03/01-show-running-config
sudo containerlab deploy -t topology.clab.yml
```
Tunggu switch selesai boot dan SSH siap. Jalankan:
```bash
ansible-playbook playbook.yml
```
Hasil running-config kelima switch ditampilkan di terminal.



## Selesai
```bash
sudo containerlab destroy -t topology.clab.yml
```
Untuk reset bersih gunakan `destroy --cleanup`; ini menghapus konfigurasi runtime yang tersimpan.

## Membaca playbook
- `hosts: switches`: jalankan pada semua switch dalam inventory.
- `gather_facts: false`: lewati pengumpulan informasi otomatis.
- `tasks`: daftar pekerjaan.
- `ios_command`: menjalankan perintah show.
- `register: hasil`: menyimpan jawaban switch.
- `debug`: menampilkan jawaban di terminal.
