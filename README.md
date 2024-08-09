
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flexible Timer with Vibration</title>
    <!-- Bootstrap CSS -->
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <!-- Custom CSS -->
    <link href="styles.css" rel="stylesheet">
</head>
<body>
    <style>
        body {
    font-family: Arial, sans-serif;
    background-color: #000;
    color: #ffffff;
}

.timer-container {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
    border: 2px solid #ff007f;
    position: relative;
    overflow: hidden;
    margin: auto;
    max-width: 600px;
}

#timer {
    font-size: 3em;
    margin-bottom: 20px;
    color: green;
}

.btn-custom {
    padding: 10px 20px;
    margin: 5px;
    font-size: 1em;
    border-radius: 5px;
    cursor: pointer;
    background: linear-gradient(135deg, #ff007f, #00d2ff);
    color: white;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
    position: relative;
    overflow: hidden;
    transition: background 0.3s, box-shadow 0.3s;
}

.btn-custom:hover {
    background: linear-gradient(135deg, #00d2ff, #ff007f);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.7);
}

.btn-custom:active {
    background: linear-gradient(135deg, #ff007f, #00d2ff);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.7);
}

.btn-custom:disabled {
    background: #555;
    color: #888;
    cursor: not-allowed;
    box-shadow: none;
}

@keyframes blink {
    0% { background-color: #ff007f; }
    50% { background-color: #00d2ff; }
    100% { background-color: #ff007f; }
}

.btn-blink {
    animation: blink 1s infinite;
}

@keyframes rgbLight {
    0% { box-shadow: 0 0 10px rgba(255, 0, 255, 0.8); }
    50% { box-shadow: 0 0 10px rgba(0, 255, 255, 0.8); }
    100% { box-shadow: 0 0 10px rgba(255, 0, 255, 0.8); }
}

.btn-rgb {
    animation: rgbLight 1.5s infinite alternate;
}

.input-container {
    margin-bottom: 20px;
}

.input-container label {
    margin-right: 10px;
    font-size: 1em;
    color: #ffffff;
}

.form-control {
    background: #333;
    border: 1px solid #444;
    color: #ffffff;
    border-radius: 5px;
}

.form-control:focus {
    outline: none;
    border-color: #ff007f;
    background: #222;
}

#vibration-alert {
    display: none;
    margin-top: 20px;
    font-size: 1.2em;
    color: #ff007f;
    text-shadow: 0 0 5px rgba(255, 0, 255, 0.8);
}

#slide-to-stop {
    display: none;
    margin-top: 20px;
    padding: 10px;
    background: linear-gradient(135deg, #00d2ff, #ff007f);
    color: white;
    border-radius: 5px;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
    transition: background 0.3s;
}

#slide-to-stop:hover {
    background: linear-gradient(135deg, #ff007f, #00d2ff);
}

#clock {
    font-weight: bold;
    padding-right: 20px;
    size: 20px;
}

h1 {
    display: none;
}

#clock, #battery {
    font-weight: bold;
    margin-bottom: 10px;
}
    </style>
    <center>
        <div class="container">
            <div class="timer-container">
                <h2 id="timer">00:00:00.000</h2>
                <div class="input-container">
                    <label for="minutes">Minutes:</label>
                    <input type="number" id="minutes" class="form-control" value="0" min="0" max="59">
                    <label for="seconds">Seconds:</label>
                    <input type="number" id="seconds" class="form-control" value="0" min="0" max="59">
                    <label for="milliseconds">Milliseconds:</label>
                    <input type="number" id="milliseconds" class="form-control" value="0" min="0">
                </div>
                <div class="buttons">
                    <button id="start" class="btn btn-custom">Start</button>
                    <button id="stop" class="btn btn-custom" disabled>Stop</button>
                    <button id="resume" class="btn btn-custom" disabled>Resume</button>
                    <button id="reset" class="btn btn-custom" disabled>Reset</button>
                </div>
                <div id="vibration-alert" style="display: none;">Your selected time is complete!</div>
                <div id="slide-to-stop" style="display: none;">Click to Stop</div>
            </div>
        </div>
        <div id="clock">
            <span id="hour">00</span>:<span id="minute">00</span>:<span id="second">00</span> <span id="ampm">AM</span>
        </div>
        <div id="battery-status">
            Battery: <span id="battery-percentage">0%</span>
            <span id="charging-icon" style="display: none;">⚡</span>
            <div id="battery-warning" style="display: none; color: #ff007f; font-size: 1.2em;">
                YOU NEED TO CHARGE YOUR PHONE
            </div>
        </div>
    </center>
    <!-- Bootstrap JS and dependencies -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
    <!-- Custom JS -->
    <script>
       let timer;
let isRunning = false;
let elapsedTime = 0;
let countdownTime = 0;
let startTime;
let remainingTime;
let continuousMode = false;

function updateTimerDisplay() {
    if (continuousMode) {
        elapsedTime += Date.now() - startTime;
        startTime = Date.now();

        const hours = Math.floor(elapsedTime / 3600000).toString().padStart(2, '0');
        const minutes = Math.floor((elapsedTime % 3600000) / 60000).toString().padStart(2, '0');
        const seconds = Math.floor((elapsedTime % 60000) / 1000).toString().padStart(2, '0');
        const milliseconds = (elapsedTime % 1000).toString().padStart(3, '0');
        document.getElementById('timer').textContent = `${hours}:${minutes}:${seconds}.${milliseconds}`;
    } else {
        remainingTime = countdownTime - (Date.now() - startTime + elapsedTime);

        if (remainingTime <= 0) {
            clearInterval(timer);
            document.getElementById('timer').textContent = "00:00:00.000";
            return;
        }

        const hours = Math.floor(remainingTime / 3600000).toString().padStart(2, '0');
        const minutes = Math.floor((remainingTime % 3600000) / 60000).toString().padStart(2, '0');
        const seconds = Math.floor((remainingTime % 60000) / 1000).toString().padStart(2, '0');
        const milliseconds = (remainingTime % 1000).toString().padStart(3, '0');
        document.getElementById('timer').textContent = `${hours}:${minutes}:${seconds}.${milliseconds}`;
    }
}

function startTimer() {
    if (isRunning) return;

    const minutes = parseInt(document.getElementById('minutes').value) || 0;
    const seconds = parseInt(document.getElementById('seconds').value) || 0;
    const milliseconds = parseInt(document.getElementById('milliseconds').value) || 0;

    countdownTime = (minutes * 60000) + (seconds * 1000) + milliseconds;
    if (countdownTime <= 0) {
        continuousMode = true;
    } else {
        continuousMode = false;
    }

    startTime = Date.now();
    timer = setInterval(updateTimerDisplay, 10);
    isRunning = true;

    document.getElementById('start').disabled = true;
    document.getElementById('stop').disabled = false;
    document.getElementById('reset').disabled = false;
}

function stopTimer() {
    clearInterval(timer);
    isRunning = false;

    document.getElementById('start').disabled = false;
    document.getElementById('stop').disabled = true;
    document.getElementById('resume').disabled = false;
}

function resumeTimer() {
    if (isRunning) return;

    startTime = Date.now();
    timer = setInterval(updateTimerDisplay, 10);
    isRunning = true;

    document.getElementById('start').disabled = true;
    document.getElementById('stop').disabled = false;
    document.getElementById('resume').disabled = true;
}

function resetTimer() {
    clearInterval(timer);
    isRunning = false;
    elapsedTime = 0;

    document.getElementById('timer').textContent = "00:00:00.000";

    document.getElementById('start').disabled = false;
    document.getElementById('stop').disabled = true;
    document.getElementById('resume').disabled = true;
    document.getElementById('reset').disabled = true;
}

function updateClock() {
    const now = new Date();
    let hour = now.getHours();
    const minute = now.getMinutes();
    const second = now.getSeconds();
    const ampm = hour >= 12 ? 'PM' : 'AM';

    // Convert 24-hour time to 12-hour time
    hour = hour % 12;
    hour = hour ? hour : 12; // the hour '0' should be '12'

    document.getElementById('hour').textContent = String(hour).padStart(2, '0');
    document.getElementById('minute').textContent = String(minute).padStart(2, '0');
    document.getElementById('second').textContent = String(second).padStart(2, '0');
    document.getElementById('ampm').textContent = ampm;
}

function updateBatteryStatus(battery) {
    const batteryPercentage = Math.round(battery.level * 100);
    document.getElementById('battery-percentage').textContent = batteryPercentage + '%';

    // Show warning message if battery is below 20%
    if (battery.level < 0.2) {
        document.getElementById('battery-warning').style.display = 'block';
    } else {
        document.getElementById('battery-warning').style.display = 'none';
    }

    // Show ⚡ icon if charging
    if (battery.charging) {
        document.getElementById('charging-icon').style.display = 'inline';
    } else {
        document.getElementById('charging-icon').style.display = 'none';
    }
}

function handleBattery(battery) {
    updateBatteryStatus(battery);

    // Listen for battery changes
    battery.addEventListener('levelchange', () => updateBatteryStatus(battery));
    battery.addEventListener('chargingchange', () => updateBatteryStatus(battery));
}

function initializeBatteryStatus() {
    if ('getBattery' in navigator) {
        navigator.getBattery().then(handleBattery);
    } else {
        document.getElementById('battery-percentage').textContent = 'Battery API not supported';
    }
}

// Update the time every second
setInterval(updateClock, 1000);

// Initial call to display time immediately
updateClock();

// Initialize battery status
initializeBatteryStatus();

// Add event listeners for timer controls
document.getElementById('start').addEventListener('click', startTimer);
document.getElementById('stop').addEventListener('click', stopTimer);
document.getElementById('resume').addEventListener('click', resumeTimer);
document.getElementById('reset').addEventListener('click', resetTimer);
    </script>
