<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>syevita</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background: url('apeni.JPG');
        background-size: cover;
        background-repeat: no-repeat;
        padding: 20px;
        color: #ddd;
    }
    .container {
        max-width: 600px;
        margin: auto;
        background: #011020;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h1 {
        text-align: center;
    }
    input, button {
        width: 100%;
        padding: 10px;
        margin-top: 8px;
        margin-bottom: 15px;
        font-size: 16px;
    }
    button {
        background: navy;
        color: white;
        border: none;
        cursor: pointer;
        border-radius: 5px;
    }
    button:hover {
        background: #123255;
    }
    .summary {
        background: navy;
        padding: 15px;
        border-radius: 5px;
        margin-bottom: 20px;
    }
    ul {
        list-style: none;
        padding: 0;
    }
    li {
        padding: 8px;
        border-bottom: 1px solid #ddd;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    .income { color: green; }
    .expense { color: red; }
    .hapus {
        background: red;
        color: white;
        border: none;
        padding: 5px 8px;
        border-radius: 4px;
        cursor: pointer;
        font-size: 12px;
    }
</style>
</head>
<body>

<div class="container">
    <h1>KEUANGAN V</h1>

    <div class="summary">
        <p><strong>Saldo Tersisa:</strong> Rp <span id="saldo">0</span></p>
        <p><strong>Total Pengeluaran Bulan Ini:</strong> Rp <span id="totalPengeluaran">0</span></p>
    </div>

    <input type="text" id="keterangan" placeholder="Keterangan">
    <input type="number" id="jumlah" placeholder="Jumlah uang">
    
    <button onclick="tambahPemasukan()">Tambah Pemasukan</button>
    <button onclick="tambahPengeluaran()">Tambah Pengeluaran</button>

    <h3>Riwayat Transaksi</h3>
    <ul id="riwayat"></ul>
</div>

<script>
    let saldo = JSON.parse(localStorage.getItem("saldo")) || 0;
    let totalPengeluaran = JSON.parse(localStorage.getItem("totalPengeluaran")) || 0;
    let riwayat = JSON.parse(localStorage.getItem("riwayat")) || [];

    function simpanData() {
        localStorage.setItem("saldo", saldo);
        localStorage.setItem("totalPengeluaran", totalPengeluaran);
        localStorage.setItem("riwayat", JSON.stringify(riwayat));
    }

    function updateTampilan() {
        saldoSpan.textContent = saldo;
        totalPengeluaranSpan.textContent = totalPengeluaran;

        riwayatUl.innerHTML = "";
        riwayat.forEach((item, index) => {
            const li = document.createElement("li");
            li.className = item.tipe;

            li.innerHTML = `
                <span>${item.ket} : Rp ${item.jumlah}</span>
                <button class="hapus" onclick="hapusTransaksi(${index})">Hapus</button>
            `;

            riwayatUl.appendChild(li);
        });
    }

    function tambahPemasukan() {
        if (!keterangan.value || !jumlah.value) return alert("Data belum lengkap");

        saldo += +jumlah.value;
        riwayat.push({ ket: keterangan.value, jumlah: +jumlah.value, tipe: "income" });

        simpanData();
        updateTampilan();
        resetInput();
    }

    function tambahPengeluaran() {
        if (!keterangan.value || !jumlah.value) return alert("Data belum lengkap");

        saldo -= +jumlah.value;
        totalPengeluaran += +jumlah.value;
        riwayat.push({ ket: keterangan.value, jumlah: +jumlah.value, tipe: "expense" });

        simpanData();
        updateTampilan();
        resetInput();
    }

    function hapusTransaksi(index) {
        const item = riwayat[index];

        if (item.tipe === "income") {
            saldo -= item.jumlah;
        } else {
            saldo += item.jumlah;
            totalPengeluaran -= item.jumlah;
        }

        riwayat.splice(index, 1);
        simpanData();
        updateTampilan();
    }

    function resetInput() {
        keterangan.value = "";
        jumlah.value = "";
    }

    const saldoSpan = document.getElementById("saldo");
    const totalPengeluaranSpan = document.getElementById("totalPengeluaran");
    const riwayatUl = document.getElementById("riwayat");

    updateTampilan();
</script>

</body>
</html>

