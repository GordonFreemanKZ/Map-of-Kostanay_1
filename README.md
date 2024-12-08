<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Карта Костаная</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        #map {
            height: 100vh; /* Карта занимает весь экран */
            margin: 0;
        }
    </style>
</head>
<body>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // Создание карты
        const map = L.map('map').setView([53.214, 63.624], 13); // Координаты Костаная

        // Добавление слоя карты OpenStreetMap
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Добавление метки на карте
        const kostanayMarker = L.marker([53.214, 63.624]).addTo(map)
            .bindPopup('Костанай, Казахстан')
            .openPopup();

        // Добавление круговой области
        const circle = L.circle([53.214, 63.624], {
            color: 'blue',
            fillColor: '#30a',
            fillOpacity: 0.3,
            radius: 1000 // Радиус в метрах
        }).addTo(map);

        // Обработка клика по карте
        map.on('click', function(e) {
            const { lat, lng } = e.latlng;
            L.marker([lat, lng]).addTo(map)
                .bindPopup(`Координаты: ${lat.toFixed(4)}, ${lng.toFixed(4)}`)
                .openPopup();
        });
    </script>
</body>
</html>
