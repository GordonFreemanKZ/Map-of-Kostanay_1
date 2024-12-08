<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Карта Костаная, созданная Элиной Бухаршиной!</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-routing-machine/3.2.12/leaflet-routing-machine.css" />
    <style>
        #map {
            height: 90vh; /* Карта занимает 90% экрана */
            margin: 0;
        }
        #controls {
            padding: 10px;
            background: #f8f9fa;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        input, button {
            margin: 5px;
            padding: 5px;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <!-- Блок с формой -->
    <div id="controls">
        <label>Точка A (лат, лон):</label>
        <input id="pointA" type="text" placeholder="Например, 53.214, 63.624">
        <label>Точка B (лат, лон):</label>
        <input id="pointB" type="text" placeholder="Например, 53.247, 63.600">
        <button onclick="buildRoute()">Построить маршрут</button>
    </div>

    <!-- Карта -->
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

        // Инициализация маршрута
        let control = L.Routing.control({
            waypoints: [],
            routeWhileDragging: true,
            language: 'ru',
            createMarker: (i, wp) => {
                return L.marker(wp.latLng).bindPopup(i === 0 ? "Точка A" : "Точка B");
            }
        }).addTo(map);

        // Функция для построения маршрута
        function buildRoute() {
            const pointA = document.getElementById('pointA').value.split(',').map(Number);
            const pointB = document.getElementById('pointB').value.split(',').map(Number);

            // Проверка корректности координат
            if (pointA.length === 2 && pointB.length === 2 && 
                !isNaN(pointA[0]) && !isNaN(pointA[1]) && 
                !isNaN(pointB[0]) && !isNaN(pointB[1])) {

                // Установка новых точек маршрута
                control.setWaypoints([
                    L.latLng(pointA[0], pointA[1]),
                    L.latLng(pointB[0], pointB[1])
                ]);

                // Центрирование карты
                map.setView(pointA, 13);
            } else {
                alert('Введите корректные координаты в формате "Широта, Долгота"');
            }
        }

        // Обработка кликов для подсказки координат
        map.on('click', function(e) {
            const { lat, lng } = e.latlng;
            L.marker([lat, lng]).addTo(map)
                .bindPopup(`Координаты: ${lat.toFixed(4)}, ${lng.toFixed(4)}`)
                .openPopup();
        });
    </script>
</body>
</html>
