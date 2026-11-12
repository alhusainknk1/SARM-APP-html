<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>SARM V5.1 PRO</title>

<style>
*{box-sizing:border-box}
body{
 margin:0;
 font-family:Arial,sans-serif;
 background:#f1f5f9;
 color:#172033
}
header{
 background:#075e46;
 color:white;
 padding:22px 12px;
 text-align:center
}
header h2{margin:0}
header p{font-size:13px;margin:7px 0 0}
.container{
 max-width:850px;
 margin:20px auto;
 padding:12px
}
.card{
 background:white;
 padding:20px;
 border-radius:12px;
 margin-bottom:16px;
 box-shadow:0 2px 10px #0001
}
h3{color:#075e46;margin-top:0}
input,button{
 width:100%;
 padding:14px;
 margin:7px 0;
 border-radius:7px;
 font-size:15px
}
input{border:1px solid #cbd5e1}
button{
 border:0;
 background:#087653;
 color:white;
 font-weight:bold;
 cursor:pointer
}
button:disabled{opacity:.5}
.secondary{background:#475569}
.danger{background:#b42318}
.hidden{display:none!important}
.msg{
 padding:12px;
 background:#ecfdf3;
 color:#075e46;
 border-radius:7px;
 margin:12px 0;
 display:none;
 overflow-wrap:anywhere
}
.error{background:#fef2f2;color:#b42318}
.small{font-size:13px;color:#64748b}
.stat{
 background:#e9f7f0;
 padding:18px;
 border-radius:9px;
 text-align:center
}
.stat strong{
 font-size:28px;
 display:block;
 color:#075e46
}
.table-wrap{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{
 padding:11px;
 border-bottom:1px solid #ddd;
 text-align:left;
 white-space:nowrap
}
a{color:#087653}
</style>
</head>

<body>

<header>
 <h2>SARM V5.1 PRO</h2>
 <p>School Attendance & Records Manager</p>
</header>

<div class="container">

 <div id="message" class="msg"></div>

 <!-- SCHOOL REGISTRATION -->
 <section id="register" class="card">
  <h3>Register Your School</h3>

  <input id="schoolName" placeholder="School Name">
  <input id="lga" placeholder="Local Government Area">
  <input id="adminName" placeholder="School Admin Name">
  <input id="regEmail" type="email" placeholder="Admin Gmail">
  <input id="regPassword" type="password"
   placeholder="Create Password (minimum 6 characters)">

  <button id="registerBtn">REGISTER SCHOOL</button>

  <p class="small">
   Already registered?
   <a href="#" id="showLogin">Login here</a>
  </p>
 </section>

 <!-- LOGIN -->
 <section id="login" class="card hidden">
  <h3>Admin Login</h3>

  <input id="loginEmail" type="email" placeholder="Email Address">
  <input id="loginPassword" type="password" placeholder="Password">

  <button id="loginBtn">LOGIN</button>

  <p class="small">
   New school?
   <a href="#" id="showRegister">Register here</a>
  </p>

  <button id="forgotBtn" class="secondary">
   Forgot Password?
  </button>
 </section>

 <!-- DASHBOARD -->
 <section id="dashboard" class="hidden">

  <div class="card">
   <h3 id="welcome">School Dashboard</h3>
   <p id="schoolInfo"></p>
   <button id="logoutBtn" class="danger">LOGOUT</button>
  </div>

  <div class="card">
   <h3>Teacher Attendance</h3>

   <input id="teacherName"
    placeholder="Teacher / Staff Name">

   <button id="checkInBtn">CHECK IN</button>
   <button id="checkOutBtn" class="secondary">CHECK OUT</button>

   <p class="small">
    Attendance will be saved to your school's cloud records.
   </p>
  </div>

  <div class="card">
   <h3>Attendance Summary</h3>
   <div class="stat">
    <strong id="totalRecords">0</strong>
    Total Attendance Records
   </div>
  </div>

  <div class="card">
   <h3>Attendance History</h3>

   <div class="table-wrap">
    <table>
     <thead>
      <tr>
       <th>Teacher</th>
       <th>Action</th>
       <th>Date</th>
       <th>Time</th>
      </tr>
     </thead>
     <tbody id="attendanceList">
      <tr><td colspan="4">Loading...</td></tr>
     </tbody>
    </table>
   </div>
  </div>

 </section>

</div>

<script type="module">

import { initializeApp }
from "https://www.gstatic.com/firebasejs/11.6.0/firebase-app.js";

import {
 getAuth,
 createUserWithEmailAndPassword,
 signInWithEmailAndPassword,
 sendPasswordResetEmail,
 signOut,
 onAuthStateChanged
} from "https://www.gstatic.com/firebasejs/11.6.0/firebase-auth.js";

import {
 getFirestore,
 collection,
 doc,
 setDoc,
 getDoc,
 addDoc,
 query,
 where,
 onSnapshot,
 serverTimestamp
} from "https://www.gstatic.com/firebasejs/11.6.0/firebase-firestore.js";

// FIREBASE CONFIGURATION

const firebaseConfig = {
 apiKey: "AIzaSyBdlbKG7IHzU43yDgpH2_-umayFUelXMGk",
 authDomain: "sarm-cloud.firebaseapp.com",
 databaseURL: "https://sarm-cloud-default-rtdb.europe-west1.firebasedatabase.app",
 projectId: "sarm-cloud",
 storageBucket: "sarm-cloud.firebasestorage.app",
 messagingSenderId: "422922597417",
 appId: "1:422922597417:web:e5b127f0e670d810fb1838",
 measurementId: "G-HXKF48REYL"
};

// INITIALIZE FIREBASE

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

// APP VARIABLES

const $ = id => document.getElementById(id);

let currentUser = null;
let schoolData = null;
let unsubscribeAttendance = null;
let registrationInProgress = false;

// MESSAGE

function message(text,error=false){
 const el=$("message");
 el.textContent=text;
 el.className=error ? "msg error" : "msg";
 el.style.display="block";
 window.scrollTo({top:0,behavior:"smooth"});
}

function clearMessage(){
 $("message").style.display="none";
}

// PAGE CONTROL

function showPage(page){
 ["register","login","dashboard"].forEach(id=>{
  $(id).classList.toggle("hidden",id!==page);
 });
 clearMessage();
}

// NAVIGATION

$("showLogin").onclick=e=>{
 e.preventDefault();
 showPage("login");
};

$("showRegister").onclick=e=>{
 e.preventDefault();
 showPage("register");
};

// SCHOOL REGISTRATION

$("registerBtn").onclick=async()=>{

 const name=$("schoolName").value.trim();
 const lga=$("lga").value.trim();
 const admin=$("adminName").value.trim();
 const email=$("regEmail").value.trim();
 const password=$("regPassword").value;

 if(!name || !lga || !admin || !email || !password){
  return message("Please fill in all fields.",true);
 }

 if(password.length<6){
  return message("Password must be at least 6 characters.",true);
 }

 $("registerBtn").disabled=true;
 registrationInProgress=true;

 try{

  const credential=await createUserWithEmailAndPassword(
   auth,email,password
  );

  const uid=credential.user.uid;

  await setDoc(doc(db,"schools",uid),{
   schoolName:name,
   lga:lga,
   ownerUid:uid,
   createdAt:serverTimestamp()
  });

  const profile={
   adminName:admin,
   email:email,
   schoolId:uid,
   role:"admin",
   createdAt:serverTimestamp()
  };

  await setDoc(doc(db,"users",uid),profile);

  currentUser=credential.user;
  schoolData=profile;

  await openDashboard(credential.user,profile);

  message("School registration successful!");

 }catch(error){

  message("Registration error: "+error.message,true);

 }finally{

  registrationInProgress=false;
  $("registerBtn").disabled=false;

 }
};

// ADMIN LOGIN

$("loginBtn").onclick=async()=>{

 const email=$("loginEmail").value.trim();
 const password=$("loginPassword").value;

 if(!email || !password){
  return message("Enter your email and password.",true);
 }

 $("loginBtn").disabled=true;

 try{

  await signInWithEmailAndPassword(auth,email,password);

 }catch(error){

  message("Login error: "+error.message,true);

 }finally{

  $("loginBtn").disabled=false;

 }
};

// PASSWORD RECOVERY

$("forgotBtn").onclick=async()=>{

 const email=$("loginEmail").value.trim();

 if(!email){
  return message("Enter your Gmail address first.",true);
 }

 try{

  await sendPasswordResetEmail(auth,email);
  message("Password reset email sent. Check your inbox.");

 }catch(error){

  message(error.message,true);

 }
};

// DASHBOARD

async function openDashboard(user,profile){

 const school=await getDoc(
  doc(db,"schools",profile.schoolId)
 );

 if(!school.exists()){
  throw new Error("School record not found.");
 }

 currentUser=user;
 schoolData=profile;

 $("welcome").textContent="Welcome, "+profile.adminName;

 $("schoolInfo").textContent=
  school.data().schoolName+" | "+school.data().lga;

 showPage("dashboard");

 loadAttendance(profile.schoolId);
}

// AUTHENTICATION

onAuthStateChanged(auth,async user=>{

 if(registrationInProgress)return;

 if(unsubscribeAttendance){
  unsubscribeAttendance();
  unsubscribeAttendance=null;
 }

 if(!user){
  currentUser=null;
  schoolData=null;
  showPage("login");
  return;
 }

 try{

  const profile=await getDoc(
   doc(db,"users",user.uid)
  );

  if(!profile.exists()){
   await signOut(auth);
   message("Admin profile not found. Please contact support.",true);
   return;
  }

  await openDashboard(user,profile.data());

 }catch(error){

  message("Dashboard error: "+error.message,true);

 }

});

// RECORD ATTENDANCE

async function recordAttendance(action){

 if(!currentUser || !schoolData){
  return message("Please login again.",true);
 }

 const teacher=$("teacherName").value.trim();

 if(!teacher){
  return message("Enter teacher or staff name.",true);
 }

 try{

  const now=new Date();

  await addDoc(collection(db,"attendance"),{

   schoolId:schoolData.schoolId,
   teacherName:teacher,
   action:action,

   date:now.toLocaleDateString("en-CA"),
   time:now.toLocaleTimeString(),

   createdAt:serverTimestamp(),
   createdBy:currentUser.uid

  });

  $("teacherName").value="";

  message(action+" recorded successfully!");

 }catch(error){

  message("Attendance error: "+error.message,true);

 }

}

$("checkInBtn").onclick=()=>recordAttendance("CHECK IN");

$("checkOutBtn").onclick=()=>recordAttendance("CHECK OUT");

// LIVE ATTENDANCE HISTORY

function loadAttendance(schoolId){

 const q=query(
  collection(db,"attendance"),
  where("schoolId","==",schoolId)
 );

 unsubscribeAttendance=onSnapshot(q,snapshot=>{

  const records=[];

  snapshot.forEach(item=>{
   records.push(item.data());
  });

  records.sort((a,b)=>
   (b.date+" "+b.time).localeCompare(a.date+" "+a.time)
  );

  $("totalRecords").textContent=records.length;

  const list=$("attendanceList");
  list.innerHTML="";

  if(records.length===0){
   list.innerHTML=
    '<tr><td colspan="4">No attendance records yet.</td></tr>';
   return;
  }

  records.forEach(record=>{

   const tr=document.createElement("tr");

   [
    record.teacherName,
    record.action,
    record.date,
    record.time
   ].forEach(value=>{

    const td=document.createElement("td");
    td.textContent=value || "";
    tr.appendChild(td);

   });

   list.appendChild(tr);

  });

 },error=>{

  message("History error: "+error.message,true);

 });

}

// LOGOUT

$("logoutBtn").onclick=async()=>{

 try{

  await signOut(auth);

 }catch(error){

  message(error.message,true);

 }

};

// INITIAL SCREEN

showPage("register");

</script>

</body>
</html>
