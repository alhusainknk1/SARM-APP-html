rrentSchool(){
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
