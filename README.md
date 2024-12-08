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

        // Данные о районах
        const districts = [
            {
                name: "Микрорайон Наурыз",
                coords: [
                    [53.1900, 63.6100],
                    [53.1950, 63.6250],
                    [53.1800, 63.6300],
                    [53.1750, 63.6150]
                ],
                color: "blue"
            },
            {
                name: "Район Алтын Арман",
                coords: [
                    [53.2450, 63.6050],
                    [53.2500, 63.6200],
                    [53.2350, 63.6150],
                    [53.2300, 63.6000]
                ],
                color: "green"
            },
            {
                name: "Центральный район",
                coords: [
                    [53.2200, 63.6200],
                    [53.2250, 63.6300],
                    [53.2100, 63.6350],
                    [53.2050, 63.6250]
                ],
                color: "red"
            }
        ];

        // Добавление полигонов для каждого района
        districts.forEach(district => {
            L.polygon(district.coords, {
                color: district.color,
                weight: 4, // Толщина линии
                opacity: 0.8,
                fillOpacity: 0.1 // Легкая заливка внутри
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

