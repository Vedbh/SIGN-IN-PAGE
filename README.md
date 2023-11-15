
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registration & Login</title>
    
</head>
<body>
    <style>
        body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #1a1a1a;
}

.container {
    width: 100vw;
    height: 100vh;
    display: flex;  
    align-items: center;
    justify-content: center;
    background-position: center;
    background-size: cover;
    background-color: #1a1a1a;
}

form {
    width: 250px;
    padding: 20px;
    background-color: rgba(255, 255, 255, 0.8);
    border-radius: 8px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    background-color: silver;
}

input {
    width: 100%;
    padding: 05px;
    margin: 10px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
}
button {
    display: inline-block;
    padding: 10px 20px;
    font-size: 16px;
    font-weight: bold;
    text-align: center;
    text-decoration: none;
    cursor: pointer;
    border: 2px solid #3498db;
    color: #3498db;
    background-color: #fff;
    border-radius: 5px;
    transition: background-color 0.3s ease, color 0.3s ease;
}

button:hover {
    background-color: #3498db;
    color: #fff;
}
p{
    color: red;
    font-size: 10px;
}
    </style>
    <div class="container" style="background-image: url('crew-data-center-cyber-security-team-computer-engineers-working-double-exposure-blurry-interface-concept-protection-164673298.webp')">
        <!-- Registration Form -->
        <form id="registration-form">
            <h2>Register Your Id</h2>
            <input type="text" id="register-username" placeholder="Username" required>
            <input type="password" id="register-password" placeholder="Password" required>
            <p>0-9 Any Number In 8 Digits Password</p>
            <button type="button" onclick="register()">Register</button>

        </form>
        
        <!-- Login Form -->
        <form id="login-form" style="display: none;">
            <h2>confirm Login</h2>
            <input type="text" id="login-username" placeholder="Username" required>
            <input type="password" id="login-password" placeholder="Password" required>
           <center><button type="button" onclick="login()">Login</button>
     

    <script>
     // Mock database (replace with real database in real-world applications)
let users = {};

function register() {
    let username = document.getElementById('register-username').value;
    let password = document.getElementById('register-password').value;

    // Check if the user already exists
    if (users[username]) {
        alert('User already exists!');
    } else {
        // Check if the password is strong
        if (isStrongPassword(password)) {
            users[username] = password;
            alert('Registration successful!');
            
            // Switch to login form
            document.getElementById('registration-form').style.display = 'none';
            document.getElementById('login-form').style.display = 'block';
        } else {
            alert('Please enter a strong password.');
        }
    }
}

function isStrongPassword(password) {
    var regex = /^[0-9@$!%*?&]{8}$/;


    // Test the password against the regex
    return regex.test(password);
}
function login() {
    let username = document.getElementById('login-username').value;
    let password = document.getElementById('login-password').value;

    if (users[username] && users[username] === password) {
        window.location.href = 'welcome.html'; // Redirect to second page
    } else {
        alert('Invalid credentials or user not found!');
    }
}

    </script>
