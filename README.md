```  Nama:  Daffa Syahrandy Husain```

```  NIM:   2409116069```

# 1. Mengimport library yang digunakan dalam program ini
``` ruby
import csv
from prettytable import PrettyTable
import datetime
import pwinput
from colorama import Fore, Style
```

# 2. Membuat variabel untuk file menu csv
``` ruby
menu_pagi = "pagi.csv"
menu_siang = "siang.csv"
menu_malam = "malam.csv"
menu_vip = "vip.csv"
```
# 3. Membuat data user untuk login
``` ruby
data_user = [
    {"username": "dapa", "password": "123", "role": "VIP", "saldo": 1000000},
    {"username": "user", "password": "123", "role": "Biasa", "saldo": 500000}
]
```
# 4. Membuat variabel untuk memastikan hanya sekali pakai
``` ruby
voucher_digunakan = False
```
# 5. Membuat function def waktu untuk menentukan jam sesuai sistem
``` ruby
def waktu():
    jam = datetime.datetime.now().hour
    if 6 <= jam < 12:
        return "pagi"
    elif 12 <= jam < 18:
        return "siang"
    elif 18 <= jam < 24:
        return "malam"
    else:
        return None
```
# 6. Membuat functionn def untuk menampilkan menu role biasa
``` ruby
def tampil_menu_biasa():
    waktu_sekarang = waktu()
    table = PrettyTable()

    if waktu_sekarang == "pagi":
        try:
            with open(menu_pagi, "r") as f:
                reader = csv.reader(f)
                data_menu = list(reader)
            table.field_names = ["Menu", "Harga"]
            for row in data_menu:
                table.add_row(row)
            print("+----------------------+")
            print("|      Menu Pagi      |")
            print("+----------------------+")
            print(table)
        except FileNotFoundError:
            print("File menu pagi tidak ditemukan.")
    elif waktu_sekarang == "siang":
        try:
            with open(menu_siang, "r") as f:
                reader = csv.reader(f)
                data_menu = list(reader)
            table.field_names = ["Menu", "Harga"]
            for row in data_menu:
                table.add_row(row)
            print("+----------------------+")
            print("|      Menu Siang      |")
            print("+----------------------+")
            print(table)
        except FileNotFoundError:
            print("File menu siang tidak ditemukan.")
    elif waktu_sekarang == "malam":
        try:
            with open(menu_malam, "r") as f:
                reader = csv.reader(f)
                data_menu = list(reader)
            table.field_names = ["Menu", "Harga"]
            for row in data_menu:
                table.add_row(row)
            print("+----------------------+")
            print("|      Menu Malam      |")
            print("+----------------------+")
            print(table)
        except FileNotFoundError:
            print("File menu malam tidak ditemukan.")
    else:
        print("Restoran Sedang Tutup")
```
# 7. Membuat def function untuk menampilkan menu role vip
``` ruby
def tampil_menu_vip(user):
    if user["role"] != "VIP":
        print(Fore.RED,"\nAkses ditolak. Menu VIP hanya untuk pengguna VIP.", Style.RESET_ALL)
        return

    try:
        with open(menu_vip, "r") as f:
            reader = csv.reader(f)
            data_menu = list(reader)
        table = PrettyTable()
        table.field_names = ["Menu", "Harga"]
        for row in data_menu:
            table.add_row(row)
        print("+----------------------------------+")
        print("|            Menu VIP              |")
        print("+----------------------------------+")
        print(table)
    except FileNotFoundError:
        print("File menu VIP tidak ditemukan.")
```
# 8. Membuat function def pesan untuk user memesan menu
``` ruby
def pesan(user):
    global voucher_digunakan

    waktu_sekarang = waktu()
    if user["role"] == "VIP":
        tampil_menu_vip(user)
    else:
        tampil_menu_biasa()

    pesanan = input("Masukkan pesanan : ").split(',')
    pesanan = [item.strip().lower() for item in pesanan]

    # Tentukan file menu berdasarkan waktu, jika tidak VIP
    if waktu_sekarang == "pagi":
        menu_file = menu_pagi
    elif waktu_sekarang == "siang":
        menu_file = menu_siang
    elif waktu_sekarang == "malam":
        menu_file = menu_malam
    else:
        print("Restoran Sedang Tutup")
        return

    try:
        # Jika pengguna VIP, pastikan membuka file menu VIP terlepas dari waktu
        if user["role"] == "VIP":
            menu_file = menu_vip

        with open(menu_file, "r", encoding="utf-8") as f:
            reader = csv.reader(f)
            data_menu = [row for row in reader if row]
    except FileNotFoundError:
        print("File menu tidak ditemukan.")
        return

    total_harga = 0
    for pesanan_item in pesanan:
        menu_ditemukan = False
        for row in data_menu:
            menu_nama, menu_harga = row[0].strip().lower(), row[1].strip()
            if menu_nama == pesanan_item:
                harga = int(menu_harga)
                total_harga += harga
                menu_ditemukan = True
                break

        if not menu_ditemukan:
            print(f"Menu '{pesanan_item}' tidak ditemukan.")

    if user["role"] == "VIP" and not voucher_digunakan:
        gunakan_voucher = input("Apakah Anda ingin menggunakan voucher (y/n)? ").strip().lower()
        if gunakan_voucher == "y":
            voucher_diskon = total_harga * 0.2
            total_harga -= voucher_diskon
            voucher_digunakan = True
            print(Fore.GREEN, f"\nVoucher berhasil digunakan. Diskon sebesar {int(voucher_diskon)}.", Style.RESET_ALL)

    if total_harga > 0:
        if user["saldo"] >= total_harga:
            user["saldo"] -= total_harga
            print(f"Total harga pesanan: {int(total_harga)}. Saldo tersisa: {user['saldo']}")
        else:
            print(Fore.RED, f"\nSaldo tidak cukup untuk membayar total pesanan sebesar {int(total_harga)}.", Style.RESET_ALL)
```
# 9. Membuat function def login untuk user login sebelum mengakses tampilan awal
``` ruby
def login():
    kesempatan = 3
    while kesempatan > 0:
        username = input("Masukkan username: ")
        password = pwinput.pwinput("Masukkan password: ", mask="*")

        for user in data_user:
            if user["username"] == username and user["password"] == password:
                print(Fore.GREEN,f"\nSelamat datang, {username} ({user['role']})", Style.RESET_ALL)
                return user

        kesempatan -= 1
        if kesempatan > 0:
            print(Fore.RED,f"\nUsername atau password salah. Kesempatan tersisa: {kesempatan}", Style.RESET_ALL)
        else:
            print(Fore.RED,"\nAnda telah gagal login 3 kali. Akun tidak dapat digunakan lagi.", Style.RESET_ALL)
            return None
```
# 10. Membuat function def main untuk menampilkan tampilan awal dan memanggil untuk memulai program
``` ruby
def main():
    user = login()
    if not user:
        return

    while True:
        print("\nMenu:")
        print("1. Pesan")
        print("2. Tampilkan Menu Biasa")
        print("3. Tampilkan Menu VIP")
        print("4. Keluar")

        pilihan = input("Masukkan pilihan: ")

        if pilihan == "1":
            pesan(user)
        elif pilihan == "2":
            tampil_menu_biasa()
        elif pilihan == "3":
            tampil_menu_vip(user)
        elif pilihan == "4":
            print("Terima kasih telah berkunjung!")
            break
        else:
            print("Pilihan tidak valid. Silakan pilih lagi.")


main()
```
