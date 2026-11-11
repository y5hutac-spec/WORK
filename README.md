
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Y5HUT ATTANTANCE</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;font-family:Arial,sans-serif;
 background:#f1f5f9;color:#172554;
}
header{
 background:#123c78;color:white;
 padding:25px 15px;text-align:center;
}
header h1{margin:0;font-size:27px}
header p{margin-bottom:0;color:#dbeafe}
.container{max-width:950px;margin:25px auto;padding:15px}
.cards{
 display:grid;grid-template-columns:repeat(3,1fr);
 gap:12px;margin-bottom:20px;
}
.card{
 background:white;padding:20px;border-radius:12px;
 text-align:center;box-shadow:0 2px 8px #0001;
}
.card h2{font-size:28px;margin:5px}
form,.table-box{
 background:white;padding:20px;border-radius:12px;
 margin-bottom:20px;box-shadow:0 2px 8px #0001;
}
input,select,button{
 width:100%;padding:12px;margin:8px 0;
 border:1px solid #cbd5e1;border-radius:7px;
 font-size:15px;
}
button{
 background:#1763bd;color:white;border:0;
 cursor:pointer;font-weight:bold;
}
button:hover{background:#0c4388}
.export{background:#15803d}
table{width:100%;border-collapse:collapse}
th,td{padding:12px;border-bottom:1px solid #ddd;text-align:left}
th{background:#eaf2ff}
.delete{background:#dc2626;padding:7px}
.table-scroll{overflow-x:auto}
footer{text-align:center;padding:20px;color:#64748b}
@media(max-width:600px){
 .cards{grid-template-columns:1fr}
 th,td{padding:8px;font-size:13px}
}
</style>
</head>
<body>

<header>
<h1>Y5HUT ATTANTANCE</h1>
<p>Attendance Management System</p>
</header>

<div class="container">

<div class="cards">
 <div class="card">
  <p>Total Records</p><h2 id="total">0</h2>
 </div>
 <div class="card">
  <p>Present</p><h2 id="present">0</h2>
 </div>
 <div class="card">
  <p>Absent</p><h2 id="absent">0</h2>
 </div>
</div>

<form id="attendanceForm">
<h2>Mark Attendance</h2>

<label>Student / Employee Name</label>
<input id="person" type="text" placeholder="Enter Name" required>

<label>Date</label>
<input id="date" type="date" required>

<label>Attendance Status</label>
<select id="status">
<option value="Present">Present</option>
<option value="Absent">Absent</option>
<option value="Late">Late</option>
</select>

<button type="submit">SAVE ATTENDANCE</button>
</form>

<div class="table-box">
<h2>Attendance Records</h2>
<button class="export" onclick="downloadCSV()">DOWNLOAD CSV REPORT</button>
<div class="table-scroll">
<table>
<thead>
<tr>
<th>Name</th>
<th>Date</th>
<th>Status</th>
<th>Action</th>
</tr>
</thead>
<tbody id="records"></tbody>
</table>
</div>
</div>
</div>

<footer>© 2026 Y5HUT ATTANTANCE</footer>

<script>
let data = JSON.parse(localStorage.getItem("y5hutRecords") || "[]");

const dateInput = document.getElementById("date");
dateInput.value = new Date().toLocaleDateString("en-CA");

function saveData(){
 localStorage.setItem("y5hutRecords",JSON.stringify(data));
}

function render(){
 const table=document.getElementById("records");
 table.innerHTML="";

 data.forEach((item,index)=>{
  const tr=document.createElement("tr");

  [item.name,item.date,item.status].forEach(value=>{
   const td=document.createElement("td");
   td.textContent=value;
   tr.appendChild(td);
  });

  const action=document.createElement("td");
  const btn=document.createElement("button");
  btn.textContent="Delete";
  btn.className="delete";
  btn.onclick=()=>{
   if(confirm("Delete this record?")){
    data.splice(index,1);
    saveData();
    render();
   }
  };
  action.appendChild(btn);
  tr.appendChild(action);
  table.appendChild(tr);
 });

 document.getElementById("total").textContent=data.length;
 document.getElementById("present").textContent=
 data.filter(x=>x.status==="Present").length;
 document.getElementById("absent").textContent=
 data.filter(x=>x.status==="Absent").length;
}

document.getElementById("attendanceForm")
.addEventListener("submit",function(e){
 e.preventDefault();

 const name=document.getElementById("person").value.trim();
 const date=dateInput.value;
 const status=document.getElementById("status").value;

 if(data.some(x=>x.name.toLowerCase()===name.toLowerCase() && x.date===date)){
  alert("Attendance already exists for this person and date!");
  return;
 }

 data.push({name,date,status});
 saveData();
 render();
 this.reset();
 dateInput.value=new Date().toLocaleDateString("en-CA");
 alert("Attendance Saved Successfully!");
});

function downloadCSV(){
 let csv="Name,Date,Status\n";
 data.forEach(x=>{
  csv+=[x.name,x.date,x.status].map(v=>
   '"'+String(v).replace(/"/g,'""')+'"'
  ).join(",")+"\n";
 });

 const blob=new Blob(["\uFEFF"+csv],{type:"text/csv;charset=utf-8;"});
 const url=URL.createObjectURL(blob);
 const a=document.createElement("a");
 a.href=url;
 a.download="Y5HUT_Attendance.csv";
 a.click();
 URL.revokeObjectURL(url);
}

render();
</script>
</body>
</html>
