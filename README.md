# DAFTAR-HADIR
Silahkan isi daftar hadir ini dengan lengkap
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Daftar Hadir</title>
<style>
body { font-family: Arial; background:#f4f6f9; margin:0; }
.container { max-width:800px; margin:20px auto; background:#fff; padding:20px; border-radius:10px; box-shadow:0 4px 10px rgba(0,0,0,0.1);} 
h1 { text-align:center; }
p { text-align:center; color:#555; }
label { font-weight:bold; display:block; margin-top:12px; }
input { width:100%; padding:10px; margin-top:5px; border-radius:6px; border:1px solid #ccc; font-size:16px; }

.ttd-box { border:1px solid #ccc; border-radius:6px; width:100%; height:140px; background:#fff; touch-action:none; }
canvas { width:100%; height:100%; }

button { margin-top:12px; width:100%; padding:12px; background:#007bff; color:#fff; border:none; border-radius:6px; font-size:16px; }

.hidden { display:none; }

 table { width:100%; margin-top:20px; border-collapse:collapse; }
 th, td { border:1px solid #ddd; padding:8px; text-align:center; font-size:14px; }
 th { background:#f0f0f0; }
 .ttd-img { width:100px; height:50px; object-fit:contain; }
</style>
</head>
<body>

<div class="container" id="formPage">
  <h1>DAFTAR HADIR</h1>
  <p>Silahkan isi dengan lengkap di bawah ini.</p>

  <form id="attendanceForm">
    <label>Nama Lengkap</label>
    <input type="text" id="nama" required>

    <label>Jabatan</label>
    <input type="text" id="jabatan" required>

    <label>Tanggal</label>
    <input type="date" id="tanggal" required>

    <label>Tanda Tangan</label>
    <div class="ttd-box">
      <canvas id="signaturePad"></canvas>
    </div>
    <button type="button" onclick="clearSignature()">Hapus TTD</button>

    <button type="submit">Simpan</button>
    <button type="button" onclick="showRekap()">Selesai</button>
  </form>
</div>

<div class="container hidden" id="rekapPage">
  <h1>REKAP DAFTAR HADIR</h1>

  <table id="attendanceTable">
    <thead>
      <tr>
        <th>No</th>
        <th>Nama</th>
        <th>Jabatan</th>
        <th>Tanggal</th>
        <th>TTD</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <button onclick="backForm()">Kembali</button>
</div>

<script>
const canvas = document.getElementById('signaturePad');
const ctx = canvas.getContext('2d');

function resizeCanvas(){
  canvas.width = canvas.offsetWidth;
  canvas.height = canvas.offsetHeight;
}
resizeCanvas();
window.addEventListener('resize', resizeCanvas);

let drawing = false;
let dataList = [];

function getPos(e){
  const rect = canvas.getBoundingClientRect();
  if(e.touches){
    return { x: e.touches[0].clientX - rect.left, y: e.touches[0].clientY - rect.top };
  }
  return { x: e.clientX - rect.left, y: e.clientY - rect.top };
}

function startDraw(e){ e.preventDefault(); drawing = true; }
function endDraw(e){ e.preventDefault(); drawing = false; ctx.beginPath(); }

function draw(e){
  if(!drawing) return;
  e.preventDefault();
  const pos = getPos(e);
  ctx.lineWidth = 2;
  ctx.lineCap = 'round';
  ctx.strokeStyle = '#000';
  ctx.lineTo(pos.x, pos.y);
  ctx.stroke();
  ctx.beginPath();
  ctx.moveTo(pos.x, pos.y);
}

canvas.addEventListener('mousedown', startDraw);
canvas.addEventListener('mouseup', endDraw);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('touchstart', startDraw);
canvas.addEventListener('touchend', endDraw);
canvas.addEventListener('touchmove', draw);

function clearSignature(){ ctx.clearRect(0,0,canvas.width,canvas.height); }

const form = document.getElementById('attendanceForm');
const tableBody = document.querySelector('#attendanceTable tbody');

form.addEventListener('submit', function(e){
  e.preventDefault();

  const data = {
    nama: document.getElementById('nama').value,
    jabatan: document.getElementById('jabatan').value,
    tanggal: document.getElementById('tanggal').value,
    ttd: canvas.toDataURL()
  };

  dataList.push(data);

  form.reset();
  clearSignature();
});

function showRekap(){
  document.getElementById('formPage').classList.add('hidden');
  document.getElementById('rekapPage').classList.remove('hidden');

  tableBody.innerHTML = '';

  dataList.forEach((d, i)=>{
    const row = tableBody.insertRow();
    row.innerHTML = `
      <td>${i+1}</td>
      <td>${d.nama}</td>
      <td>${d.jabatan}</td>
      <td>${d.tanggal}</td>
      <td><img src="${d.ttd}" class="ttd-img"></td>
    `;
  });
}

function backForm(){
  document.getElementById('rekapPage').classList.add('hidden');
  document.getElementById('formPage').classList.remove('hidden');
}
</script>
</body>
</html>
