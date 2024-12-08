Карта города Костанай, созданная Элиной Бухаршиной!
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
        const map = L.map('map').setView([53.214, 63.624], 12); // Центр карты — Костанай

        // Добавление слоя карты OpenStreetMap
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Районы в виде кругов
        const districts = [
            {
                name: "Микрорайон Наурыз",
                coords: [53.1855, 63.6179],
                radius: 1000, // Радиус в метрах
                color: "blue"
            },
            {
                name: "ЖК Юбилейный",
                coords: [53.24269168341029, 63.61414706611485],
                radius: 1000, // Радиус в метрах
                color: "green"
            },
            {
                name: "Центральный район",
                coords: [53.214, 63.624],
                radius: 800, // Радиус в метрах
                color: "red"
            }
        ];

        // Добавление кругов на карту
        districts.forEach(district => {
            L.circle(district.coords, {
                color: district.color,
                fillColor: district.color,
                fillOpacity: 0.4,
                radius: district.radius
            }).addTo(map)
              .bindPopup(`<b>${district.name}</b>`);
        });

        // Добавление обработки кликов на карту
        map.on('click', function(e) {
            const { lat, lng } = e.latlng;
            L.marker([lat, lng]).addTo(map)
                .bindPopup(`Координаты: ${lat.toFixed(4)}, ${lng.toFixed(4)}`)
                .openPopup();
        });
    </script>
</body>
</html>
