<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Pemetaan Cabang Alkes Jawa Timur</title>

  <!-- Leaflet -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css">
  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

  <!-- SheetJS -->
  <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>

  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
    }

    .controls {
      padding: 12px;
      background: #f5f5f5;
    }

    #map {
      width: 100%;
      height: 90vh;
    }

    .file-info {
      font-size: 12px;
      color: #333;
      margin-top: 4px;
    }
  </style>
</head>
<body>

<div class="controls">
  <strong>Pemetaan Cabang Alkes Provinsi Jawa Timur</strong><br><br>

  <input type="file" id="upload" accept=".xlsx,.xls">
  <div id="fileInfo" class="file-info">Belum ada file diunggah</div>
</div>

<div id="map"></div>

<script>
  // ===============================
  // PETA TERKUNCI JAWA TIMUR
  // ===============================
  const jatimBounds = [
    [-9.2, 111.5],
    [-5.5, 114.8]
  ];

  const map = L.map('map', {
    center: [-7.5, 112.5],
    zoom: 8,
    minZoom: 8,
    maxZoom: 8,
    maxBounds: jatimBounds,
    maxBoundsViscosity: 1.0,
    zoomControl: false,
    scrollWheelZoom: false,
    dragging: false,
    doubleClickZoom: false,
    boxZoom: false,
    keyboard: false
  });

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap'
  }).addTo(map);

  let markersLayer = L.layerGroup().addTo(map);

  // ===============================
  // UPLOAD EXCEL (PASTI KEDETEKSI)
  // ===============================
  const uploadInput = document.getElementById('upload');
  const fileInfo = document.getElementById('fileInfo');

  uploadInput.addEventListener('change', function () {
    const file = uploadInput.files[0];

    if (!file) {
      fileInfo.textContent = "Tidak ada file dipilih";
      return;
    }

    fileInfo.textContent = "File dipilih: " + file.name;

    const reader = new FileReader();

    reader.onload = function (e) {
      const data = new Uint8Array(e.target.result);
      const workbook = XLSX.read(data, { type: 'array' });

      const sheet = workbook.Sheets[workbook.SheetNames[0]];
      const rows = XLSX.utils.sheet_to_json(sheet);

      markersLayer.clearLayers();

      let totalMarker = 0;

      rows.forEach(row => {
        const lat = parseFloat(row.Latitude);
        const lng = parseFloat(row.Longitude);
        const persen = row.Persentase;
        const nama = row["Kabupaten/Kota"];

        if (!isNaN(lat) && !isNaN(lng)) {
          totalMarker++;

          const marker = L.circleMarker([lat, lng], {
            radius: 8,
            color: 'red',
            fillColor: 'red',
            fillOpacity: 0.7
          }).addTo(markersLayer);

          marker.bindPopup(
            `<b>${nama}</b><br>Persentase: ${persen}%`
          );
        }
      });

      alert("Upload berhasil! Marker ditampilkan: " + totalMarker);
    };

    reader.readAsArrayBuffer(file);
  });
</script>

</body>
</html>
