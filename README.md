# Smart-Park-Pro
The web application serves as a parking management and location tracking system, enabling users to efficiently manage parking spaces and track bus locations. It integrates a user-friendly interface with backend services to provide a seamless experience.

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Parking and Location Tracing</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-image: url('C:/Users/vedpa/Desktop/firstimage.jpg');
            background-size: cover;
            background-attachment: fixed;
            background-color: #f4f4f4;
        }

        header {
            background-color: rgba(0, 0, 0, 0.8);
            color: #fff;
            padding: 1rem 0;
            text-align: center;
        }

        .container {
            width: 90%;
            max-width: 800px;
            margin: 2rem auto;
            background-color: rgba(255, 255, 255, 0.9);
            padding: 2rem;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .form-group {
            margin-bottom: 1.5rem;
        }

        .form-group label {
            display: block;
            font-weight: bold;
            margin-bottom: 0.5rem;
        }

        .form-group input {
            width: 100%;
            padding: 0.5rem;
            border: 1px solid #ccc;
            border-radius: 4px;
        }

        .btn {
            display: inline-block;
            padding: 0.5rem 1rem;
            margin: 0.5rem 0;
            color: #fff;
            background-color: #007bff;
            border: none;
            border-radius: 4px;
            text-decoration: none;
            cursor: pointer;
        }

        .btn:hover {
            background-color: #0056b3;
        }

        .options {
            display: flex;
            justify-content: space-around;
            margin-top: 2rem;
        }

        .options a {
            flex: 1;
            margin: 0 1rem;
            text-align: center;
        }

        #map-container {
            display: none;
            margin-top: 2rem;
            padding: 1rem;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
        }

        #map {
            width: 100%;
            height: 400px;
            border: 1px solid #ccc;
            border-radius: 8px;
        }

        #map-info {
            margin-top: 1rem;
            text-align: left;
        }

        .map-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 10px;
            margin: 1rem auto;
            max-width: 400px;
        }

        .map-cell {
            width: 50px;
            height: 50px;
            border: 1px solid #ccc;
            border-radius: 4px;
            background-color: #d3d3d3;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 14px;
        }

        .available {
            background-color: green;
            color: white;
        }

        .occupied {
            background-color: red;
            color: white;
        }
    </style>
</head>
<body>
    <header>
        <h1>Parking and Location Tracing</h1>
    </header>

    <div class="container">
        <!-- Personal Details -->
        <div id="personal-details">
            <h2>Enter Personal Details</h2>
            <div class="form-group">
                <label for="name">Name:</label>
                <input type="text" id="name" placeholder="Enter your name">
            </div>
            <div class="form-group">
                <label for="mobile">Mobile Number:</label>
                <input type="text" id="mobile" placeholder="Enter your mobile number">
            </div>
            <div class="form-group">
                <label for="email">Gmail ID:</label>
                <input type="email" id="email" placeholder="Enter your Gmail ID">
            </div>
            <button class="btn" onclick="showOptions()">Next</button>
        </div>

        <!-- Options -->
        <div id="options" style="display: none;">
            <h2>Choose an Option</h2>
            <div class="options">
                <a href="#" class="btn" onclick="showParking()">Parking</a>
                <a href="#" class="btn" onclick="showTracking()">Tracking</a>
            </div>
        </div>

        <!-- Parking -->
        <div id="parking" style="display: none;">
            <h2>Type Of Your Vehicle</h2>
            <div class="options">
                <button class="btn" onclick="showMap('2-wheeler')">2 Wheeler</button>
                <button class="btn" onclick="showMap('3-wheeler')">3 Wheeler</button>
                <button class="btn" onclick="showMap('4-wheeler')">4 Wheeler</button>
            </div>
        </div>

        <!-- Map Interface -->
        <div id="map-container">
            <h2>Parking Map</h2>
            <div id="map"></div>
            <div id="map-info">
                <p>Green: Available Spaces</p>
                <p>Red: Occupied Spaces</p>
            </div>
            <div class="map-grid" id="map-grid">
                <!-- Dynamic Parking Spaces -->
            </div>
        </div>

        <!-- Tracking -->
        <div id="tracking" style="display: none;">
            <h2>Bus Location Tracking</h2>
            <div>
                <label for="bus-number">Enter Bus Number:</label>
                <input type="text" id="bus-number" placeholder="Enter bus number">
                <button class="btn" onclick="trackBus()">Track</button>
            </div>
            <p id="tracking-result"></p>
        </div>
    </div>

    <script src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCU5ozcioKMVHEphllDfe_J5xax3LEnOPQ&libraries=places"></script>
    <script>
        function showOptions() {
            document.getElementById('personal-details').style.display = 'none';
            document.getElementById('options').style.display = 'block';
        }

        function showParking() {
            document.getElementById('options').style.display = 'none';
            document.getElementById('parking').style.display = 'block';
        }

        function showMap(vehicleType) {
            document.getElementById('parking').style.display = 'none';
            document.getElementById('map-container').style.display = 'block';
            initMap(vehicleType);
        }

        function showTracking() {
            document.getElementById('options').style.display = 'none';
            document.getElementById('tracking').style.display = 'block';
        }

        function trackBus() {
            const busNumber = document.getElementById('bus-number').value;
            document.getElementById('tracking-result').textContent = Tracking live location for bus number: ${busNumber};
        }

        function initMap(vehicleType) {
            const userLocation = { lat: 22.3080, lng: 73.1800 };
            const map = new google.maps.Map(document.getElementById("map"), {
                center: userLocation,
                zoom: 16,
            });
            new google.maps.Marker({
                position: userLocation,
                map: map,
                title: "Your Location (Near Parul University)",
            });

            const parkingSpaces = generateParkingSpaces(vehicleType);
            const mapGrid = document.getElementById('map-grid');
            mapGrid.innerHTML = '';

            parkingSpaces.forEach(space => {
                const cell = document.createElement('div');
                cell.className = map-cell ${space.status};
                cell.textContent = space.number;
                cell.onclick = () => selectParkingSpot(space);
                mapGrid.appendChild(cell);

                const spotLocation = { lat: space.latitude, lng: space.longitude };
                new google.maps.Marker({
                    position: spotLocation,
                    map: map,
                    title: Spot ${space.number} (${space.status === 'available' ? 'Available' : 'Reserved'}),
                    icon: space.status === 'available'
                        ? "http://maps.google.com/mapfiles/ms/icons/green-dot.png"
                        : "http://maps.google.com/mapfiles/ms/icons/red-dot.png",
                });
            });
        }

        function generateParkingSpaces(type) {
            const spaces = [];
            for (let i = 1; i <= 25; i++) {
                spaces.push({
                    number: ${type[0]}-${i},
                    status: Math.random() > 0.5 ? 'available' : 'occupied',
                    latitude: 22.3080 + (Math.random() - 0.5) * 0.002,
                    longitude: 73.1800 + (Math.random() - 0.5) * 0.002,
                });
            }
            return spaces;
        }

        function selectParkingSpot(space) {
            if (space.status === 'occupied') {
                alert('This spot is occupied. Please select another spot.');
                return;
            }
            alert(You selected spot ${space.number}. Proceed to park.);
            // Add further functionality here if needed
        }
    </script>
</body>
</html>
