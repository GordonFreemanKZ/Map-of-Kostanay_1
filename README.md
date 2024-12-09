<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <p>Карта Костаная, созданная Элиной Бухаршиной!</p>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-routing-machine/3.2.12/leaflet-routing-machine.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.css" />
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
        <button onclick="locateUser()">Моё местоположение</button>
    </div>

    <!-- Карта -->
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-routing-machine/3.2.12/leaflet-routing-machine.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.draw/1.0.4/leaflet.draw.js"></script>
    <script>
        // Создание карты
        const map = L.map('map').setView([53.214, 63.624], 12); // Центр карты — Костанай

        // Добавление слоя карты OpenStreetMap
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Добавление слоя для рисования
        const drawnItems = new L.FeatureGroup();
        map.addLayer(drawnItems);

        // Инструменты для рисования
        const drawControl = new L.Control.Draw({
            edit: {
                featureGroup: drawnItems
            },
            draw: {
                polygon: true,
                polyline: true,
                rectangle: true,
                circle: true,
                marker: true
            }
        });
        map.addControl(drawControl);

        // Обработка событий рисования
        map.on(L.Draw.Event.CREATED, function (event) {
            const layer = event.layer;
            drawnItems.addLayer(layer);
        });

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

        // Функция определения геопозиции пользователя
        function locateUser() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(position => {
                    const { latitude, longitude } = position.coords;

                    // Добавляем маркер на карту
                    L.marker([latitude, longitude]).addTo(map)
                        .bindPopup('Вы здесь!')
                        .openPopup();

                    // Центрируем карту на местоположении пользователя
                    map.setView([latitude, longitude], 14);

                    // Установка точки A как геопозиции пользователя
                    document.getElementById('pointA').value = `${latitude.toFixed(4)}, ${longitude.toFixed(4)}`;
                }, () => {
                    alert('Не удалось определить ваше местоположение.');
                });
            } else {
                alert('Геолокация не поддерживается вашим браузером.');
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
