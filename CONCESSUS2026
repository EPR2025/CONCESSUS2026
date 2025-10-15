<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PCR-EPR Member Management</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
body { font-family:"Times New Roman",sans-serif; background:#b0cdea; text-align:center; margin:20px;}
h1,h2 { color:#12984c; }
select,input {padding:8px; margin:5px 0; width:60%; border:1px solid #ccc; border-radius:5px;}
button {padding:6px 12px; margin:4px; border:none; border-radius:5px; cursor:pointer;}
button:hover {background:#14ac56;color:#fff;}
.hidden {display:none;}
.form-container {background:#f4f6f8; padding:20px; border-radius:8px; margin-top:20px; display:inline-block; text-align:left;}
table {margin:auto; background:#fff; border-collapse: collapse; width:100%; display:block; overflow-x:auto;}
table, th, td {border:1px solid #999;}
th, td {padding:5px; text-align:center; white-space:nowrap;}
#memberListContainer, #archivePage, #transferModal, #chartContainer, #dataPage, #familyInfoPage {margin-top:20px;}
label.filter-label {margin-left:5px;}
.action-btn {margin:0 2px;}
#transferModal {position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:#f4f6f8; padding:20px; border-radius:8px; z-index:1000; min-width:300px;}
#overlay {position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(242, 199, 199, 0.4); z-index:900;}
.close-btn {float:right; cursor:pointer; font-weight:bold; color:#f00;}
#chartContainer {width:80%; margin:auto;}
canvas {max-width:100%;}
</style>
</head>
<body>

<h1>PCR-EPR Member Management</h1>
<p>Presbyterian Church of Rwanda-EPR has 7 presbyteries.</p>
<button id="startBtn" aria-label="Tangiza gukurikirana abakristo">Kanda Hano!</button>
<button id="viewDataBtn" aria-label="Reba Abakristo Data">Reba Abakristo Data</button>

<!-- Presbyteries selection -->
<div id="presbyteriesPage" class="hidden">
<h2>Hitamo Presbyteries & Paruwasi</h2>
<div id="presbyList"></div>
<button id="backToMain" aria-label="Subira ku rubuga rw'ibanze">Subira Inyuma</button>
</div>

<!-- Member Form -->
<div id="memberFormPage" class="hidden">
<h2>Amakuru y'abakristo</h2>
<div class="form-container">
<form id="memberForm">
<label>No:</label>
<input type="text" id="autoNo" readonly><br>
<label>Ishuri:</label>
<select id="schoolSelect" aria-label="Hitamo Ishuri"></select><br>
<label>Itorero ry'ibanze:</label>
<select id="schoolSelect2" required aria-label="Hitamo Itorero ry'ibanze"><option value="">--Hitamo Itorero--</option></select><br>
<label>Irangamuntu:</label>
<input type="text" placeholder="Shyiramo numero y'irangamuntu" id="idInput" pattern="[0-9]{16}" title="Irangamuntu igizwe n’imibare 16" required><br>
<label>Amazina yombi:</label>
<input type="text" placeholder="Shyiramo amazina" id="nameInput" required><br>
<label>Igitsina:</label>
<select id="genderSelect" required aria-label="Hitamo Igitsina"><option value="">--Hitamo--</option><option>Gabo</option><option>Gore</option></select><br>
<label>Irangamimerere:</label>
<select id="MartalstatusSelect" required aria-label="Hitamo Irangamimerere">
  <option value="">---Hitamo---</option>
  <option value="ingaragu">Ingaragu</option>
  <option value="arubatse">Arubatse</option>
  <option value="umupfakazi">Umupfakazi</option>
  <option value="divorce">Divorce</option>
</select><br>
<label>Ububatizo:</label>
<select id="baptismSelect" required aria-label="Hitamo Ububatizo">
  <option value="">--Hitamo--</option>
  <option value="yego">Yarabatijwe</option>
  <option value="oya">Ntiyabatijwe</option>
</select><br>
<label>Itariki y'amavuko:</label>
<select id="yearSelect" required aria-label="Hitamo Umwaka"><option value="">--Hitamo Umwaka--</option></select><br>
<label>Telephone:</label>
<input type="tel" placeholder="Shyiramo nimero ya telephone" id="phoneInput" pattern="[0-9]{10}" title="Injiza nimero ya telefone ifite imibare 10" required><br>
<label>Urwego rw'amashuri yize:</label>
<select id="educationSelect" required aria-label="Hitamo urwego rw'amashuri yize">
  <option value="">---Hitamo---</option>
  <option value="Ntazi gusoma">Ntazi gusoma</option>
  <option value="Isomero">Isomero</option>
  <option value="Abanza">Abanza</option>
  <option value="Ayisumbuye">Ayisumbuye</option>
  <option value="Kaminuza">Kaminuza</option>
  <option value="Masters">Masters</option>
  <option value="PHD">PHD</option>
  <option value="Professor">Professor</option>
</select><br>
<label>Icyo akora:</label>
<select id="jobStatusSelect" required aria-label="Hitamo icyo akora">
  <option value="">---Hitamo---</option>
  <option value="Ntakazi agira">Ntakazi agira</option>
  <option value="Arikorera">Arikorera</option>
  <option value="Akorera abandi">Akorera abandi</option>
  <option value="Akorera leta">Akorera leta</option>
  <option value="Akorera NGOs">Akorera NGOs</option>
</select><br>
<button type="button" id="backToPresby" aria-label="Subira ku hitamo presbytery">Subira Inyuma</button>
<button type="submit" aria-label="Ohereza amakuru">Ohereza</button>
</form>
</div>
</div>

<!-- Family Information Page -->
<div id="familyInfoPage" class="hidden">
<h2>Amakuru y'Umuryango</h2>
<div class="form-container">
<form id="familyForm">
<label>Izina ry'umukristo:</label>
<input type="text" id="familyName" readonly><br>
<label>Irangamimerere:</label>
<input type="text" id="familyMaritalStatus" readonly><br>
<label>Uwo bashyingiranwe:</label>
<input type="text" placeholder="Shyiramo izina ry'uwo bashyingiranwe" id="spouseInput" aria-label="Izina ry'uwo bashyingiranwe"><br>
<label>Umwaka bashyingiranwe:</label>
<select id="marriageYearSelect" aria-label="Hitamo umwaka bashyingiranwe"><option value="">--Hitamo Umwaka--</option></select><br>
<label>Umubare w'abana:</label>
<input type="number" placeholder="Shyiramo umubare w'abana" id="childrenInput" min="0" aria-label="Umubare w'abana"><br>
<div id="childrenNamesContainer">
  <label>Amazina y'Abana:</label><br>
  <!-- Child name inputs will be added dynamically -->
</div>
<button type="button" id="addChildName" aria-label="Ongera Izina ry'Umwana">Ongera Umwana</button>
<button type="button" id="backToDataFromFamily" aria-label="Subira ku Abakristo Data">Subira Inyuma</button>
<button type="submit" aria-label="Bika Amakuru y'Umuryango">Bika</button>
</form>
</div>
</div>

<!-- Data Page -->
<div id="dataPage" class="hidden">
<h2>Abakristo Saved Data</h2>
<label class="filter-label">Sura kuri Presbytery:</label>
<select id="filterPresby" aria-label="Sura kuri Presbytery"><option value="">--Byose--</option></select>
<label class="filter-label">Sura kuri Paruwasi:</label>
<select id="filterParish" aria-label="Sura kuri Paruwasi"><option value="">--Byose--</option></select><br>
<button id="toggleView" aria-label="Reba Abakristo">Reba Abakristo</button>
<button id="downloadCSV" aria-label="Kuramo CSV">Kuramo CSV</button>
<button id="shareData" aria-label="Sambaza Amakuru">Sambaza</button>
<button id="openArchiveBtn" aria-label="Reba Archive">Reba Archive</button>
<!-- Chart Container -->
<div id="chartContainer">
<canvas id="memberChart"></canvas>
</div>
<!-- Members Table -->
<div id="memberListContainer" class="hidden">
<table id="memberTable">
<thead>
<tr>
<th>Presbytery</th><th>Paruwasi</th><th>Ishuri</th>
<th>Itorero ry'ibanze</th><th>No</th>
<th>Irangamuntu</th><th>Amazina</th><th>Igitsina</th><th>Ububatizo</th><th>Umwaka w'Amavuko</th><th>Telefone</th>
<th>Urwego rw'amashuri</th><th>Icyo akora</th><th>Amazina y'Abana</th><th>Ibikorwa</th>
</tr>
</thead>
<tbody id="memberTableBody"></tbody>
</table>
<div id="pagination"></div>
</div>
<button id="backToMainFromData" aria-label="Subira ku rubuga rw'ibanze">Subira Inyuma</button>
</div>

<!-- Archive Page -->
<div id="archivePage" class="hidden">
<h2>Abakristo mu Bubiko</h2>
<div id="archiveListContainer">
<table id="archiveTable">
<thead>
<tr>
<th>Presbytery</th><th>Paruwasi</th><th>Ishuri</th>
<th>Itorero ry'ibanze</th><th>No</th>
<th>Irangamuntu</th><th>Amazina</th><th>Igitsina</th><th>Ububatizo</th><th>Umwaka w'Amavuko</th><th>Telefone</th>
<th>Urwego rw'amashuri</th><th>Icyo akora</th><th>Amazina y'Abana</th><th>Ibikorwa</th>
</tr>
</thead>
<tbody id="archiveTableBody"></tbody>
</table>
</div>
<button id="backFromArchive" aria-label="Subira Inyuma">Subira Inyuma</button>
</div>

<!-- Transfer Modal -->
<div id="overlay" class="hidden"></div>
<div id="transferModal" class="hidden">
<span class="close-btn" id="closeTransfer" aria-label="Funga Modal">&times;</span>
<h3>Kohereza Umukristo</h3>
<label>Presbytery:</label>
<select id="transferPresby" aria-label="Hitamo Presbytery"></select><br>
<label>Paruwasi:</label>
<select id="transferParish" aria-label="Hitamo Paruwasi"></select><br>
<label>Ishuri:</label>
<select id="transferSchool" aria-label="Hitamo Ishuri"></select><br>
<label>Itorero ry'ibanze:</label>
<select id="transferSchool2" aria-label="Hitamo Itorero ry'ibanze"></select><br>
<label>Urwego rw'amashuri yize:</label>
<select id="transferEducation" aria-label="Hitamo urwego rw'amashuri yize">
  <option value="">---Hitamo---</option>
  <option value="Ntazi gusoma">Ntazi gusoma</option>
  <option value="Isomero">Isomero</option>
  <option value="Abanza">Abanza</option>
  <option value="Ayisumbuye">Ayisumbuye</option>
  <option value="Kaminuza">Kaminuza</option>
  <option value="Masters">Masters</option>
  <option value="PHD">PHD</option>
  <option value="Professor">Professor</option>
</select><br>
<label>Icyo akora:</label>
<select id="transferJob" aria-label="Hitamo icyo akora">
  <option value="">---Hitamo---</option>
  <option value="Ntakazi agira">Ntakazi agira</option>
  <option value="Arikorera">Arikorera</option>
  <option value="Akorera abandi">Akorera abandi</option>
  <option value="Akorera leta">Akorera leta</option>
  <option value="Akorera NGOs">Akorera NGOs</option>
</select><br>
<label>Uwo bashyingiranwe:</label>
<input type="text" id="transferSpouse" aria-label="Izina ry'uwo bashyingiranwe"><br>
<label>Umwaka bashyingiranwe:</label>
<select id="transferMarriageYear" aria-label="Hitamo umwaka bashyingiranwe"><option value="">--Hitamo Umwaka--</option></select><br>
<label>Umubare w'abana:</label>
<input type="number" id="transferChildren" min="0" aria-label="Umubare w'abana"><br>
<div id="transferChildrenNamesContainer">
  <label>Amazina y'Abana:</label><br>
  <!-- Child name inputs will be added dynamically -->
</div>
<button type="button" id="addTransferChildName" aria-label="Ongera Izina ry'Umwana">Ongera Umwana</button>
<button id="transferSave" aria-label="Bika Amakuru">Bika</button>
<button id="transferCancel" aria-label="Hagarika">Hagarika</button>
</div>

<script>
// Data
let members = [];
let archive = [];
try {
  members = JSON.parse(localStorage.getItem('members')) || [];
  archive = JSON.parse(localStorage.getItem('archive')) || [];
  // Migrate existing data to include education, job, and family fields
  members = members.map(m => ({ 
    ...m, 
    education: m.education || '', 
    job: m.job || '', 
    maritalStatus: m.maritalStatus || '', 
    spouse: m.spouse || '', 
    marriageYear: m.marriageYear || '', 
    children: m.children || '0',
    childNames: m.childNames || [] // New field for child names
  }));
  archive = archive.map(m => ({ 
    ...m, 
    education: m.education || '', 
    job: m.job || '', 
    maritalStatus: m.maritalStatus || '',
    spouse: m.spouse || '', 
    marriageYear: m.marriageYear || '', 
    children: m.children || '0',
    childNames: m.childNames || []
  }));
  localStorage.setItem('members', JSON.stringify(members));
  localStorage.setItem('archive', JSON.stringify(archive));
} catch (e) {
  console.error('Error accessing localStorage:', e);
}

let currentPresbytery = '', currentParish = '';
let editingIndex = -1;
let transferIndex = -1;
let familyEditIndex = -1;

const presbyteries = {
  "GISENYI": ["Gacuba", "Kayove", "Rugarama", "Karisimbi", "Kigarama", "Bushaka", "Rubavu", "Kinanira", "Nyabirasi", "Mukingo", "Kijote", "Nkuri", "Gahondogo", "Ruhengeri", "Kidaho", "Nyarutovu", "Ramba", "Mutake", "Karambo", "Giciye", "Nganzo", "Buganamana"],
  "KIGALI": ["Kamuhoza", "Kacyiru", "Kigali-Central"],
  "RUBENGERA": ["Sure", "Rubengera", "Gashashi"],
  "KIRINDA": ["Kuruganda", "Kirinda-Central", "Ngoma"],
  "GITARAMA": ["Mututu", "Kabgayi", "Nyamagana"],
  "REMERA": ["Bubazi", "Remera-Central", "Ndera"],
  "ZINGA": ["Kibungo", "Rukira", "Gahini"]
};

const schools = ["Gacuba", "Kabirizi", "Rugerero", "Basa"];
const localChurches = {
  "Gacuba": ["Gikarani", "Nengo", "Nyakabungo", "Makoro", "Mbugangali", "Buhuru", "Kanembwe"],
  "Kabirizi": ["Kiroji", "Rohero"],
  "Rugerero": ["Pfunda", "Rugerero", "Ruhangiro", "Gisa"],
  "Basa": ["Nyaruhengeri", "Gahinga", "Mwumba"]
};

// DOM Elements
const startBtn = document.getElementById('startBtn');
const viewDataBtn = document.getElementById('viewDataBtn');
const presbyteriesPage = document.getElementById('presbyteriesPage');
const memberFormPage = document.getElementById('memberFormPage');
const dataPage = document.getElementById('dataPage');
const familyInfoPage = document.getElementById('familyInfoPage');
const presbyList = document.getElementById('presbyList');
const backToMain = document.getElementById('backToMain');
const backToPresby = document.getElementById('backToPresby');
const backToMainFromData = document.getElementById('backToMainFromData');
const backToDataFromFamily = document.getElementById('backToDataFromFamily');
const schoolSelect = document.getElementById('schoolSelect');
const schoolSelect2 = document.getElementById('schoolSelect2');
const yearSelect = document.getElementById('yearSelect');
const educationSelect = document.getElementById('educationSelect');
const jobStatusSelect = document.getElementById('jobStatusSelect');
const familyForm = document.getElementById('familyForm');
const familyName = document.getElementById('familyName');
const familyMaritalStatus = document.getElementById('familyMaritalStatus');
const spouseInput = document.getElementById('spouseInput');
const marriageYearSelect = document.getElementById('marriageYearSelect');
const childrenInput = document.getElementById('childrenInput');
const childrenNamesContainer = document.getElementById('childrenNamesContainer');
const addChildNameBtn = document.getElementById('addChildName');
const filterPresby = document.getElementById('filterPresby');
const filterParish = document.getElementById('filterParish');
const tableBody = document.getElementById('memberTableBody');
const tableContainer = document.getElementById('memberListContainer');
const archivePage = document.getElementById('archivePage');
const archiveTableBody = document.getElementById('archiveTableBody');
const backFromArchive = document.getElementById('backFromArchive');
const openArchiveBtn = document.getElementById('openArchiveBtn');
const overlay = document.getElementById('overlay');
const transferModal = document.getElementById('transferModal');
const transferPresby = document.getElementById('transferPresby');
const transferParish = document.getElementById('transferParish');
const transferSchool = document.getElementById('transferSchool');
const transferSchool2 = document.getElementById('transferSchool2');
const transferEducation = document.getElementById('transferEducation');
const transferJob = document.getElementById('transferJob');
const transferSpouse = document.getElementById('transferSpouse');
const transferMarriageYear = document.getElementById('transferMarriageYear');
const transferChildren = document.getElementById('transferChildren');
const transferChildrenNamesContainer = document.getElementById('transferChildrenNamesContainer');
const addTransferChildNameBtn = document.getElementById('addTransferChildName');
const transferSave = document.getElementById('transferSave');
const transferCancel = document.getElementById('transferCancel');
const closeTransfer = document.getElementById('closeTransfer');
const autoNoField = document.getElementById('autoNo');
const memberForm = document.getElementById('memberForm');

// Populate dropdowns
schools.forEach(s => {
  let o = document.createElement('option');
  o.textContent = s;
  o.value = s;
  schoolSelect.appendChild(o);
  let t = document.createElement('option');
  t.textContent = s;
  t.value = s;
  transferSchool.appendChild(t);
});

function updateLocalChurchesDropdown() {
  schoolSelect2.innerHTML = '<option value="">--Hitamo Itorero--</option>';
  const selectedSchool = schoolSelect.value;
  if (selectedSchool && localChurches[selectedSchool]) {
    localChurches[selectedSchool].forEach(c => {
      let o = document.createElement('option');
      o.textContent = c;
      o.value = c;
      schoolSelect2.appendChild(o);
    });
  }
}
schoolSelect.onchange = updateLocalChurchesDropdown;

for (let y = 1900; y <= 2025; y++) {
  let o = document.createElement('option');
  o.textContent = y;
  o.value = y;
  yearSelect.appendChild(o);
  let m = document.createElement('option');
  m.textContent = y;
  m.value = y;
  marriageYearSelect.appendChild(m);
  let t = document.createElement('option');
  t.textContent = y;
  t.value = y;
  transferMarriageYear.appendChild(t);
}

Object.keys(presbyteries).forEach(p => {
  let o = document.createElement('option');
  o.textContent = p;
  o.value = p;
  filterPresby.appendChild(o);
});

// Generate unique member number
function getNextMemberNo() {
  const allNos = [...members, ...archive].map(m => parseInt(m.no)).filter(n => !isNaN(n));
  return allNos.length ? Math.max(...allNos) + 1 : 1;
}

// Dynamic Child Name Inputs for Family Form
function updateChildNameInputs(numChildren, existingNames = []) {
  childrenNamesContainer.innerHTML = '<label>Amazina y\'Abana:</label><br>';
  for (let i = 0; i < numChildren; i++) {
    const input = document.createElement('input');
    input.type = 'text';
    input.placeholder = `Izina ry'Umwana ${i + 1}`;
    input.value = existingNames[i] || '';
    input.className = 'child-name-input';
    input.setAttribute('aria-label', `Izina ry'Umwana ${i + 1}`);
    childrenNamesContainer.appendChild(input);
    childrenNamesContainer.appendChild(document.createElement('br'));
  }
}

childrenInput.addEventListener('change', function() {
  const numChildren = parseInt(childrenInput.value) || 0;
  updateChildNameInputs(numChildren, members[familyEditIndex]?.childNames || []);
});

addChildNameBtn.onclick = function() {
  const numChildren = parseInt(childrenInput.value) || 0;
  childrenInput.value = numChildren + 1;
  updateChildNameInputs(numChildren + 1, members[familyEditIndex]?.childNames || []);
};

// Dynamic Child Name Inputs for Transfer Modal
function updateTransferChildNameInputs(numChildren, existingNames = []) {
  transferChildrenNamesContainer.innerHTML = '<label>Amazina y\'Abana:</label><br>';
  for (let i = 0; i < numChildren; i++) {
    const input = document.createElement('input');
    input.type = 'text';
    input.placeholder = `Izina ry'Umwana ${i + 1}`;
    input.value = existingNames[i] || '';
    input.className = 'transfer-child-name-input';
    input.setAttribute('aria-label', `Izina ry'Umwana ${i + 1}`);
    transferChildrenNamesContainer.appendChild(input);
    transferChildrenNamesContainer.appendChild(document.createElement('br'));
  }
}

transferChildren.addEventListener('change', function() {
  const numChildren = parseInt(transferChildren.value) || 0;
  updateTransferChildNameInputs(numChildren, members[transferIndex]?.childNames || []);
});

addTransferChildNameBtn.onclick = function() {
  const numChildren = parseInt(transferChildren.value) || 0;
  transferChildren.value = numChildren + 1;
  updateTransferChildNameInputs(numChildren + 1, members[transferIndex]?.childNames || []);
};

// Navigation
startBtn.onclick = () => {
  startBtn.classList.add('hidden');
  viewDataBtn.classList.add('hidden');
  presbyteriesPage.classList.remove('hidden');
  renderPresbyteries();
};

backToMain.onclick = () => {
  presbyteriesPage.classList.add('hidden');
  startBtn.classList.remove('hidden');
  viewDataBtn.classList.remove('hidden');
};

viewDataBtn.onclick = () => {
  startBtn.classList.add('hidden');
  viewDataBtn.classList.add('hidden');
  dataPage.classList.remove('hidden');
  updateFilterParish();
  renderTable();
  renderChart();
};

backToMainFromData.onclick = () => {
  dataPage.classList.add('hidden');
  tableContainer.classList.add('hidden');
  startBtn.classList.remove('hidden');
  viewDataBtn.classList.remove('hidden');
};

backToPresby.onclick = () => {
  memberFormPage.classList.add('hidden');
  presbyteriesPage.classList.remove('hidden');
};

backToDataFromFamily.onclick = () => {
  familyInfoPage.classList.add('hidden');
  dataPage.classList.remove('hidden');
  tableContainer.classList.remove('hidden');
  renderTable();
};

// Render presbyteries
function renderPresbyteries() {
  presbyList.innerHTML = '';
  for (const presby in presbyteries) {
    const p = document.createElement('p');
    p.innerHTML = `<strong>${presby}</strong>`;
    presbyList.appendChild(p);
    const label = document.createElement('label');
    label.textContent = 'Hitamo Paruwasi:';
    presbyList.appendChild(label);
    const select = document.createElement('select');
    select.setAttribute('aria-label', `Hitamo Paruwasi ya ${presby}`);
    const defaultOpt = document.createElement('option');
    defaultOpt.value = '';
    defaultOpt.textContent = '--Hitamo--';
    select.appendChild(defaultOpt);
    presbyteries[presby].forEach(parish => {
      let o = document.createElement('option');
      o.value = parish;
      o.textContent = parish;
      select.appendChild(o);
    });
    select.onchange = function() {
      if (this.value) {
        currentPresbytery = presby;
        currentParish = this.value;
        memberFormPage.classList.remove('hidden');
        presbyteriesPage.classList.add('hidden');
        if (editingIndex < 0) autoNoField.value = getNextMemberNo();
        updateLocalChurchesDropdown();
      }
    };
    presbyList.appendChild(select);
  }
}

// Member Form Submit
memberForm.addEventListener('submit', function(e) {
  e.preventDefault();
  const idInput = document.getElementById('idInput').value;
  if (members.some(m => m.id === idInput && members.indexOf(m) !== editingIndex)) {
    alert('Irangamuntu iri mu bice byabitswe! Hitamo irindi.');
    return;
  }
  const selectedSchool = document.getElementById('schoolSelect').value;
  const selectedLocalChurch = document.getElementById('schoolSelect2').value;
  const selectedEducation = document.getElementById('educationSelect').value;
  const selectedJob = document.getElementById('jobStatusSelect').value;
  const selectedMaritalStatus = document.getElementById('MartalstatusSelect').value;
  if (!selectedSchool || !selectedLocalChurch || !selectedEducation || !selectedJob) {
    alert('Ugomba guhitamo Ishuri, Itorero ry\'ibanze, Urwego rw\'amashuri, na Icyo akora!');
    return;
  }
  const memberData = {
    presbytery: currentPresbytery,
    parish: currentParish,
    no: autoNoField.value,
    school: selectedSchool,
    school2: selectedLocalChurch,
    id: idInput,
    name: document.getElementById('nameInput').value,
    gender: document.getElementById('genderSelect').value,
    maritalStatus: selectedMaritalStatus,
    baptism: document.getElementById('baptismSelect').value,
    birthYear: document.getElementById('yearSelect').value,
    phone: document.getElementById('phoneInput').value,
    education: selectedEducation,
    job: selectedJob,
    spouse: '',
    marriageYear: '',
    children: '0',
    childNames: []
  };
  if (editingIndex >= 0) {
    memberData.spouse = members[editingIndex].spouse || '';
    memberData.marriageYear = members[editingIndex].marriageYear || '';
    memberData.children = members[editingIndex].children || '0';
    memberData.childNames = members[editingIndex].childNames || [];
    members[editingIndex] = memberData;
    editingIndex = -1;
    alert('Amakuru yahinduwe!');
  } else {
    members.push(memberData);
    alert('Amakuru yabitswe!');
  }
  try {
    localStorage.setItem('members', JSON.stringify(members));
  } catch (e) {
    console.error('Error saving to localStorage:', e);
  }
  memberForm.reset();
  autoNoField.value = getNextMemberNo();
  updateFilterParish();
  renderTable();
  renderChart();
});

// Family Form Submit
familyForm.addEventListener('submit', function(e) {
  e.preventDefault();
  if (familyEditIndex >= 0) {
    const maritalStatus = members[familyEditIndex].maritalStatus;
    const spouse = spouseInput.value;
    const marriageYear = marriageYearSelect.value;
    const children = childrenInput.value || '0';
    const childNames = Array.from(document.querySelectorAll('.child-name-input')).map(input => input.value.trim()).filter(name => name !== '');
    if ((maritalStatus === 'arubatse' || maritalStatus === 'divorce') && marriageYear === '') {
      alert('Igihe bashyingiranwe kirakenewe iyo irangamimerere ni Arubatse cyangwa Divorce!');
      return;
    }
    if (parseInt(children) < childNames.length) {
      alert('Umubare w\'abana ugomba kuba fungana na amazina yabana wuzuye!');
      return;
    }
    members[familyEditIndex].spouse = spouse;
    members[familyEditIndex].marriageYear = marriageYear;
    members[familyEditIndex].children = children;
    members[familyEditIndex].childNames = childNames;
    try {
      localStorage.setItem('members', JSON.stringify(members));
    } catch (e) {
      console.error('Error saving to localStorage:', e);
    }
    alert('Amakuru y\'umuryango yabitswe!');
    familyForm.reset();
    updateChildNameInputs(0);
    familyInfoPage.classList.add('hidden');
    dataPage.classList.remove('hidden');
    tableContainer.classList.remove('hidden');
    renderTable();
  }
});

// Render Members Table with Pagination
function renderTable(page = 1, pageSize = 10) {
  const presbyFilter = filterPresby.value;
  const parishFilter = filterParish.value;
  tableBody.innerHTML = '';
  const filtered = members.filter(m =>
    (presbyFilter === '' || m.presbytery === presbyFilter) &&
    (parishFilter === '' || m.parish === parishFilter)
  );
  const start = (page - 1) * pageSize;
  const end = start + pageSize;
  const paginated = filtered.slice(start, end);
  if (paginated.length === 0) {
    const tr = document.createElement('tr');
    const td = document.createElement('td');
    td.colSpan = 15;
    td.textContent = 'Nta makuru yabitswe';
    tr.appendChild(td);
    tableBody.appendChild(tr);
  } else {
    paginated.forEach((m, index) => {
      const tr = document.createElement('tr');
      ['presbytery', 'parish', 'school', 'school2', 'no', 'id', 'name', 'gender', 'baptism', 'birthYear', 'phone', 'education', 'job', 'childNames'].forEach(key => {
        const td = document.createElement('td');
        td.textContent = key === 'childNames' ? (m[key] || []).join(', ') : m[key] || '';
        tr.appendChild(td);
      });
      const tdActions = document.createElement('td');
      const editBtn = document.createElement('button');
      editBtn.textContent = 'Hindura';
      editBtn.className = 'action-btn';
      editBtn.setAttribute('aria-label', `Hindura amakuru ya ${m.name}`);
      editBtn.onclick = function() {
        autoNoField.value = m.no;
        document.getElementById('schoolSelect').value = m.school;
        updateLocalChurchesDropdown();
        document.getElementById('schoolSelect2').value = m.school2;
        document.getElementById('idInput').value = m.id;
        document.getElementById('nameInput').value = m.name;
        document.getElementById('genderSelect').value = m.gender;
        document.getElementById('MartalstatusSelect').value = m.maritalStatus;
        document.getElementById('baptismSelect').value = m.baptism;
        document.getElementById('yearSelect').value = m.birthYear;
        document.getElementById('phoneInput').value = m.phone;
        document.getElementById('educationSelect').value = m.education || '';
        document.getElementById('jobStatusSelect').value = m.job || '';
        currentPresbytery = m.presbytery;
        currentParish = m.parish;
        editingIndex = members.indexOf(m);
        memberFormPage.classList.remove('hidden');
        dataPage.classList.add('hidden');
      };
      tdActions.appendChild(editBtn);
      const delBtn = document.createElement('button');
      delBtn.textContent = 'Siba';
      delBtn.className = 'action-btn';
      delBtn.setAttribute('aria-label', `Siba amakuru ya ${m.name}`);
      delBtn.onclick = function() {
        if (confirm(`Urashaka gusiba ${m.name}?`)) {
          members.splice(members.indexOf(m), 1);
          try {
            localStorage.setItem('members', JSON.stringify(members));
          } catch (e) {
            console.error('Error saving to localStorage:', e);
          }
          renderTable(page);
          renderChart();
        }
      };
      tdActions.appendChild(delBtn);
      const archBtn = document.createElement('button');
      archBtn.textContent = 'Bika mu Bubiko';
      archBtn.className = 'action-btn';
      archBtn.setAttribute('aria-label', `Bika ${m.name} mu bubiko`);
      archBtn.onclick = function() {
        archive.push(m);
        members.splice(members.indexOf(m), 1);
        try {
          localStorage.setItem('members', JSON.stringify(members));
          localStorage.setItem('archive', JSON.stringify(archive));
        } catch (e) {
          console.error('Error saving to localStorage:', e);
        }
        renderTable(page);
        renderChart();
      };
      tdActions.appendChild(archBtn);
      const transBtn = document.createElement('button');
      transBtn.textContent = 'Ohereza';
      transBtn.className = 'action-btn';
      transBtn.setAttribute('aria-label', `Ohereza ${m.name} ku yandi paruwasi`);
      transBtn.onclick = function() {
        transferIndex = members.indexOf(m);
        openTransferModal(m);
      };
      tdActions.appendChild(transBtn);
      const familyBtn = document.createElement('button');
      familyBtn.textContent = 'Umuryango';
      familyBtn.className = 'action-btn';
      familyBtn.setAttribute('aria-label', `Reba amakuru y'umuryango ya ${m.name}`);
      familyBtn.onclick = function() {
        familyEditIndex = members.indexOf(m);
        familyName.value = m.name;
        familyMaritalStatus.value = m.maritalStatus || '';
        spouseInput.value = m.spouse || '';
        marriageYearSelect.value = m.marriageYear || '';
        childrenInput.value = m.children || '0';
        updateChildNameInputs(parseInt(m.children) || 0, m.childNames || []);
        dataPage.classList.add('hidden');
        familyInfoPage.classList.remove('hidden');
      };
      tdActions.appendChild(familyBtn);
      tr.appendChild(tdActions);
      tableBody.appendChild(tr);
    });
  }
  const pagination = document.getElementById('pagination');
  pagination.innerHTML = `
    <button onclick="renderTable(${page - 1})" ${page === 1 ? 'disabled' : ''} aria-label="Subira ku ipage ry'inyuma">Ibanze</button>
    <span>Page ${page}</span>
    <button onclick="renderTable(${page + 1})" ${end >= filtered.length ? 'disabled' : ''} aria-label="Genda ku ipage rikurikira">Ibikurikira</button>
  `;
}

// Filter Parish
function updateFilterParish() {
  filterParish.innerHTML = '';
  let defaultOpt = document.createElement('option');
  defaultOpt.value = '';
  defaultOpt.textContent = '--Byose--';
  filterParish.appendChild(defaultOpt);
  const presby = filterPresby.value;
  let parishesList = [];
  if (presby === '') {
    for (let p in presbyteries) {
      parishesList = parishesList.concat(presbyteries[p]);
    }
  } else {
    parishesList = presbyteries[presby];
  }
  parishesList.forEach(par => {
    let o = document.createElement('option');
    o.value = par;
    o.textContent = par;
    filterParish.appendChild(o);
  });
}

filterPresby.onchange = () => {
  updateFilterParish();
  renderTable();
};

filterParish.onchange = () => {
  renderTable();
};

// Toggle Table
document.getElementById('toggleView').onclick = function() {
  if (tableContainer.classList.contains('hidden')) {
    tableContainer.classList.remove('hidden');
    renderTable();
  } else {
    tableContainer.classList.add('hidden');
  }
};

// CSV Download
document.getElementById('downloadCSV').onclick = function() {
  if (members.length === 0) {
    alert('Nta makuru yabitswe');
    return;
  }
  const csvHeader = [...Object.keys(members[0]).filter(k => k !== 'childNames'), 'childNames'].join(',') + '\n';
  const csvRows = members.map(m => {
    const values = Object.values(m).map((v, i) => {
      if (Object.keys(m)[i] === 'childNames') {
        return `"${(v || []).join(';')}"`;
      }
      return `"${v || ''}"`;
    });
    return values.join(',');
  }).join('\n');
  const bom = '\uFEFF';
  const blob = new Blob([bom + csvHeader + csvRows], { type: 'text/csv;charset=utf-8' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'abakristo.csv';
  a.click();
  URL.revokeObjectURL(url);
};

// Share
document.getElementById('shareData').onclick = function() {
  if (members.length === 0) {
    alert('Nta makuru yabitswe');
    return;
  }
  const textData = members.map(m => 
    `${m.no} - ${m.name} - ${m.school} - ${m.school2} - ${m.education} - ${m.job} - ${m.spouse || ''} - ${m.marriageYear || ''} - ${m.children || '0'} - ${(m.childNames || []).join(', ')}`
  ).join('\n');
  if (navigator.share) {
    navigator.share({ title: 'Abakristo Data', text: textData }).catch(console.error);
  } else {
    alert('Sambaza ntishyigikirwa');
  }
};

// Archive Page
openArchiveBtn.onclick = function() {
  dataPage.classList.add('hidden');
  archivePage.classList.remove('hidden');
  renderArchive();
};

backFromArchive.onclick = function() {
  archivePage.classList.add('hidden');
  dataPage.classList.remove('hidden');
};

function renderArchive() {
  archiveTableBody.innerHTML = '';
  if (archive.length === 0) {
    const tr = document.createElement('tr');
    const td = document.createElement('td');
    td.colSpan = 15;
    td.textContent = 'Nta makuru mu bubiko';
    tr.appendChild(td);
    archiveTableBody.appendChild(tr);
    return;
  }
  archive.forEach(m => {
    const tr = document.createElement('tr');
    ['presbytery', 'parish', 'school', 'school2', 'no', 'id', 'name', 'gender', 'baptism', 'birthYear', 'phone', 'education', 'job', 'childNames'].forEach(k => {
      let td = document.createElement('td');
      td.textContent = k === 'childNames' ? (m[k] || []).join(', ') : m[k] || '';
      tr.appendChild(td);
    });
    const tdActions = document.createElement('td');
    const resBtn = document.createElement('button');
    resBtn.textContent = 'Garura';
    resBtn.setAttribute('aria-label', `Garura ${m.name} mu bubiko`);
    resBtn.onclick = function() {
      members.push(m);
      archive.splice(archive.indexOf(m), 1);
      try {
        localStorage.setItem('members', JSON.stringify(members));
        localStorage.setItem('archive', JSON.stringify(archive));
      } catch (e) {
        console.error('Error saving to localStorage:', e);
      }
      renderArchive();
      renderChart();
    };
    tdActions.appendChild(resBtn);
    tr.appendChild(tdActions);
    archiveTableBody.appendChild(tr);
  });
}

// Transfer
function openTransferModal(m) {
  overlay.classList.remove('hidden');
  transferModal.classList.remove('hidden');
  transferPresby.innerHTML = '';
  Object.keys(presbyteries).forEach(p => {
    let o = document.createElement('option');
    o.value = p;
    o.textContent = p;
    transferPresby.appendChild(o);
  });
  transferPresby.value = m.presbytery;
  transferPresby.onchange = function() {
    transferParish.innerHTML = '';
    presbyteries[transferPresby.value].forEach(par => {
      let o = document.createElement('option');
      o.value = par;
      o.textContent = par;
      transferParish.appendChild(o);
    });
  };
  transferPresby.onchange();
  transferParish.value = m.parish;
  transferSchool.innerHTML = '';
  schools.forEach(s => {
    let o = document.createElement('option');
    o.value = s;
    o.textContent = s;
    transferSchool.appendChild(o);
  });
  transferSchool.value = m.school;
  transferSchool2.innerHTML = '<option value="">--Hitamo Itorero--</option>';
  if (m.school && localChurches[m.school]) {
    localChurches[m.school].forEach(c => {
      let o = document.createElement('option');
      o.value = c;
      o.textContent = c;
      transferSchool2.appendChild(o);
    });
  }
  transferSchool2.value = m.school2;
  transferEducation.value = m.education || '';
  transferJob.value = m.job || '';
  transferSpouse.value = m.spouse || '';
  transferMarriageYear.value = m.marriageYear || '';
  transferChildren.value = m.children || '0';
  updateTransferChildNameInputs(parseInt(m.children) || 0, m.childNames || []);
  transferSchool.onchange = function() {
    transferSchool2.innerHTML = '<option value="">--Hitamo Itorero--</option>';
    const selectedSchool = transferSchool.value;
    if (selectedSchool && localChurches[selectedSchool]) {
      localChurches[selectedSchool].forEach(c => {
        let o = document.createElement('option');
        o.value = c;
        o.textContent = c;
        transferSchool2.appendChild(o);
      });
    }
  };
}

transferSave.onclick = function() {
  if (transferIndex >= 0) {
    const selectedSchool = transferSchool.value;
    const selectedLocalChurch = transferSchool2.value;
    const selectedEducation = transferEducation.value;
    const selectedJob = transferJob.value;
    const selectedSpouse = transferSpouse.value;
    const selectedMarriageYear = transferMarriageYear.value;
    const selectedChildren = transferChildren.value || '0';
    const selectedChildNames = Array.from(document.querySelectorAll('.transfer-child-name-input')).map(input => input.value.trim()).filter(name => name !== '');
    if (!selectedSchool || !selectedLocalChurch || !selectedEducation || !selectedJob) {
      alert('Ugomba guhitamo Ishuri, Itorero ry\'ibanze, Urwego rw\'amashuri, na Icyo akora!');
      return;
    }
    if ((members[transferIndex].maritalStatus === 'arubatse' || members[transferIndex].maritalStatus === 'divorce') && selectedMarriageYear === '') {
      alert('Igihe bashyingiranwe kirakenewe iyo irangamimerere ni Arubatse cyangwa Divorce!');
      return;
    }
    if (parseInt(selectedChildren) < selectedChildNames.length) {
      alert('Umubare w\'abana ugomba kuba fungana na amazina yabana wuzuye!');
      return;
    }
    members[transferIndex].presbytery = transferPresby.value;
    members[transferIndex].parish = transferParish.value;
    members[transferIndex].school = selectedSchool;
    members[transferIndex].school2 = selectedLocalChurch;
    members[transferIndex].education = selectedEducation;
    members[transferIndex].job = selectedJob;
    members[transferIndex].spouse = selectedSpouse;
    members[transferIndex].marriageYear = selectedMarriageYear;
    members[transferIndex].children = selectedChildren;
    members[transferIndex].childNames = selectedChildNames;
    try {
      localStorage.setItem('members', JSON.stringify(members));
    } catch (e) {
      console.error('Error saving to localStorage:', e);
    }
    alert('Kohereza byagenze neza!');
    renderChart();
  }
  transferIndex = -1;
  closeTransferModal();
  renderTable();
};

transferCancel.onclick = closeTransferModal;
closeTransfer.onclick = closeTransferModal;

function closeTransferModal() {
  overlay.classList.add('hidden');
  transferModal.classList.add('hidden');
}

// Render Bar Chart
let chartInstance = null;
function renderChart() {
  const ctx = document.getElementById('memberChart').getContext('2d');
  const presbyCounts = Object.keys(presbyteries).map(presby => ({
    presby,
    count: members.filter(m => m.presbytery === presby).length
  }));
  const labels = presbyCounts.map(p => p.presby);
  const data = presbyCounts.map(p => p.count);
  if (chartInstance) {
    chartInstance.destroy();
  }
  chartInstance = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: labels,
      datasets: [{
        label: 'Umubare w’Abakristo muri Presbytery',
        data: data,
        backgroundColor: '#12984c',
        borderColor: '#1ac564',
        borderWidth: 1
      }]
    },
    options: {
      scales: {
        y: { beginAtZero: true, title: { display: true, text: 'Umubare w’Abakristo' } },
        x: { title: { display: true, text: 'Presbytery' } }
      },
      plugins: { legend: { display: true } }
    }
  });
}

// Initial
updateFilterParish();
renderTable();
renderChart();
</script>

</body>
</html>



