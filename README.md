
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SARM - School Attendance & Records Manager</title>

<style>
* { box-sizing: border-box; }

body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #f1f5f9;
    color: #222;
}

header {
    background: #087443;
    color: white;
    padding: 20px;
    text-align: center;
}

.container {
    max-width: 650px;
    margin: 20px auto;
    padding: 15px;
}

.card {
    background: white;
    padding: 20px;
    margin: 15px 0;
    border-radius: 12px;
    box-shadow: 0 2px 8px #ddd;
}

h2 { color: #087443; }

input {
    width: 100%;
    padding: 13px;
    margin: 8px 0;
    border: 1px solid #ccc;
    border-radius: 6px;
}

button {
    background: #087443;
    color: white;
    border: none;
    padding: 13px 18px;
    border-radius: 6px;
    cursor: pointer;
    margin: 5px 0;
}

button:hover { background: #065c35; }

.secondary {
    background: #475569;
}

.hidden { display: none; }

.menu {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
}

.menu button { flex: 1; }

footer {
    text-align: center;
    padding: 20px;
    color: #666;
}
</style>
</head>

<body>

<header>
    <h1>SARM</h1>
    <p>School Attendance & Records Manager</p>
</header>

<div class="container">

<!-- WELCOME -->
<div id="welcome">
    <div class="card">
        <h2>Welcome to SARM</h2>
        <p>Register your school and manage your school records.</p>
        <button onclick="showRegister()">Create New School Account</button>
        <button class="secondary" onclick="showLogin()">School Admin Login</button>
    </div>
</div>

<!-- SCHOOL REGISTRATION -->
<div id="register" class="hidden">
    <div class="card">
        <h2>School Registration</h2>

        <form id="registerForm">
            <input id="schoolName" placeholder="School Name" required>
            <input id="schoolLga" placeholder="Local Government Area" required>
            <input id="adminName" placeholder="Admin Full Name" required>
            <input id="newUsername" placeholder="Create Admin Username" required>
            <input id="newPassword" type="password" placeholder="Create Admin Password" required>

            <button type="submit">Register School</button>
        </form>

        <button class="secondary" onclick="showWelcome()">Back</button>
    </div>
</div>

<!-- LOGIN -->
<div id="login" class="hidden">
    <div class="card">
        <h2>School Admin Login</h2>

        <form id="loginForm">
            <input id="username" placeholder="Admin Username" required>
            <input id="password" type="password" placeholder="Admin Password" required>

            <button type="submit">Login</button>
        </form>

        <button class="secondary" onclick="showWelcome()">Back</button>
    </div>
</div>

<!-- DASHBOARD -->
<div id="dashboard" class="hidden">

    <div class="card">
        <h2>School Dashboard</h2>
        <h3 id="displaySchool"></h3>
        <p id="displayLga"></p>
        <p id="displayAdmin"></p>
    </div>

    <div class="card">
        <h2>School Management</h2>

        <div class="menu">
            <button onclick="showSection('staffSection')">Manage Staff</button>
            <button onclick="showSection('attendanceSection')">Attendance</button>
            <button onclick="showSection('recordsSection')">School Records</button>
        </div>
    </div>

    <!-- STAFF -->
    <div id="staffSection" class="card hidden">
        <h2>Add Staff</h2>

        <form id="staffForm">
            <input id="staffName" placeholder="Staff Full Name" required>
            <input id="staffId" placeholder="Staff ID" required>
            <input id="staffPin" type="password" placeholder="Staff PIN" required>
            <button type="submit">Add Staff</button>
        </form>

        <h3>Staff List</h3>
        <ul id="staffList"></ul>
    </div>

    <!-- ATTENDANCE -->
    <div id="attendanceSection" class="card hidden">
        <h2>Attendance Management</h2>
        <p>Attendance module will be added in the next development stage.</p>
    </div>

    <!-- RECORDS -->
    <div id="recordsSection" class="card hidden">
        <h2>School Records</h2>
        <p>Student records and reports will be added in the next development stage.</p>
    </div>

    <div class="card">
        <button onclick="logout()" style="background:#b91c1c">
            Logout
        </button>
    </div>

</div>

</div>

<footer>
    SARM Web App V2.0
</footer>

<script>

let currentSchool = null;

function getSchools() {
    return JSON.parse(localStorage.getItem("sarm_schools") || "[]");
}

function saveSchools(schools) {
    localStorage.setItem("sarm_schools", JSON.stringify(schools));
}

function showOnly(id) {
    ["welcome", "register", "login", "dashboard"].forEach(section => {
        document.getElementById(section).classList.add("hidden");
    });

    document.getElementById(id).classList.remove("hidden");
}

function showWelcome() { showOnly("welcome"); }
function showRegister() { showOnly("register"); }
function showLogin() { showOnly("login"); }

document.getElementById("registerForm").addEventListener("submit", function(e) {
    e.preventDefault();

    let schools = getSchools();

    let school = {
        id: Date.now().toString(),
        name: document.getElementById("schoolName").value.trim(),
        lga: document.getElementById("schoolLga").value.trim(),
        admin: document.getElementById("adminName").value.trim(),
        username: document.getElementById("newUsername").value.trim(),
        password: document.getElementById("newPassword").value,
        staff: []
    };

    if (schools.some(s => s.username.toLowerCase() === school.username.toLowerCase())) {
        alert("This username is already registered. Please choose another.");
        return;
    }

    schools.push(school);
    saveSchools(schools);

    alert("School registration successful! Please login.");

    document.getElementById("registerForm").reset();
    showLogin();
});

document.getElementById("loginForm").addEventListener("submit", function(e) {
    e.preventDefault();

    let username = document.getElementById("username").value.trim();
    let password = document.getElementById("password").value;

    let school = getSchools().find(s =>
        s.username.toLowerCase() === username.toLowerCase() &&
        s.password === password
    );

    if (!school) {
        alert("Incorrect username or password.");
        return;
    }

    currentSchool = school;
    openDashboard();
});

function openDashboard() {
    showOnly("dashboard");

    document.getElementById("displaySchool").textContent =
        currentSchool.name;

    document.getElementById("displayLga").textContent =
        "LGA: " + currentSchool.lga;

    document.getElementById("displayAdmin").textContent =
        "Admin: " + currentSchool.admin;

    document.getElementById("staffSection").classList.add("hidden");
    document.getElementById("attendanceSection").classList.add("hidden");
    document.getElementById("recordsSection").classList.add("hidden");

    showStaff();
}

function showSection(id) {
    ["staffSection", "attendanceSection", "recordsSection"].forEach(section => {
        document.getElementById(section).classList.add("hidden");
    });

    document.getElementById(id).classList.remove("hidden");
}

document.getElementById("staffForm").addEventListener("submit", function(e) {
    e.preventDefault();

    if (!currentSchool) return;

    let name = document.getElementById("staffName").value.trim();
    let id = document.getElementById("staffId").value.trim();
    let pin = document.getElementById("staffPin").value;

    if (currentSchool.staff.some(s => s.id.toLowerCase() === id.toLowerCase())) {
        alert("This Staff ID already exists in your school.");
        return;
    }

    currentSchool.staff.push({name, id, pin});

    updateCurrentSchool();

    this.reset();
    showStaff();

    alert("Staff added successfully!");
});

function updateCurrentSchool() {
    let schools = getSchools();

    let index = schools.findIndex(s => s.id === currentSchool.id);

    if (index !== -1) {
        schools[index] = currentSchool;
        saveSchools(schools);
    }
}

function showStaff() {
    let list = document.getElementById("staffList");
    list.innerHTML = "";

    currentSchool.staff.forEach(staff => {
        let item = document.createElement("li");
        item.textContent = staff.name + " - " + staff.id;
        list.appendChild(item);
    });
}

function logout() {
    currentSchool = null;
    document.getElementById("loginForm").reset();
    showWelcome();
}

showWelcome();

</script>

</body>
</html>

