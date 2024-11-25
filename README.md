# barbershop
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Barbershop</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f9;
            color: #333;
        }
        .container {
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }
        h1, h3 {
            text-align: center;
            color: #444;
        }
        .choices {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
        }
        .btn {
            display: inline-block;
            padding: 15px 25px;
            font-size: 16px;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .btn-primary {
            background-color: #007bff;
        }
        .btn-primary:hover {
            background-color: #0056b3;
        }
        .btn-secondary {
            background-color: #28a745;
        }
        .btn-secondary:hover {
            background-color: #1c7430;
        }
        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        input, select, button {
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            padding: 10px;
            text-align: left;
            border: 1px solid #ddd;
        }
        th {
            background-color: #f4f4f4;
        }
        .time-slot {
            padding: 10px;
            text-align: center;
            border-radius: 5px;
            margin: 5px;
            cursor: pointer;
        }
        .available {
            background-color: #28a745;
            color: white;
        }
        .unavailable {
            background-color: #dc3545;
            color: white;
            pointer-events: none;
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div id="home" class="container">
        <h1>Benvenuto nel Barbershop</h1>
        <p>Scegli la tua opzione:</p>
        <div class="choices">
            <button class="btn btn-primary" onclick="showClientPage()">Cliente</button>
            <button class="btn btn-secondary" onclick="showAdminLogin()">Amministratore</button>
        </div>
    </div>

    <!-- Cliente Page -->
    <div id="client-page" class="container hidden">
        <h1>Prenota un Appuntamento</h1>
        <form id="booking-form">
            <label for="name">Nome:</label>
            <input type="text" id="name" required>
            <label for="surname">Cognome:</label>
            <input type="text" id="surname" required>
            <label for="phone">Telefono:</label>
            <input type="text" id="phone" required>
            <label for="date">Data:</label>
            <input type="date" id="date" required onchange="updateTimeSlots()">
            <h3>Orari Disponibili:</h3>
            <div id="time-slots"></div>
            <button type="button" onclick="submitBooking()">Prenota</button>
        </form>
        <button onclick="goBackHome()">Torna indietro</button>
    </div>

    <!-- Conferma Prenotazione -->
    <div id="confirmation" class="container hidden">
        <h1>Prenotazione avvenuta con successo!</h1>
        <p>Puoi annullare la tua prenotazione entro 24 ore.</p>
        <button id="cancel-btn" class="btn btn-secondary" onclick="cancelBooking()">Annulla Prenotazione</button>
        <button onclick="goBackHome()">Torna alla Home</button>
    </div>

    <script>
        let bookings = [];
        let activeBooking = null;
        const availableTimes = ["09:00", "10:00", "11:00", "12:00"];

        function showClientPage() {
            document.getElementById('home').classList.add('hidden');
            document.getElementById('client-page').classList.remove('hidden');
        }

        function goBackHome() {
            document.getElementById('client-page').classList.add('hidden');
            document.getElementById('confirmation').classList.add('hidden');
            document.getElementById('home').classList.remove('hidden');
        }

        function updateTimeSlots() {
            const date = document.getElementById('date').value;
            const timeSlots = document.getElementById('time-slots');
            timeSlots.innerHTML = '';

            availableTimes.forEach(time => {
                const isUnavailable = bookings.some(b => b.date === date && b.time === time);
                const slot = document.createElement('div');
                slot.textContent = time;
                slot.classList.add('time-slot', isUnavailable ? 'unavailable' : 'available');
                if (!isUnavailable) {
                    slot.onclick = () => selectTimeSlot(time);
                }
                timeSlots.appendChild(slot);
            });
        }

        function selectTimeSlot(time) {
            document.getElementById('time-slots').querySelectorAll('.time-slot').forEach(slot => {
                slot.classList.remove('selected');
            });
            document.querySelector(`.time-slot:contains('${time}')`).classList.add('selected');
        }

        function submitBooking() {
            const name = document.getElementById('name').value;
            const surname = document.getElementById('surname').value;
            const phone = document.getElementById('phone').value;
            const date = document.getElementById('date').value;
            const selectedSlot = document.querySelector('.time-slot.available.selected');
            const time = selectedSlot ? selectedSlot.textContent : null;

            if (!time) {
                alert("Seleziona un orario disponibile!");
                return;
            }

            activeBooking = { name, surname, phone, date, time };
            bookings.push(activeBooking);

            document.getElementById('client-page').classList.add('hidden');
            document.getElementById('confirmation').classList.remove('hidden');

            setTimeout(() => {
                if (activeBooking) {
                    alert("Il periodo per annullare la prenotazione è scaduto.");
                    activeBooking = null;
                    document.getElementById('cancel-btn').classList.add('hidden');
                }
            }, 24 * 60 * 60 * 1000); // Timer di 24 ore
        }

        function cancelBooking() {
            bookings = bookings.filter(b => b !== activeBooking);
            activeBooking = null;
            alert("Prenotazione annullata con successo.");
            goBackHome();
        }
    </script>
</body>
</html>
