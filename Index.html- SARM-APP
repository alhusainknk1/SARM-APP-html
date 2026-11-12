<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>SARM V6.0 PRO</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#f1f5f9;color:#172033;font-family:Arial,sans-serif}
header{background:#075e46;color:white;padding:22px 12px;text-align:center}
header h2{margin:0}
header p{font-size:13px;margin:7px 0 0}
.container{max-width:950px;margin:20px auto;padding:12px}
.card{background:white;padding:18px;border-radius:12px;margin-bottom:15px;box-shadow:0 2px 10px #0001}
h3{color:#075e46;margin-top:0}
input,select,button{width:100%;padding:13px;margin:6px 0;border:1px solid #cbd5e1;border-radius:7px;font-size:15px}
button{background:#075e46;color:white;border:0;font-weight:bold;cursor:pointer}
button.secondary{background:#475569}
button.danger{background:#b91c1c}
button:disabled{opacity:.5}
.hidden{display:none!important}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:12px}
.stat{background:#ecfdf5;padding:16px;border-radius:9px;text-align:center}
.stat b{display:block;font-size:25px;color:#075e46;margin-top:7px}
.small{font-size:12px;color:#64748b}
.msg{padding:12px;border-radius:7px;margin:8px 0;background:#e0f2fe;color:#075985}
.error{background:#fee2e2;color:#991b1b}
.success{background:#dcfce7;color:#166534}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{padding:10px;border-bottom:1px solid #e2e8f0;text-align:left}
.tablewrap{overflow-x:auto}
nav{display:flex;flex-wrap:wrap;gap:6px;margin-bottom:12px}
nav button{width:auto;flex:1;min-width:100px;padding:10px}
footer{text-align:center;color:#64748b;font-size:12px;padding:20px}
</style>
</head>
<body>
<header>
<h2>SARM V6.0 PRO</h2>
<p>School Attendance & Records Manager</p>
</header>

<div class="container">
<div id="message"></div>

<section id="authSection" class="card">
<h3>Login to SARM</h3>
<input id="loginEmail" type="email" placeholder="Email address">
<input id="loginPassword" type="password" placeholder="Password">
<button onclick="login()">Login</button>
<hr>
<h3>Register New School (Admin)</h3>
<input id="schoolName" placeholder="School name">
<select id="schoolType">
<option value="urban">Urban School</option>
<option value="rural">Rural School</option>
</select>
<input id="adminName" placeholder="Admin full name">
<input id="adminEmail" type="email" placeholder="Admin email">
<input id="adminPassword" type="password" placeholder="Create password (minimum 6 characters)">
<button onclick="registerSchool()">Register School</button>
<hr>
<h3>Staff Registration</h3>
<p class="small">Ask your school Admin for the School Code.</p>
<input id="staffSchoolCode" placeholder="School Code">
<input id="staffName" placeholder="Staff full name">
<input id="staffEmail" type="email" placeholder="Staff email">
<input id="staffPassword" type="password" placeholder="Create password (minimum 6 characters)">
<button onclick="registerStaff()">Register as Staff</button>
</section>

<section id="appSection" class="hidden">
<div class="card">
<div class="grid">
<div><b id="welcome">Welcome</b><p class="small" id="schoolInfo"></p></div>
<div><p class="small">School code</p><b id="schoolCode"></b></div>
</div>
<button class="secondary" onclick="logout()">Logout</button>
</div>

<div id="adminPanel" class="hidden">
<nav>
<button onclick="showTab('dashboard')">Dashboard</button>
<button onclick="showTab('history')">History</button>
<button onclick="showTab('staff')">Staff</button>
<button onclick="showTab('settings')">Settings</button>
</nav>

<div id="dashboardTab" class="card">
<h3>School Dashboard</h3>
<div class="grid">
<div class="stat">Total Staff<b id="totalStaff">0</b></div>
<div class="stat">Present Today<b id="presentToday">0</b></div>
<div class="stat">Late Today<b id="lateToday">0</b></div>
<div class="stat">Attendance Rate<b id="attendanceRate">0%</b></div>
</div>
<p class="small" id="todayDate"></p>
</div>

<div id="historyTab" class="card hidden">
<h3>Attendance History & Reports</h3>
<select id="reportPeriod" onchange="loadHistory()">
<option value="daily">Daily</option>
<option value="weekly">Weekly</option>
<option value="monthly">Monthly</option>
</select>
<input id="reportDate" type="date" onchange="loadHistory()">
<button onclick="loadHistory()">Refresh History</button>
<div class="grid">
<div class="stat">Records<b id="historyCount">0</b></div>
<div class="stat">Present<b id="historyPresent">0</b></div>
<div class="stat">Late<b id="historyLate">0</b></div>
<div class="stat">Average Rate<b id="historyRate">0%</b></div>
</div>
<div class="tablewrap">
<table>
<thead><tr><th>Staff</th><th>Date</th><th>Check-in</th><th>Check-out</th><th>Status</th></tr></thead>
<tbody id="historyTable"></tbody>
</table>
</div>
<button class="secondary" onclick="exportCSV()">Export CSV</button>
</div>

<div id="staffTab" class="card hidden">
<h3>Registered Staff</h3>
<div class="tablewrap">
<table>
<thead><tr><th>Name</th><th>Email</th><th>Staff ID</th></tr></thead>
<tbody id="staffTable"></tbody>
</table>
</div>
</div>

<div id="settingsTab" class="card hidden">
<h3>School Settings</h3>
<p>Attendance control</p>
<select id="attendanceEnabled">
<option value="true">Attendance Enabled</option>
<option value="false">Attendance Disabled</option>
</select>
<button onclick="saveSettings()">Save Settings</button>
<h3>Backup</h3>
<p class="small">Download a JSON backup of school, staff and attendance records.</p>
<button class="secondary" onclick="backupData()">Download Backup</button>
</div>
</div>

<div id="staffPanel" class="hidden">
<div class="card">
<h3>Staff Attendance</h3>
<p id="staffDate"></p>
<div class="grid">
<div class="stat">Today's Status<b id="myStatus">Not checked in</b></div>
<div class="stat">Check-in Time<b id="myCheckin">--:--</b></div>
</div>
<button id="checkinButton" onclick="checkIn()">CHECK IN</button>
<button id="checkoutButton" class="secondary" onclick="checkOut()">CHECK OUT</button>
</div>
<div class="card">
<h3>My Attendance History</h3>
<select id="myPeriod" onchange="loadMyHistory()">
<option value="daily">Daily</option>
<option value="weekly">Weekly</option>
<option value="monthly">Monthly</option>
</select>
<div class="grid">
<div class="stat">Attendance Records<b id="myRecords">0</b></div>
<div class="stat">Late Records<b id="myLate">0</b></div>
<div class="stat">Attendance Rate<b id="myRate">0%</b></div>
</div>
<div class="tablewrap">
<table>
<thead><tr><th>Date</th><th>Check-in</th><th>Check-out</th><th>Status</th></tr></thead>
<tbody id="myHistoryTable"></tbody>
</table>
</div>
</div>
</div>
</section>
</div>
<footer>SARM V6.0 PRO | School Attendance & Records Manager</footer>

<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"></script>
<script>
const firebaseConfig = {
 apiKey:"AIzaSyBdlbKG7IHzU43yDgpH2_-umayFUelXMGk",
 authDomain:"sarm-cloud.firebaseapp.com",
 databaseURL:"https://sarm-cloud-default-rtdb.europe-west1.firebasedatabase.app",
 projectId:"sarm-cloud",
 storageBucket:"sarm-cloud.firebasestorage.app",
 messagingSenderId:"422922597417",
 appId:"1:422922597417:web:e5b127f0e670d810fb1838",
 measurementId:"G-HXKF48REYL"
};

firebase.initializeApp(firebaseConfig);
const auth=firebase.auth();
const db=firebase.firestore();

let currentUser=null, profile=null, school=null;
let cachedHistory=[];
let currentTab="dashboard";

db.enablePersistence({synchronizeTabs:true}).catch(e=>{
 console.log("Offline persistence:",e.code);
});

const $=id=>document.getElementById(id);
const today=()=>new Date().toISOString().slice(0,10);
const schoolRef=()=>db.collection("schools").doc(profile.schoolId);
const attendanceRef=()=>schoolRef().collection("attendance");

function notify(message,error=false){
 $("message").innerHTML='<div class="msg '+(error?'error':'success')+'">'+escapeHTML(message)+'</div>';
 window.scrollTo({top:0,behavior:"smooth"});
}
function escapeHTML(s){
 return String(s??"").replace(/[&<>"']/g,c=>({
 "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 })[c]);
}
function timeNow(){
 return new Date().toLocaleTimeString("en-GB",{hour:"2-digit",minute:"2-digit"});
}
function dateNow(){return today();}
function periodStart(period,date){
 const d=new Date(date+"T00:00:00");
 if(period==="weekly"){
  const day=(d.getDay()+6)%7;
  d.setDate(d.getDate()-day);
 }else if(period==="monthly"){
  d.setDate(1);
 }
 return d.toISOString().slice(0,10);
}
function periodEnd(period,date){
 const d=new Date(date+"T00:00:00");
 if(period==="daily")return date;
 if(period==="weekly"){
  const day=(d.getDay()+6)%7;
  d.setDate(d.getDate()-day+6);
 }else{
  d.setMonth(d.getMonth()+1,0);
 }
 return d.toISOString().slice(0,10);
}
function lateLimit(){
 return school?.type==="rural"?"08:20":"08:00";
}
function isLate(t){return t>lateLimit();}

async function registerSchool(){
 try{
  const name=$("schoolName").value.trim();
  const type=$("schoolType").value;
  const adminName=$("adminName").value.trim();
  const email=$("adminEmail").value.trim();
  const password=$("adminPassword").value;
  if(!name||!adminName||!email||password.length<6)
   return notify("Complete all school and admin fields. Password must be at least 6 characters.",true);

  const cred=await auth.createUserWithEmailAndPassword(email,password);
  const uid=cred.user.uid;
  await db.collection("schools").doc(uid).set({
   name,type,ownerUid:uid,attendanceEnabled:true,
   createdAt:firebase.firestore.FieldValue.serverTimestamp()
  });
  await db.collection("users").doc(uid).set({
   name:adminName,email,role:"admin",schoolId:uid,
   createdAt:firebase.firestore.FieldValue.serverTimestamp()
  });
  notify("School registered successfully. Your School Code is "+uid);
 }catch(e){notify(e.message,true);}
}

async function registerStaff(){
 try{
  const schoolId=$("staffSchoolCode").value.trim();
  const name=$("staffName").value.trim();
  const email=$("staffEmail").value.trim();
  const password=$("staffPassword").value;
  if(!schoolId||!name||!email||password.length<6)
   return notify("Complete all staff registration fields.",true);

  const schoolDoc=await db.collection("schools").doc(schoolId).get();
  if(!schoolDoc.exists)return notify("Invalid School Code.",true);

  const cred=await auth.createUserWithEmailAndPassword(email,password);
  const uid=cred.user.uid;
  await db.collection("users").doc(uid).set({
   name,email,role:"staff",schoolId,
   createdAt:firebase.firestore.FieldValue.serverTimestamp()
  });
  await db.collection("schools").doc(schoolId)
   .collection("staff").doc(uid).set({
    name,email,active:true,uid,
    createdAt:firebase.firestore.FieldValue.serverTimestamp()
   });
  notify("Staff registration successful. You can now login.");
 }catch(e){notify(e.message,true);}
}

async function login(){
 try{
  const email=$("loginEmail").value.trim();
  const password=$("loginPassword").value;
  await auth.signInWithEmailAndPassword(email,password);
 }catch(e){notify(e.message,true);}
}

async function logout(){await auth.signOut();}

auth.onAuthStateChanged(async user=>{
 if(!user){
  currentUser=null;profile=null;school=null;
  $("authSection").classList.remove("hidden");
  $("appSection").classList.add("hidden");
  return;
 }
 try{
  currentUser=user;
  const p=await db.collection("users").doc(user.uid).get();
  if(!p.exists)throw new Error("User profile not found. Contact your school administrator.");
  profile=p.data();
  const s=await db.collection("schools").doc(profile.schoolId).get();
  if(!s.exists)throw new Error("School record not found.");
  school=s.data();
  $("authSection").classList.add("hidden");
  $("appSection").classList.remove("hidden");
  $("welcome").textContent="Welcome, "+profile.name;
  $("schoolInfo").textContent=school.name+" | "+school.type.toUpperCase();
  $("schoolCode").textContent=profile.schoolId;
  if(profile.role==="admin"){
   $("adminPanel").classList.remove("hidden");
   $("staffPanel").classList.add("hidden");
   $("attendanceEnabled").value=String(school.attendanceEnabled!==false);
   await loadDashboard();
   await loadStaff();
  }else if(profile.role==="staff"){
   $("staffPanel").classList.remove("hidden");
   $("adminPanel").classList.add("hidden");
   await loadMyToday();
   await loadMyHistory();
  }else throw new Error("Unknown account role.");
 }catch(e){
  notify(e.message,true);
 }
});

function showTab(tab){
 currentTab=tab;
 ["dashboard","history","staff","settings"].forEach(t=>{
  $(t+"Tab").classList.toggle("hidden",t!==tab);
 });
 if(tab==="dashboard")loadDashboard();
 if(tab==="history")loadHistory();
 if(tab==="staff")loadStaff();
}

async function loadStaff(){
 try{
  const snap=await schoolRef().collection("staff").get();
  $("totalStaff").textContent=snap.size;
  $("staffTable").innerHTML=snap.docs.map(d=>{
   const x=d.data();
   return `<tr><td>${escapeHTML(x.name)}</td><td>${escapeHTML(x.email)}</td><td>${escapeHTML(d.id)}</td></tr>`;
  }).join("");
 }catch(e){notify("Staff loading error: "+e.message,true);}
}

async function loadDashboard(){
 try{
  $("todayDate").textContent="Date: "+dateNow()+" | Late threshold: "+lateLimit();
  const [staffSnap,records]=await Promise.all([
   schoolRef().collection("staff").get(),
   attendanceRef().where("date","==",dateNow()).get()
  ]);
  const present=records.size;
  const late=records.docs.filter(d=>d.data().status==="Late").length;
  const rate=staffSnap.size?Math.round(present/staffSnap.size*100):0;
  $("totalStaff").textContent=staffSnap.size;
  $("presentToday").textContent=present;
  $("lateToday").textContent=late;
  $("attendanceRate").textContent=rate+"%";
 }catch(e){notify("Dashboard error: "+e.message,true);}
}

async function loadHistory(){
 if(profile?.role!=="admin")return;
 try{
  const date=$("reportDate").value||dateNow();
  const period=$("reportPeriod").value;
  const start=periodStart(period,date),end=periodEnd(period,date);
  const snap=await attendanceRef()
   .where("date",">=",start).where("date","<=",end)
   .orderBy("date","desc").get();
  cachedHistory=snap.docs.map(d=>({id:d.id,...d.data()}));
  renderAdminHistory();
 }catch(e){notify("History error: "+e.message,true);}
}

function renderAdminHistory(){
 const rows=cachedHistory;
 const present=rows.length;
 const late=rows.filter(x=>x.status==="Late").length;
 const staffCount=Number($("totalStaff").textContent)||0;
 $("historyCount").textContent=rows.length;
 $("historyPresent").textContent=present;
 $("historyLate").textContent=late;
 $("historyRate").textContent=(staffCount?Math.round(present/staffCount*100):0)+"%";
 $("historyTable").innerHTML=rows.map(x=>`
 <tr><td>${escapeHTML(x.staffName)}</td>
 <td>${escapeHTML(x.date)}</td>
 <td>${escapeHTML(x.checkIn||"--")}</td>
 <td>${escapeHTML(x.checkOut||"--")}</td>
 <td>${escapeHTML(x.status)}</td></tr>`).join("");
}

async function loadMyToday(){
 try{
  $("staffDate").textContent="Date: "+dateNow()+" | Late threshold: "+lateLimit();
  const snap=await attendanceRef()
   .where("staffUid","==",currentUser.uid)
   .where("date","==",dateNow()).limit(1).get();
  const rec=snap.empty?null:snap.docs[0].data();
  $("myStatus").textContent=rec?(rec.checkOut?"Completed":rec.status):"Not checked in";
  $("myCheckin").textContent=rec?.checkIn||"--:--";
  $("checkinButton").disabled=!!rec;
  $("checkoutButton").disabled=!rec||!!rec.checkOut;
 }catch(e){notify("Attendance loading error: "+e.message,true);}
}

async function checkIn(){
 try{
  if(school.attendanceEnabled===false)
   return notify("Attendance is currently disabled by your Admin.",true);
  const snap=await attendanceRef()
   .where("staffUid","==",currentUser.uid)
   .where("date","==",dateNow()).limit(1).get();
  if(!snap.empty)return notify("You have already checked in today.",true);

  const now=timeNow();
  const status=isLate(now)?"Late":"Present";
  const data={
   staffUid:currentUser.uid,
   staffName:profile.name,
   date:dateNow(),
   checkIn:now,
   checkInAt:firebase.firestore.FieldValue.serverTimestamp(),
   checkOut:null,
   status,
   schoolId:profile.schoolId
  };
  await attendanceRef().add(data);
  notify("Check-in successful: "+now+" ("+status+")");
  await loadMyToday();
 }catch(e){notify("Check-in error: "+e.message,true);}
}

async function checkOut(){
 try{
  const snap=await attendanceRef()
   .where("staffUid","==",currentUser.uid)
   .where("date","==",dateNow()).limit(1).get();
  if(snap.empty)return notify("Please check in first.",true);
  const doc=snap.docs[0];
  if(doc.data().checkOut)return notify("You have already checked out.",true);
  await doc.ref.update({
   checkOut:timeNow(),
   checkOutAt:firebase.firestore.FieldValue.serverTimestamp()
  });
  notify("Check-out successful.");
  await loadMyToday();
 }catch(e){notify("Check-out error: "+e.message,true);}
}

async function loadMyHistory(){
 try{
  const period=$("myPeriod").value;
  const date=dateNow();
  const start=periodStart(period,date),end=periodEnd(period,date);
  const snap=await attendanceRef()
   .where("staffUid","==",currentUser.uid)
   .where("date",">=",start).where("date","<=",end)
   .orderBy("date","desc").get();
  const rows=snap.docs.map(d=>d.data());
  $("myRecords").textContent=rows.length;
  $("myLate").textContent=rows.filter(x=>x.status==="Late").length;
  const days=period==="daily"?1:period==="weekly"?5:new Date().getDate();
  $("myRate").textContent=Math.min(100,Math.round(rows.length/days*100))+"%";
  $("myHistoryTable").innerHTML=rows.map(x=>`
   <tr><td>${escapeHTML(x.date)}</td>
   <td>${escapeHTML(x.checkIn||"--")}</td>
   <td>${escapeHTML(x.checkOut||"--")}</td>
   <td>${escapeHTML(x.status)}</td></tr>`).join("");
 }catch(e){notify("My History error: "+e.message,true);}
}

async function saveSettings(){
 try{
  await schoolRef().update({
   attendanceEnabled:$("attendanceEnabled").value==="true"
  });
  school.attendanceEnabled=$("attendanceEnabled").value==="true";
  notify("School settings saved.");
 }catch(e){notify("Settings error: "+e.message,true);}
}

function exportCSV(){
 const header=["Staff","Date","Check-in","Check-out","Status"];
 const rows=cachedHistory.map(x=>[
  x.staffName,x.date,x.checkIn||"",x.checkOut||"",x.status
 ]);
 const csv=[header,...rows].map(r=>r.map(v=>'"'+String(v??"").replace(/"/g,'""')+'"').join(",")).join("\n");
 downloadFile(csv,"SARM-attendance.csv","text/csv");
}

async function backupData(){
 try{
  const [staff,attendance]=await Promise.all([
   schoolRef().collection("staff").get(),
   attendanceRef().get()
  ]);
  const backup={
   version:"6.0",
   exportedAt:new Date().toISOString(),
   school,
   staff:staff.docs.map(d=>({id:d.id,...d.data()})),
   attendance:attendance.docs.map(d=>({id:d.id,...d.data()}))
  };
  downloadFile(JSON.stringify(backup,null,2),"SARM-backup-"+dateNow()+".json","application/json");
 }catch(e){notify("Backup error: "+e.message,true);}
}

function downloadFile(content,name,type){
 const blob=new Blob([content],{type});
 const url=URL.createObjectURL(blob);
 const a=document.createElement("a");
 a.href=url;a.download=name;a.click();
 URL.revokeObjectURL(url);
}

$("reportDate").value=dateNow();
</script>
</body>
</html>
