
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SARM V5.0</title>

<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f1f5f9;color:#172033}
header{background:#075e46;color:white;padding:18px;text-align:center}
header h2{margin:0}
.container{max-width:650px;margin:18px auto;padding:12px}
.card{background:white;padding:18px;margin-bottom:15px;border-radius:12px;box-shadow:0 2px 8px #0001}
h3{color:#075e46;margin-top:0}
input,select{width:100%;padding:12px;margin:7px 0 13px;border:1px solid #cbd5e1;border-radius:7px;font-size:15px}
button{background:#087f5b;color:white;border:0;padding:12px 16px;border-radius:7px;font-size:15px;margin:5px 2px;cursor:pointer}
button.secondary{background:#475569}
button.danger{background:#b91c1c}
button.full{width:100%}
.small{font-size:13px;color:#64748b}
.record{padding:12px;border-bottom:1px solid #e2e8f0}
nav{display:flex;flex-wrap:wrap;gap:5px;margin-bottom:12px}
nav button{flex:1;min-width:90px;font-size:13px}
.hidden{display:none!important}
.status{padding:10px;background:#ecfdf5;border-radius:7px;color:#065f46}
footer{text-align:center;color:#64748b;padding:20px;font-size:12px}
</style>
</head>

<body>

<header>
<h2>SARM V5.0</h2>
<p>School Attendance & Records Manager</p>
</header>

<div class="container">

<section id="home" class="card">
<h3>Welcome to SARM</h3>
<p>School Attendance and Records Management System.</p>
<button class="full" onclick="show('signup')">Register New School</button>
<button class="full secondary" onclick="show('login')">Admin Login</button>
</section>

<section id="signup" class="card hidden">
<h3>School Registration</h3>
<input id="schoolName" placeholder="School Name">
<input id="schoolLGA" placeholder="LGA / Location">
<input id="adminName" placeholder="Admin Full Name">
<input id="phone" placeholder="Phone Number">
<input id="username" placeholder="Create Username">
<input id="password" type="password" placeholder="Create Password">
<input id="recovery" placeholder="Create Recovery Code">
<button class="full" onclick="registerSchool()">Create School Account</button>
<button class="secondary" onclick="show('home')">Back</button>
</section>

<section id="login" class="card hidden">
<h3>Admin Login</h3>
<input id="loginUser" placeholder="Username">
<input id="loginPass" type="password" placeholder="Password">
<button class="full" onclick="loginAdmin()">Login</button>
<button class="secondary" onclick="show('forgot')">Forgot Password / Username</button>
<button class="secondary" onclick="show('home')">Back</button>
</section>

<section id="forgot" class="card hidden">
<h3>Account Recovery</h3>
<input id="recoverPhone" placeholder="Registered Phone Number">
<input id="recoverCode" placeholder="Recovery Code">
<button class="full" onclick="recoverAccount()">Recover Account</button>
<button class="secondary" onclick="show('login')">Back to Login</button>
</section>

<section id="dashboard" class="hidden">
<div class="card">
<h3 id="welcome"></h3>
<p id="schoolInfo" class="small"></p>
<button class="danger" onclick="logout()">Logout</button>
</div>

<nav>
<button onclick="tab('staff')">Staff</button>
<button onclick="tab('attendance')">Attendance</button>
<button onclick="tab('reports')">Reports</button>
<button onclick="tab('settings')">Settings</button>
<button onclick="tab('backup')">Backup</button>
</nav>

<div id="staff" class="card">
<h3>Staff Registration</h3>
<input id="staffName" placeholder="Staff Full Name">
<input id="staffPin" type="password" maxlength="8" placeholder="Staff PIN">
<button onclick="addStaff()">Add Staff</button>
<h3>Registered Staff</h3>
<div id="staffList"></div>
</div>

<div id="attendance" class="card hidden">
<h3>Staff Attendance</h3>
<label>Select Staff</label>
<select id="attendanceStaff"></select>
<label>Attendance Action</label>
<select id="attendanceAction">
<option value="in">Check In</option>
<option value="out">Check Out</option>
</select>
<input id="attendancePin" type="password" placeholder="Staff PIN (if required)">
<button class="full" onclick="markAttendance()">Submit Attendance</button>
<p class="small">Attendance verification follows the mode selected in Settings.</p>
</div>

<div id="reports" class="card hidden">
<h3>Attendance Reports</h3>
<select id="reportPeriod" onchange="loadReports()">
<option value="daily">Daily</option>
<option value="weekly">Weekly</option>
<option value="monthly">Monthly</option>
</select>
<input id="reportDate" type="date" onchange="loadReports()">
<div id="reportResults"></div>
<button onclick="exportCSV()">Export CSV</button>
</div>

<div id="settings" class="card hidden">
<h3>School Attendance Settings</h3>
<label>Attendance Verification Mode</label>
<select id="mode">
<option value="pin">PIN Only</option>
<option value="gps">GPS Only</option>
<option value="both">PIN and GPS</option>
</select>
<label>School Latitude</label>
<input id="latitude" type="number" step="any" placeholder="Latitude">
<label>School Longitude</label>
<input id="longitude" type="number" step="any" placeholder="Longitude">
<label>Allowed GPS Radius (metres)</label>
<input id="radius" type="number" placeholder="100">
<button onclick="saveSettings()">Save Settings</button>
<p class="small">GPS verification requires location permission and an HTTPS connection.</p>
</div>

<div id="backup" class="card hidden">
<h3>Backup and Restore</h3>
<p>Download a copy of this school's records.</p>
<button onclick="backup()">Download Backup</button>
<hr>
<p>Restore a previous SARM backup.</p>
<input type="file" id="restoreFile" accept=".json">
<button onclick="restore()">Restore Backup</button>
</div>
</section>

</div>

<footer>SARM V5.0 | School Records Management</footer>

<script>
const DB_KEY = "SARM_V5_DATABASE";
let schools = [];
let currentSchool = null;

function uid(){
    return Date.now().toString(36) + Math.random().toString(36).slice(2,8);
}

function show(id){
    document.querySelectorAll(".container > section, #dashboard")
        .forEach(x => x.classList.add("hidden"));
    document.getElementById(id).classList.remove("hidden");
}

function saveDatabase(){
    localStorage.setItem(DB_KEY, JSON.stringify(schools));
}

function loadDatabase(){
    try{
        schools = JSON.parse(localStorage.getItem(DB_KEY)) || [];
    }catch(e){
        schools = [];
    }
}

function registerSchool(){
    const name = document.getElementById("schoolName").value.trim();
    const lga = document.getElementById("schoolLGA").value.trim();
    const admin = document.getElementById("adminName").value.trim();
    const phone = document.getElementById("phone").value.trim();
    const username = document.getElementById("username").value.trim();
    const password = document.getElementById("password").value;
    const recoveryCode = document.getElementById("recovery").value.trim();

    if(!name || !admin || !phone || !username || !password || !recoveryCode){
        alert("Please complete all required fields.");
        return;
    }

    if(password.length < 6){
        alert("Password must be at least 6 characters.");
        return;
    }

    if(schools.some(s => s.username.toLowerCase() === username.toLowerCase())){
        alert("This username is already registered.");
        return;
    }

    const school = {
        id: uid(),
        name,
        lga,
        admin,
        phone,
        username,
        password,
        recoveryCode,
        staff: [],
        attendance: [],
        settings:{
            mode:"pin",
            latitude:"",
            longitude:"",
            radius:100
        },
        createdAt:new Date().toISOString()
    };

    schools.push(school);
    saveDatabase();

    alert("School account created successfully.");
    document.getElementById("loginUser").value = username;
    show("login");
}

function loginAdmin(){
    const username = document.getElementById("loginUser").value.trim();
    const password = document.getElementById("loginPass").value;

    const school = schools.find(s =>
        s.username.toLowerCase() === username.toLowerCase() &&
        s.password === password
    );

    if(!school){
        alert("Incorrect username or password.");
        return;
    }

    currentSchool = school;
    openDashboard();
}

function recoverAccount(){
    const phone = document.getElementById("recoverPhone").value.trim();
    const code = document.getElementById("recoverCode").value.trim();

    const school = schools.find(s =>
        s.phone === phone && s.recoveryCode === code
    );

    if(!school){
        alert("Recovery details are incorrect.");
        return;
    }

    alert("Your username is: " + school.username +
        "\nYour password cannot be displayed. Please contact the account administrator or use a secure reset process.");

    show("login");
}

function openDashboard(){
    show("dashboard");
    document.getElementById("welcome").textContent =
        "Welcome, " + currentSchool.admin;
    document.getElementById("schoolInfo").textContent =
        currentSchool.name + " | " + currentSchool.lga;

    loadStaff();
    loadSettings();
    loadReports();
}

function logout(){
    currentSchool = null;
    show("home");
}

function tab(id){
    ["staff","attendance","reports","settings","backup"].forEach(name => {
        document.getElementById(name).classList.toggle("hidden", name !== id);
    });

    if(id === "staff") loadStaff();
    if(id === "attendance") loadAttendanceStaff();
    if(id === "reports") loadReports();
    if(id === "settings") loadSettings();
}

function addStaff(){
    if(!currentSchool) return;

    const name = document.getElementById("staffName").value.trim();
    const pin = document.getElementById("staffPin").value.trim();

    if(!name || !pin){
        alert("Enter staff name and PIN.");
        return;
    }

    if(!/^[0-9]{4,8}$/.test(pin)){
        alert("PIN must contain 4 to 8 digits.");
        return;
    }

    if(currentSchool.staff.some(s => s.pin === pin)){
        alert("This PIN is already used in this school.");
        return;
    }

    currentSchool.staff.push({
        id:uid(),
        name,
        pin
    });

    saveCurrentSchool();
    document.getElementById("staffName").value = "";
    document.getElementById("staffPin").value = "";
    loadStaff();

    alert("Staff registered successfully.");
}

function saveCurrentSchool(){
    const index = schools.findIndex(s => s.id === currentSchool.id);
    if(index >= 0){
        schools[index] = currentSchool;
        saveDatabase();
    }
}

function loadStaff(){
    const list = document.getElementById("staffList");
    list.innerHTML = "";

    currentSchool.staff.forEach(staff => {
        const row = document.createElement("div");
        row.className = "record";
        row.textContent = staff.name;
        list.appendChild(row);
    });

    loadAttendanceStaff();
}

function loadAttendanceStaff(){
    const select = document.getElementById("attendanceStaff");
    select.innerHTML = '<option value="">Select Staff</option>';

    currentSchool.staff.forEach(staff => {
        const option = document.createElement("option");
        option.value = staff.id;
        option.textContent = staff.name;
        select.appendChild(option);
    });
}

function loadSettings(){
    const s = currentSchool.settings;
    document.getElementById("mode").value = s.mode || "pin";
    document.getElementById("latitude").value = s.latitude || "";
    document.getElementById("longitude").value = s.longitude || "";
    document.getElementById("radius").value = s.radius || 100;
}

function saveSettings(){
    currentSchool.settings = {
        mode:document.getElementById("mode").value,
        latitude:document.getElementById("latitude").value,
        longitude:document.getElementById("longitude").value,
        radius:Number(document.getElementById("radius").value) || 100
    };

    saveCurrentSchool();
    alert("Settings saved.");
}

function distanceMetres(lat1, lon1, lat2, lon2){
    const R = 6371000;
    const rad = x => x * Math.PI / 180;
    const dLat = rad(lat2-lat1);
    const dLon = rad(lon2-lon1);

    const a = Math.sin(dLat/2)**2 +
        Math.cos(rad(lat1))*Math.cos(rad(lat2))*
        Math.sin(dLon/2)**2;

    return R * 2 * Math.atan2(Math.sqrt(a),Math.sqrt(1-a));
}

function getGPS(){
    return new Promise((resolve,reject)=>{
        if(!navigator.geolocation){
            reject("GPS is not supported.");
            return;
        }

        const s = currentSchool.settings;

        if(!s.latitude || !s.longitude){
            reject("Admin must enter the school GPS coordinates.");
            return;
        }

        navigator.geolocation.getCurrentPosition(position=>{
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;

            const distance = distanceMetres(
                lat,lon,Number(s.latitude),Number(s.longitude)
            );

            if(distance <= Number(s.radius || 100)){
                resolve({lat,lon});
            }else{
                reject("Location is outside the school boundary.");
            }
        },()=>{
            reject("GPS permission or location could not be verified.");
        },{
            enableHighAccuracy:true,
            timeout:15000
        });
    });
}

async function markAttendance(){
    const staffId = document.getElementById("attendanceStaff").value;
    const type = document.getElementById("attendanceAction").value;
    const pin = document.getElementById("attendancePin").value.trim();

    const staff = currentSchool.staff.find(s => s.id === staffId);

    if(!staff){
        alert("Select a staff member.");
        return;
    }

    const mode = currentSchool.settings.mode || "pin";

    if((mode === "pin" || mode === "both") && staff.pin !== pin){
        alert("Incorrect staff PIN.");
        return;
    }

    let location = null;

    if(mode === "gps" || mode === "both"){
        try{
            location = await getGPS();
        }catch(error){
            alert(error);
            return;
        }
    }

    const previous = currentSchool.attendance
        .filter(a => a.staffId === staffId)
        .sort((a,b)=>new Date(b.time)-new Date(a.time))[0];

    if(type === "in" && previous && previous.type === "in"){
        alert("Staff member is already checked in.");
        return;
    }

    if(type === "out" && (!previous || previous.type !== "in")){
        alert("Staff member must check in first.");
        return;
    }

    const now = new Date();

    currentSchool.attendance.push({
        id:uid(),
        staffId,
        staffName:staff.name,
        type,
        time:now.toISOString(),
        lat:location ? location.lat : null,
        lon:location ? location.lon : null,
        method:mode
    });

    saveCurrentSchool();

    document.getElementById("attendancePin").value = "";

    alert(type === "in" ? "Check-in successful." : "Check-out successful.");

    loadReports();
}

function loadReports(){
    if(!currentSchool) return;

    const period = document.getElementById("reportPeriod").value;
    const dateValue = document.getElementById("reportDate").value ||
        new Date().toISOString().slice(0,10);

    const start = new Date(dateValue + "T00:00:00");
    const end = new Date(dateValue + "T23:59:59.999");

    if(period === "weekly"){
        const day = start.getDay();
        const offset = day === 0 ? 6 : day - 1;
        start.setDate(start.getDate()-offset);
        end.setTime(start.getTime());
        end.setDate(start.getDate()+6);
        end.setHours(23,59,59,999);
    }

    if(period === "monthly"){
        start.setDate(1);
        end.setMonth(start.getMonth()+1,0);
        end.setHours(23,59,59,999);
    }

    const records = currentSchool.attendance.filter(a=>{
        const d = new Date(a.time);
        return d >= start && d <= end;
    });

    const output = document.getElementById("reportResults");
    output.innerHTML = "";

    currentSchool.staff.forEach(staff=>{
        const list = records.filter(a=>a.staffId===staff.id);
        const ins = list.filter(a=>a.type==="in").length;
        const outs = list.filter(a=>a.type==="out").length;

        const row = document.createElement("div");
        row.className = "record";
        row.textContent = staff.name +
            " | Check-ins: " + ins +
            " | Check-outs: " + outs;

        output.appendChild(row);
    });
}

function exportCSV(){
    const rows = [
        ["Staff","Action","Date","Time","Verification"]
    ];

    currentSchool.attendance.forEach(a=>{
        const d = new Date(a.time);
        rows.push([
            a.staffName,
            a.type,
            d.toLocaleDateString(),
            d.toLocaleTimeString(),
            a.method
        ]);
    });

    const csv = rows.map(row=>
        row.map(v=>'"'+String(v).replace(/"/g,'""')+'"').join(",")
    ).join("\n");

    downloadFile(csv,"SARM-Attendance.csv","text/csv");
}

function backup(){
    const data = JSON.stringify(currentSchool,null,2);
    downloadFile(data,"SARM-School-Backup.json","application/json");
}

function downloadFile(data,name,type){
    const blob = new Blob([data],{type});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");

    a.href = url;
    a.download = name;
    a.click();

    URL.revokeObjectURL(url);
}

async function restore(){
    const file = document.getElementById("restoreFile").files[0];

    if(!file){
        alert("Select a backup file first.");
        return;
    }

    try{
        const imported = JSON.parse(await file.text());

        if(!imported.id || !imported.name ||
           !Array.isArray(imported.staff) ||
           !Array.isArray(imported.attendance)){
            alert("Invalid SARM backup file.");
            return;
        }

        if(!confirm("Restore this backup and replace this school's records?")){
            return;
        }

        const index = schools.findIndex(s=>s.id===imported.id);

        if(index >= 0){
            schools[index] = imported;
        }else{
            schools.push(imported);
        }

        currentSchool = imported;
        saveDatabase();
        openDashboard();

        alert("Backup restored successfully.");
    }catch(error){
        alert("Could not read this backup file.");
    }
}

window.addEventListener("load",()=>{
    loadDatabase();
    show("home");
});
</script>

</body>
</html>


