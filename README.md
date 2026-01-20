# Pemetaan-Cabang-Alkess
Web GIS pemetaan cabang alkes berbasis browser dengan update data dari Excel.
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
        body {
            margin: 0;
        }
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

<!-- PapaParse -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>

<script>
    /* ===============================
       BATAS WILAYAH JAWA TIMUR
       =============================== */
    const jatimBounds = [
        [-8.9, 111.0], // Barat Daya
        [-6.8, 114.5]  // Timur Laut
    ];

    // Inisialisasi peta (FULL LOCK)
    const map = L.map('map', {
        center: [-7.6, 112.5],
        zoom: 8,
        zoomControl: false,
        dragging: false,
        scrollWheelZoom: false,
        doubleClickZoom: false,
        boxZoom: false,
        keyboard: false,
        maxBounds: jatimBounds,
        maxBoundsViscosity: 1.0
    });

    // Tile layer
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap'
    }).addTo(map);

    // Paksa tampilan hanya Jawa Timur
    map.fitBounds(jatimBounds);

    /* ===============================
       LOAD DATA DARI EXCEL (CSV)
       =============================== */
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
