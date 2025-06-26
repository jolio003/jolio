<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AEROPORT SD - SDN 003 Rantai Palado</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f4f4f4; margin: 0; padding: 20px; }
    h1, h2, h3, h4 { text-align: center; margin: 5px 0; }
    .container { max-width: 1000px; margin: auto; background: #fff; padding: 20px; border-radius: 10px; }
    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    th, td { padding: 8px; border: 1px solid #ccc; text-align: center; }
    select, input { padding: 5px; }
    .btn { padding: 10px 15px; background: #007bff; color: white; border: none; cursor: pointer; border-radius: 5px; }
    .btn:hover { background: #0056b3; }
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
</head>
<body>
<div class="container">
  <h3>PEMERINTAH KABUPATEN MAMASA</h3>
  <h4>DINAS PENDIDIKAN DAN KEBUDAYAAN</h4>
  <h2>SDN 003 RANTEPALADO</h2>
  <h4>NPSN: 40601076</h4>
  <h1>AEROPORT SD</h1>
  <h2>Aplikasi Rapor Manual Kelas 1-6</h2>

  <label for="kelas">Pilih Kelas:</label>
  <select id="kelas" onchange="gantiKelas()">
    <option value="1">Kelas 1</option>
    <option value="2">Kelas 2</option>
    <option value="3">Kelas 3</option>
    <option value="4">Kelas 4</option>
    <option value="5">Kelas 5</option>
    <option value="6">Kelas 6</option>
  </select>

  <table id="rapor">
    <thead>
      <tr>
        <th>No</th>
        <th>Nama Siswa</th>
        <th>Matematika</th>
        <th>Bahasa Indonesia</th>
        <th>PJOK</th>
        <th>IPAS</th>
        <th>SBdK</th>
        <th>PKn</th>
       <th>BAHASA INGGRIS</th>
       <th>MULOK PERTANIAN</th>
       <th>MULOK PETERNAKAN</th>
        <th>Rata-rata</th>
      </tr>
    </thead>
    <tbody id="tabel-isi">
      <!-- Baris siswa akan dimasukkan lewat JavaScript -->
    </tbody>
  </table>
  <br>
  <button class="btn" onclick="tambahSiswa()">+ Tambah Siswa</button>
  <button class="btn" onclick="eksporExcel()">⬇️ Ekspor ke Excel</button>
</div>

<script>
  let dataRapor = {
    1: [], 2: [], 3: [], 4: [], 5: [], 6: []
  };
  let kelasAktif = 1;

  function gantiKelas() {
    kelasAktif = document.getElementById('kelas').value;
    tampilkanData();
  }

  function tambahSiswa() {
    const nama = prompt("Masukkan nama siswa:");
    if (nama) {
      dataRapor[kelasAktif].push({ nama, nilai: { mtk: '', indo: '', ips: '', sbdk: '', pkn: '' } });
      tampilkanData();
    }
  }

  function hitungRata2(nilai) {
    const n = [nilai.mtk, nilai.indo, nilai.ipa, nilai.ips, nilai.pkn].map(Number);
    if (n.some(isNaN)) return '';
    return (n.reduce((a, b) => a + b, 0) / n.length).toFixed(2);
  }

  function updateNilai(index, mapel, value) {
    dataRapor[kelasAktif][index].nilai[mapel] = value;
    tampilkanData();
  }

  function tampilkanData() {
    const tbody = document.getElementById('tabel-isi');
    tbody.innerHTML = '';
    dataRapor[kelasAktif].forEach((siswa, i) => {
      const rata2 = hitungRata2(siswa.nilai);
      const row = `<tr>
        <td>${i + 1}</td>
        <td>${siswa.nama}</td>
        <td><input type="number" value="${siswa.nilai.mtk}" onchange="updateNilai(${i}, 'mtk', this.value)"></td>
        <td><input type="number" value="${siswa.nilai.indo}" onchange="updateNilai(${i}, 'indo', this.value)"></td>
        <td><input type="number" value="${siswa.nilai.ipa}" onchange="updateNilai(${i}, 'ipa', this.value)"></td>
        <td><input type="number" value="${siswa.nilai.ips}" onchange="updateNilai(${i}, 'ips', this.value)"></td>
        <td><input type="number" value="${siswa.nilai.pkn}" onchange="updateNilai(${i}, 'pkn', this.value)"></td>
        <td>${rata2}</td>
      </tr>`;
      tbody.innerHTML += row;
    });
  }

  function eksporExcel() {
    const data = dataRapor[kelasAktif].map((s, i) => {
      return {
        'No': i + 1,
        'Nama': s.nama,
        'Matematika': s.nilai.mtk,
        'Bahasa Indonesia': s.nilai.indo,
        'IPA': s.nilai.ipa,
        'IPS': s.nilai.ips,
        'PKn': s.nilai.pkn,
        'Rata-rata': hitungRata2(s.nilai)
      }
    });
    const worksheet = XLSX.utils.json_to_sheet(data);
    const workbook = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(workbook, worksheet, `Kelas ${kelasAktif}`);
    XLSX.writeFile(workbook, `Rapor_Kelas_${kelasAktif}.xlsx`);
  }

  // Load awal
  tampilkanData();
</script>
</body>
</html>
