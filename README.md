# Training sederhana — 5 switch Cisco IOL-L2

Topology setiap lab: 2 spine + 3 leaf. Image: `vrnetlab/cisco_iol:L2-17.18.02`. Username, password dan enable: `admin/admin`.

## 1. Persiapan sekali saja
Setelah ZIP diekstrak ke lokasi berikut, jalankan:
```bash
cd /home/rabilnugraha/03-containerlab-ansible-playbook/03
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
ansible-galaxy collection install -r collections/requirements.yml
```
Pada terminal baru, aktifkan kembali:
```bash
source /home/rabilnugraha/03-containerlab-ansible-playbook/03/.venv/bin/activate
```
Host harus Linux dengan Docker, Containerlab, image L2 yang sudah tersedia, Python 3.11/3.12 dan venv. Jalankan Ansible pada host yang sama.

## 2. Pilih latihan
| Folder | Pekerjaan | IP spine1, spine2, leaf1, leaf2, leaf3 |
|---|---|---|
| 01-show-running-config | Menampilkan konfigurasi | 172.31.31.11–15 |
| 02-add-vlan50 | Membuat VLAN 50 | 172.31.32.11–15 |
| 03-delete-vlan50 | Menghapus VLAN 50 | 172.31.33.11–15 |

IP tetap dan sudah ada di inventory. Subnet /24 berbeda antar folder. Periksa apakah subnet bertabrakan dengan jaringan host/VPN; jika perlu ubah topology dan inventory bersama. Peserta pada host yang sama perlu VM terpisah atau nama lab/network dan subnet berbeda.

## 3. Jalankan latihan
Contoh menambah VLAN:
```bash
cd /home/rabilnugraha/03-containerlab-ansible-playbook/03/02-add-vlan50
sudo containerlab deploy -t topology.clab.yml
```
Tunggu boot switch dan SSH siap, kemudian:
```bash
ansible-playbook playbook.yml
ansible switches -m cisco.ios.ios_command -a 'commands="show vlan brief"'
```
VLAN 50 bernama TRAINING seharusnya muncul pada lima switch.

Untuk menghapus VLAN pada **switch yang sama**, tetap berada di folder tambah VLAN dan jalankan:
```bash
ansible-playbook ../03-delete-vlan50/playbook.yml -i inventory.ini
ansible switches -m cisco.ios.ios_command -a 'commands="show vlan brief"'
```
VLAN 50 seharusnya hilang. Tidak perlu deploy lab hapus untuk langkah ini.

Untuk latihan penghapusan secara terpisah, ikuti README folder `03-delete-vlan50`: deploy lab baru, buat VLAN memakai `prepare-vlan50.yml`, lalu hapus memakai `playbook.yml`. Tiap folder memiliki perangkat sendiri; konfigurasi tidak otomatis berpindah antar lab.

## 4. Selesai
Dari folder lab yang dideploy:
```bash
sudo containerlab destroy -t topology.clab.yml
```
Reset bersih: tambahkan `--cleanup`; konfigurasi runtime/NVRAM akan dihapus.

## Catatan instructor
Playbook sengaja singkat. Show memiliki dua task agar jawaban tampil; tambah/hapus masing-masing satu task. Tidak ada backup file, assertion atau variabel tambahan. `vars.yml` disertakan tetapi belum dipakai. Penambahan/penghapusan VLAN hanya mengubah database VLAN, bukan konfigurasi trunk/access port. Port Ethernet0/0 khusus management, spine data-port E0/1–3, leaf E0/1–2. Host key checking dinonaktifkan untuk lab.

Sebelum kelas, jalankan dari setiap folder:
```bash
ansible-playbook playbook.yml --syntax-check
```
Opsional untuk tambah/hapus: `ansible-playbook playbook.yml --check --diff`. Preview membutuhkan switch reachable, tidak menyimpan perubahan, dan harus dilanjutkan run normal serta `show vlan brief`. Rentang dependency di requirements adalah baseline, bukan lockfile uji live.

Sintaks YAML dan konsistensi alamat telah diperiksa saat pembuatan. Deploy/eksekusi live serta Ansible syntax-check dengan collections belum diuji di host Anda.
