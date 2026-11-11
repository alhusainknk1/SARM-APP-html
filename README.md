
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SARM V5.1 PRO</title>

<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#f1f5f9;color:#172033}
header{background:#075e46;color:white;padding:18px;text-align:center}
header h2{margin:0}
.container{max-width:700px;margin:18px auto;padding:12px}
.card{background:white;padding:18px;margin-bottom:15px;border-radius:12px;box-shadow:0 2px 8px #0001}
h3{color:#075e46;margin-top:0}
input,select{width:100%;padding:12px;margin:7px 0 13px;border:1px solid #cbd5e1;border-radius:7px;font-size:15px}
button{background:#087f5b;color:white;border:0;padding:12px 16px;border-radius:7px;font-size:14px;margin:5px 2px;cursor:pointer}
button.secondary{background:#475569}
button.danger{background:#b91c1c}
button.full{width:100%}
.small{font-size:13px;color:#64748b}
.record{padding:12px;border-bottom:1px solid #e2e8f0;line-height:1.7}
nav{display:flex;flex-wrap:wrap;gap:5px;margin-bottom:12px}
nav button{flex:1;min-width:90px;font-size:13px}
.hidden{display:none!important}
.status{padding:10px;background:#ecfdf5;border-radius:7px;color:#065f46}
footer{text-align:center;color:#64748b;padding:20px;font-size:12px}
.rank{display:inline-block;background:#e0f2fe;color:#075985;padding:4px 8px;border-radius:5px;font-size:12px}
</style>
</head>

<body>
<header>
<h2>SARM V5.1 PRO</h2>
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
<button class="secondary" onclick="show('login')">Back</button>
</section>

<section id="dashboard" class="hidden">
<div class="card">
<h3 id="welcome"></h3>
<p id="schoolInfo" class="small"></p>
<p id="clock" class="status"></p>
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

<label>Staff Full Name</label>
<input id="staffName" placeholder="Enter staff name">

<label>Staff Rank / Position</label>
<select id="staffRank">
<option>Head Teacher</option>
<option>Deputy Head Teacher</option>
<option>Assistant Head Teacher</option>
<option>Class Teacher</option>
<option>Assistant Teacher</option>
<option>Secretary</option>
<option>Other Staff</option>
</select>

<label>Staff PIN (4–8 digits)</label>
<input id="staffPin" type="password" maxlength="8" placeholder="Create Staff PIN">

<button class="full" onclick="addStaff()">Register Staff</button>

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

<input id="attendancePin" type="password" placeholder="Staff PIN">

<button class="full" onclick="markAttendance()">Submit Attendance</button>

<p class="small">
Date and time are recorded automatically using Nigeria time (WAT).
</p>
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
<input id="latitude" type="number" step="any">

<label>School Longitude</label>
<input id="longitude" type="number" step="any">

<label>Allowed GPS Radius (metres)</label>
<input id="radius" type="number" placeholder="100">

<button onclick="saveSettings()">Save Settings</button>
<p class="small">GPS requires location permission and HTTPS.</p>
</div>

<div id="backup" class="card hidden">
<h3>Backup and Restore</h3>
<p>Download a copy of your school's records.</p>
<button onclick="backup()">Download Backup</button>
<hr>
<p>Restore a previous backup.</p>
<input type="file" id="restoreFile" accept=".json">
<button onclick="restore()">Restore Backup</button>
</div>
</section>
</div>

<footer>SARM V5.1 PRO | School Records Management</footer>

<script>
const DB_KEY = "SARM_V5_DATABASE";
const TIME_ZONE = "Africa/Lagos";

let schools = [];
let currentSchool = null;

const RANKS = [
"Head Teacher",
"Deputy Head Teacher",
"Assistant Head Teacher",
"Class Teacher",
"Assistant Teacher",
"Secretary",
"Other Staff"
];

function uid(){
return Date.now().toString(36)+Math.random().toString(36).slice(2,8);
}

function show(id){
document.querySelectorAll(".container > section, #dashboard")
.forEach(x=>x.classList.add("hidden"));
document.getElementById(id).classList.remove("hidden");
}

function nigeriaDate(date=new Date()){
return new Intl.DateTimeFormat("en-CA",{
timeZone:TIME_ZONE,
year:"numeric",
month:"2-digit",
day:"2-digit"
}).format(date);
}

function nigeriaTime(date){
return new Intl.DateTimeFormat("en-NG",{
timeZone:TIME_ZONE,
dateStyle:"medium",
timeStyle:"medium",
hour12:true
}).format(new Date(date));
}

function saveDatabase(){
localStorage.setItem(DB_KEY,JSON.stringify(schools));
}

function loadDatabase(){
try{
schools=JSON.parse(localStorage.getItem(DB_KEY))||[];
}catch(e){
schools=[];
}

schools.forEach(s=>{
s.staff=s.staff||[];
s.attendance=s.attendance||[];
s.settings=s.settings||{
mode:"pin",latitude:"",longitude:"",radius:100
};

s.staff.forEach(st=>{
if(!st.rank)st.rank="Other Staff";
});
});
saveDatabase();
}

function registerSchool(){
const name=schoolName.value.trim();
const lga=schoolLGA.value.trim();
const admin=adminName.value.trim();
const phoneValue=phone.value.trim();
const user=username.value.trim();
const pass=password.value;
const code=recovery.value.trim();

if(!name||!admin||!phoneValue||!user||!pass||!code){
alert("Please complete all required fields.");
return;
}

if(pass.length<6){
alert("Password must be at least 6 characters.");
return;
}

if(schools.some(s=>s.username.toLowerCase()===user.toLowerCase())){
alert("Username already registered.");
return;
}

const school={
id:uid(),
name,lga,admin,
phone:phoneValue,
username:user,
password:pass,
recoveryCode:code,
staff:[],
attendance:[],
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
loginUser.value=user;
show("login");
}

function loginAdmin(){
const user=loginUser.value.trim();
const pass=loginPass.value;

const school=schools.find(s=>
s.username.toLowerCase()===user.toLowerCase()&&
s.password===pass
);

if(!school){
alert("Incorrect username or password.");
return;
}

currentSchool=school;
openDashboard();
}

function recoverAccount(){
const p=recoverPhone.value.trim();
const c=recoverCode.value.trim();

const school=schools.find(s=>
s.phone===p&&s.recoveryCode===c
);

if(!school){
alert("Recovery details are incorrect.");
return;
}

alert("Your username is: "+school.username+
"\nPlease use your recovery code and contact your administrator for password reset.");

show("login");
}

function openDashboard(){
show("dashboard");

welcome.textContent="Welcome, "+currentSchool.admin;
schoolInfo.textContent=currentSchool.name+" | "+currentSchool.lga;

reportDate.value=nigeriaDate();
updateClock();
loadStaff();
loadSettings();
loadReports();
}

function updateClock(){
if(!currentSchool)return;
clock.textContent="Nigeria Date & Time: "+nigeriaTime(new Date());
}

setInterval(updateClock,1000);

function logout(){
currentSchool=null;
show("home");
}

function tab(id){
["staff","attendance","reports","settings","backup"].forEach(name=>{
document.getElementById(name).classList.toggle("hidden",name!==id);
});

if(id==="staff")loadStaff();
if(id==="attendance")loadAttendanceStaff();
if(id==="reports")loadReports();
if(id==="settings")loadSettings();
}

function saveCurrentSchool(){
const index=schools.findIndex(s=>s.id===currentSchool.id);
if(index>=0){
schools[index]=currentSchool;
saveDatabase();
}
}

function addStaff(){
if(!currentSchool)return;

const name=staffName.value.trim();
const rank=staffRank.value;
const pin=staffPin.value.trim();

if(!name||!pin){
alert("Enter staff name and PIN.");
return;
}

if(!/^[0-9]{4,8}$/.test(pin)){
alert("PIN must contain 4 to 8 digits.");
return;
}

if(currentSchool.staff.some(s=>s.pin===pin)){
alert("This PIN is already used in this school.");
return;
}

currentSchool.staff.push({
id:uid(),
name,
rank,
pin,
createdAt:new Date().toISOString()
});

saveCurrentSchool();

staffName.value="";
staffPin.value="";

loadStaff();
alert("Staff registered successfully.");
}

function editStaff(id){
const staff=currentSchool.staff.find(s=>s.id===id);
if(!staff)return;

const name=prompt("Edit staff name:",staff.name);
if(name===null)return;

if(!name.trim()){
alert("Staff name cannot be empty.");
return;
}

const rank=prompt(
"Enter new rank:\n"+RANKS.join("\n"),
staff.rank
);

if(rank===null)return;

if(!RANKS.includes(rank.trim())){
alert("Please enter a valid rank from the list.");
return;
}

staff.name=name.trim();
staff.rank=rank.trim();

currentSchool.attendance.forEach(a=>{
if(a.staffId===id){
a.staffName=staff.name;
}
});

saveCurrentSchool();
loadStaff();
alert("Staff details updated.");
}

function deleteStaff(id){
if(!confirm("Delete this staff member?"))return;

currentSchool.staff=currentSchool.staff.filter(s=>s.id!==id);

saveCurrentSchool();
loadStaff();
}

function loadStaff(){
const list=document.getElementById("staffList");
list.innerHTML="";

currentSchool.staff.forEach(st=>{
const row=document.createElement("div");
row.className="record";

const name=document.createElement("strong");
name.textContent=st.name;

const rank=document.createElement("div");
rank.innerHTML="";
const badge=document.createElement("span");
badge.className="rank";
badge.textContent=st.rank||"Other Staff";
rank.appendChild(badge);

const edit=document.createElement("button");
edit.textContent="Edit";
edit.onclick=()=>editStaff(st.id);

const del=document.createElement("button");
del.textContent="Delete";
del.className="danger";
del.onclick=()=>deleteStaff(st.id);

row.append(name,rank,edit,del);
list.appendChild(row);
});

loadAttendanceStaff();
}

function loadAttendanceStaff(){
const select=document.getElementById("attendanceStaff");
select.innerHTML='<option value="">Select Staff</option>';

currentSchool.staff.forEach(st=>{
const option=document.createElement("option");
option.value=st.id;
option.textContent=st.name+" - "+st.rank;
select.appendChild(option);
});
}

function loadSettings(){
const s=currentSchool.settings;

mode.value=s.mode||"pin";
latitude.value=s.latitude||"";
longitude.value=s.longitude||"";
radius.value=s.radius||100;
}

function saveSettings(){
currentSchool.settings={
mode:mode.value,
latitude:latitude.value,
longitude:longitude.value,
radius:Number(radius.value)||100
};

saveCurrentSchool();
alert("Settings saved.");
}

function distanceMetres(lat1,lon1,lat2,lon2){
const R=6371000;
const rad=x=>x*Math.PI/180;
const dLat=rad(lat2-lat1);
const dLon=rad(lon2-lon1);

const a=Math.sin(dLat/2)**2+
Math.cos(rad(lat1))*Math.cos(rad(lat2))*
Math.sin(dLon/2)**2;

return R*2*Math.atan2(Math.sqrt(a),Math.sqrt(1-a));
}

function getGPS(){
return new Promise((resolve,reject)=>{
if(!navigator.geolocation){
reject("GPS is not supported.");
return;
}

const s=currentSchool.settings;

if(!s.latitude||!s.longitude){
reject("Admin must enter school GPS coordinates.");
return;
}

navigator.geolocation.getCurrentPosition(position=>{
const lat=position.coords.latitude;
const lon=position.coords.longitude;

const distance=distanceMetres(
lat,lon,Number(s.latitude),Number(s.longitude)
);

if(distance<=Number(s.radius||100)){
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
if(!currentSchool)return;

const staffId=attendanceStaff.value;
const type=attendanceAction.value;
const pin=attendancePin.value.trim();

const staff=currentSchool.staff.find(s=>s.id===staffId);

if(!staff){
alert("Select a staff member.");
return;
}

const verification=currentSchool.settings.mode||"pin";

if((verification==="pin"||verification==="both")&&staff.pin!==pin){
alert("Incorrect staff PIN.");
return;
}

let location=null;

if(verification==="gps"||verification==="both"){
try{
location=await getGPS();
}catch(error){
alert(error);
return;
}
}

const previous=currentSchool.attendance
.filter(a=>a.staffId===staffId)
.sort((a,b)=>new Date(b.time)-new Date(a.time))[0];

if(type==="in"&&previous&&previous.type==="in"){
alert("Staff member is already checked in.");
return;
}

if(type==="out"&&(!previous||previous.type!=="in")){
alert("Staff member must check in first.");
return;
}

/* AUTOMATIC DATE AND TIME */
const now=new Date();

currentSchool.attendance.push({
id:uid(),
staffId,
staffName:staff.name,
staffRank:staff.rank,
type,
time:now.toISOString(),
lat:location?location.lat:null,
lon:location?location.lon:null,
method:verification
});

saveCurrentSchool();

attendancePin.value="";

alert(
(type==="in"?"CHECK-IN SUCCESSFUL":"CHECK-OUT SUCCESSFUL")+
"\n"+staff.name+
"\n"+nigeriaTime(now)
);

loadReports();
}

function getReportRange(){
const period=reportPeriod.value;
const selected=reportDate.value||nigeriaDate();

const parts=selected.split("-").map(Number);
let start=new Date(Date.UTC(parts[0],parts[1]-1,parts[2]));
let end;

if(period==="weekly"){
const day=(start.getUTCDay()+6)%7;
start.setUTCDate(start.getUTCDate()-day);
end=new Date(start);
end.setUTCDate(end.getUTCDate()+7);
}else if(period==="monthly"){
start=new Date(Date.UTC(parts[0],parts[1]-1,1));
end=new Date(Date.UTC(parts[0],parts[1],1));
}else{
end=new Date(start);
end.setUTCDate(end.getUTCDate()+1);
}

return {start,end};
}

function loadReports(){
if(!currentSchool)return;

const range=getReportRange();

const records=currentSchool.attendance.filter(a=>{
const d=new Date(a.time);
return d>=range.start&&d<range.end;
});

const output=document.getElementById("reportResults");
output.innerHTML="";

currentSchool.staff.forEach(st=>{
const list=records.filter(a=>a.staffId===st.id);

const row=document.createElement("div");
row.className="record";

const title=document.createElement("strong");
title.textContent=st.name+" - "+st.rank;

row.appendChild(title);

if(list.length===0){
const p=document.createElement("p");
p.textContent="No attendance records.";
row.appendChild(p);
}else{
list.forEach(a=>{
const p=document.createElement("p");
p.textContent=
(a.type==="in"?"Check-in: ":"Check-out: ")+
nigeriaTime(a.time);
row.appendChild(p);
});
}

output.appendChild(row);
});

if(!currentSchool.staff.length){
output.textContent="No staff registered.";
}
}

function exportCSV(){
const rows=[
["Staff","Rank","Action","Date","Time","Verification"]
];

currentSchool.attendance.forEach(a=>{
const d=new Date(a.time);

rows.push([
a.staffName,
a.staffRank||"Other Staff",
a.type==="in"?"Check In":"Check Out",
nigeriaDate(d),
nigeriaTime(d),
a.method
]);
});

const csv=rows.map(row=>
row.map(v=>'"'+String(v).replace(/"/g,'""')+'"').join(",")
).join("\n");

downloadFile("\uFEFF"+csv,"SARM-Attendance.csv","text/csv;charset=utf-8");
}

function backup(){
const data=JSON.stringify(currentSchool,null,2);
downloadFile(data,"SARM-School-Backup.json","application/json");
}

function downloadFile(data,name,type){
const blob=new Blob([data],{type});
const url=URL.createObjectURL(blob);
const a=document.createElement("a");

a.href=url;
a.download=name;
a.click();

setTimeout(()=>URL.revokeObjectURL(url),1000);
}

async function restore(){
const file=restoreFile.files[0];

if(!file){
alert("Select a backup file first.");
return;
}

try{
const imported=JSON.parse(await file.text());

if(!imported.id||!imported.name||
!Array.isArray(imported.staff)||
!Array.isArray(imported.attendance)){
alert("Invalid SARM backup file.");
return;
}

if(!confirm("Restore this backup and replace this school's records?")){
return;
}

imported.staff.forEach(s=>{
if(!s.rank)s.rank="Other Staff";
});

const index=schools.findIndex(s=>s.id===imported.id);

if(index>=0){
schools[index]=imported;
}else{
schools.push(imported);
}

currentSchool=imported;
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
