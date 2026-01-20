<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Peta Jawa Timur</title>

    <!-- Leaflet CSS -->
    <link
        rel="stylesheet"
        href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    />

    <style>
        #map {
            width: 100%;
            height: 100vh;
        }
    </style>
</head>
<body>

<div id="map"></div>

<!-- Leaflet JS -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<!-- PapaParse (baca CSV dari Excel) -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>

<script>
    // Koordinat tengah Jawa Timur
    const jatimCenter = [-7.5361, 112.2384];

    // Inisialisasi peta (TANPA ZOOM)
    const map = L.map('map', {
        center: jatimCenter,
        zoom: 8,
        zoomControl: false,
        dragging: false,
        scrollWheelZoom: false,
        doubleClickZoom: false,
        boxZoom: false,
        keyboard: false
    });

    // Tile layer
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap'
    }).addTo(map);

    // Load data dari CSV (Excel)
    Papa.parse("data.csv", {
        download: true,
        header: true,
        complete: function(results) {
            results.data.forEach(row => {
                if (row.latitude && row.longitude) {
                    L.marker([row.latitude, row.longitude])
                        .addTo(map)
                        .bindPopup(`<b>${row.nama_kab}</b>`);
                }
            });
        }
    });
</script>

</body>
</html>
