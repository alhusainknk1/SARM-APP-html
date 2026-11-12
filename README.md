



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
