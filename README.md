# 👗 FashionStore — Website Penjualan Baju Online

Website toko baju berbasis HTML/CSS/JS yang terhubung ke Google Spreadsheet sebagai database produk dan penyimpanan pesanan.

---

## Struktur File

```
index.html   → Halaman utama website
style.css    → Tampilan & tema warna pink
script.js    → Logika produk, keranjang, dan checkout
README.md    → Dokumentasi ini
```

---

## Fitur

- Tampil produk otomatis dari Google Spreadsheet
- Filter produk berdasarkan kategori (Pria / Wanita / Anak)
- Pencarian produk real-time
- Keranjang belanja dengan update qty
- Form checkout dengan data nama, HP, alamat, metode bayar
- Pesanan tersimpan otomatis ke sheet **Pesanan** via Google Apps Script
- Fallback ke data produk statis jika spreadsheet belum dikonfigurasi

---

## Cara Setup Google Spreadsheet

### 1. Buat Spreadsheet

Buka [sheets.google.com](https://sheets.google.com), buat spreadsheet baru.  
Beri nama tab sheet pertama: `Produk`

Isi header di baris 1:

| A | B | C | D | E |
|---|---|---|---|---|
| Nama | Kategori | Harga | Stok | Gambar |

Isi data produk mulai baris 2. Kolom Gambar diisi URL foto.  
Kategori yang didukung: `Pria`, `Wanita`, `Anak`

### 2. Ambil Spreadsheet ID

Dari URL spreadsheet:
```
https://docs.google.com/spreadsheets/d/[SPREADSHEET_ID]/edit
```

### 3. Buat Google Sheets API Key

1. Buka [console.cloud.google.com](https://console.cloud.google.com)
2. Buat project → aktifkan **Google Sheets API**
3. Credentials → Create Credentials → **API Key**
4. Copy API Key

### 4. Buat Apps Script (untuk simpan pesanan)

1. Di spreadsheet → **Extensions → Apps Script**
2. Paste kode berikut:

```javascript
function doPost(e) {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sheet = ss.getSheetByName('Pesanan');
  if (!sheet) {
    sheet = ss.insertSheet('Pesanan');
    sheet.appendRow(['No Order','Tanggal','Nama','HP','Alamat','Produk','Total','Pembayaran','Status']);
  }
  const d = JSON.parse(e.postData.contents);
  sheet.appendRow([d.noOrder, d.tgl, d.nama, d.hp, d.alamat, d.items, d.total, d.bayar, d.status]);
  return ContentService.createTextOutput('OK');
}
```

3. **Deploy → New Deployment → Web App**
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Copy URL deployment

### 5. Set Spreadsheet ke Publik

Share → Anyone with the link → **Viewer**

### 6. Isi Konfigurasi di script.js

```javascript
const SPREADSHEET_ID  = 'isi_spreadsheet_id_kamu';
const API_KEY         = 'isi_api_key_kamu';
const APPS_SCRIPT_URL = 'isi_url_apps_script_kamu';
const WA_NUMBER       = '628xxxxxxxxxx'; // nomor WA toko
```

---

## Sheet Pesanan

Setiap checkout akan otomatis menambah baris baru di sheet `Pesanan`:

| No Order | Tanggal | Nama | HP | Alamat | Produk | Total | Pembayaran | Status |
|----------|---------|------|----|--------|--------|-------|------------|--------|
| ORD-xxx | ... | ... | ... | ... | ... | ... | Transfer Bank | Pending |

---

## Teknologi

- HTML5, CSS3, Vanilla JavaScript
- Google Sheets API v4 (baca produk)
- Google Apps Script Web App (simpan pesanan)
