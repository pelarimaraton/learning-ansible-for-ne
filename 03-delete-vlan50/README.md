# Menghapus VLAN 50

Kelima switch memakai image `vrnetlab/cisco_iol:L2-17.18.02`. Login: `admin/admin`. IP management sudah ditetapkan di topology dan inventory.

## Jalankan
Aktifkan virtualenv sesuai README utama, lalu:
```bash
cd /home/rabilnugraha/03-containerlab-ansible-playbook/03/03-delete-vlan50
sudo containerlab deploy -t topology.clab.yml
```
Tunggu switch selesai boot dan SSH siap. Jalankan:
```bash
ansible-playbook prepare-vlan50.yml
ansible-playbook playbook.yml
```
Perubahan disimpan ke startup-config. Periksa hasil dengan perintah berikut:
```bash
ansible switches -m cisco.ios.ios_command -a 'commands="show vlan brief"'
```

`prepare-vlan50.yml` membuat VLAN 50 terlebih dahulu untuk latihan penghapusan. Tidak perlu menjalankannya jika VLAN 50 sudah ada.

## Selesai
```bash
sudo containerlab destroy -t topology.clab.yml
```
Untuk reset bersih gunakan `destroy --cleanup`; ini menghapus konfigurasi runtime yang tersimpan.

## Membaca playbook
- `hosts: switches`: jalankan pada semua switch dalam inventory.
- `gather_facts: false`: lewati pengumpulan informasi otomatis.
- `tasks`: daftar pekerjaan.
- `ios_config`: mengubah konfigurasi switch.
- `save_when: modified`: menyimpan running-config yang berbeda dari startup-config.
