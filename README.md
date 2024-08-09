
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flexible Timer with Vibration</title>
    <!-- Bootstrap CSS -->
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <!-- Custom CSS -->
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #000; /* Dark background for better RGB effect */
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
            padding-right: 20px; /* Optional: Adds some padding from the right edge */
            size:20px;
        }
        h1{
            display:none;
        }
    </style>
</head>
<body>
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

    function updateTime() {
        if (continuousMode) {
            elapsedTime += Date.now() - startTime;
            startTime = Date.now();
            
            // Update the timer display to the elapsed time for continuous mode
            const hours = Math.floor(elapsedTime / 3600000).toString().padStart(2, '0');
            const minutes = Math.floor((elapsedTime % 3600000) / 60000).toString().padStart(2, '0');
            const seconds = Math.floor((elapsedTime % 60000) / 1000).toString().padStart(2, '0');
            const milliseconds = (elapsedTime % 1000).toString().padStart(3, '0');
            document.getElementById('timer').textContent = `${hours}:${minutes}:${seconds}.${milliseconds}`;
        } else {
            remainingTime = countdownTime - (Date.now() - startTime + elapsedTime);

            if (remainingTime <= 0) {
                clearInterval(timer);
                if (countdownTime > 0) {  // Only trigger when a specific time is set
                    document.getElementById('timer').textContent = "00:00:00.000";
                    showVibrationAlert();
                }
                return;
            }

            const hours = Math.floor(remainingTime / 3600000).toString().padStart(2, '0');
            const minutes = Math.floor((remainingTime % 3600000) / 60000).toString().padStart(2, '0');
            const seconds = Math.floor((remainingTime % 60000) / 1000).toString().padStart(2, '0');
            const milliseconds = (remainingTime % 1000).toString().padStart(3, '0');
            document.getElementById('timer').textContent = `${hours}:${minutes}:${seconds}.${milliseconds}`;
        }
    }

    function showVibrationAlert() {
        document.getElementById('vibration-alert').style.display = 'block';
        document.getElementById('slide-to-stop').style.display = 'block';

        // Trigger vibration (500ms on, 500ms off, 500ms on, etc.)
        navigator.vibrate([500, 500, 500, 500, 500]); // Vibrate pattern [on, off, on, off, on]

        // Add event listener to stop vibration when "Slide to Stop" is clicked
        document.getElementById('slide-to-stop').addEventListener('click', stopVibration);
    }

    function stopVibration() {
        navigator.vibrate(0); // Stop vibration
        document.getElementById('vibration-alert').style.display = 'none';
        document.getElementById('slide-to-stop').style.display = 'none';
    }

    document.getElementById('start').addEventListener('click', function() {
        const minutes = parseInt(document.getElementById('minutes').value, 10) || 0;
        const seconds = parseInt(document.getElementById('seconds').value, 10) || 0;
        const milliseconds = parseInt(document.getElementById('milliseconds').value, 10) || 0;

        countdownTime = (minutes * 60 + seconds) * 1000 + milliseconds;
        startTime = Date.now();
        elapsedTime = 0;
        continuousMode = countdownTime <= 0;

        if (!isRunning) {
            timer = setInterval(updateTime, 10);
            isRunning = true;
            document.getElementById('start').disabled = true;
            document.getElementById('stop').disabled = false;
            document.getElementById('resume').disabled = true;
            document.getElementById('reset').disabled = false;
        }
    });

    document.getElementById('stop').addEventListener('click', function() {
        clearInterval(timer);
        elapsedTime += Date.now() - startTime;
        isRunning = false;
        document.getElementById('start').disabled = true;
        document.getElementById('stop').disabled = true;
        document.getElementById('resume').disabled = false;
        document.getElementById('reset').disabled = false;
    });

    document.getElementById('resume').addEventListener('click', function() {
        startTime = Date.now();
        timer = setInterval(updateTime, 10);
        isRunning = true;
        document.getElementById('start').disabled = true;
        document.getElementById('stop').disabled = false;
        document.getElementById('resume').disabled = true;
        document.getElementById('reset').disabled = false;
    });

    document.getElementById('reset').addEventListener('click', function() {
        clearInterval(timer);
        document.getElementById('timer').textContent = "00:00:00.000";
        elapsedTime = 0;
        isRunning = false;
        countdownTime = 0;
        continuousMode = false;
        document.getElementById('start').disabled = false;
        document.getElementById('stop').disabled = true;
        document.getElementById('resume').disabled = true;
        document.getElementById('reset').disabled = true;
    });
     function updateTime() {
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

        // Update the time every second
        setInterval(updateTime, 1000);

        // Initial call to display time immediately
        updateTime();
</script>
