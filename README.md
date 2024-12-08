<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Карта Костаная, созданная Элиной Бухаршиной!</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-routing-machine/3.2.12/leaflet-routing-machine.css" />
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
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-routing-machine/3.2.12/leaflet-routing-machine.min.js"></script>
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
                    [53.18666664078427, 63.61593258998828],
                    [53.1852836251683, 63.62126628816808],
                    [53.183339738046364, 63.62001355803299],
                    [53.18475826125334, 63.614600876596846]
                ],
                color: "blue"
            },
            {
                name: "ЖК Юбилейный",
                coords: [
                    [53.24787450770954, 63.600710187902656],
                    [53.24855834712629, 63.601735655610135],
                    [53.244110480123396, 63.62270547663778],
                    [53.23740987250295, 63.61708605648223]
                ],
                color: "green"
            },
            {
                name: "Центральный район",
                coords: [
                    [53.20683178870234, 63.617304493459045],
                    [53.20433112637876, 63.62228420836312],
                    [53.21442500141282, 63.63661115207711],
                    [53.217138566774594, 63.631210065676186]
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

        // Инициализация маршрута с помощью Leaflet Routing Machine
        const control = L.Routing.control({
            waypoints: [
                L.latLng(53.2068, 63.6173), // Точка начала (Центральный район)
                L.latLng(53.2478, 63.6007) // Точка назначения (ЖК Юбилейный)
            ],
            routeWhileDragging: true, // Перетаскивание маркеров изменяет маршрут
            show: true,
            language: 'ru', // Русский язык
            createMarker: (i, wp) => {
                return L.marker(wp.latLng).bindPopup(i === 0 ? "Точка отправления" : "Точка назначения");
            }
        }).addTo(map);

        // Добавление кликов на карту для динамического добавления точек маршрута
        map.on('click', function(e) {
            const { lat, lng } = e.latlng;
            control.spliceWaypoints(control.getWaypoints().length - 1, 0, L.latLng(lat, lng));
        });

        // Обработка кликов для добавления метки на карту
        map.on('contextmenu', function(e) {
            const { lat, lng } = e.latlng;
            L.marker([lat, lng]).addTo(map)
                .bindPopup(`Координаты: ${lat.toFixed(4)}, ${lng.toFixed(4)}`)
                .openPopup();
        });
    </script>
</body>
</html>

