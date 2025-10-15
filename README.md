<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>PCR-EPR Member Management</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.13.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.13.1/firebase-firestore-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.13.1/firebase-auth-compat.js"></script>
  <style>
    body { font-family: "Times New Roman", sans-serif; background: #b0cdea; text-align: center; margin: 20px; }
    h1, h2 { color: #12984c; }
    select, input { padding: 8px; margin: 5px 0; width: 60%; border: 1px solid #ccc; border-radius: 5px; }
    button { padding: 6px 12px; margin: 4px; border: none; border-radius: 5px; cursor: pointer; }
    button:hover { background: #14ac56; color: #fff; }
    .hidden { display: none; }
    .form-container { background: #f4f6f8; padding: 20px; border-radius: 8px; margin-top: 20px; display: inline-block; text-align: left; }
    table { margin: auto; background: #fff; border-collapse: collapse; width: auto; max-width: 100%; display: block; overflow-x: auto; }
    table, th, td { border: 1px solid #999; }
    th, td { padding: 5px; text-align: center; white-space: nowrap; }
    #memberListContainer, #archivePage, #transferModal, #chartContainer, #dataPage, #familyInfoPage { margin-top: 20px; }
    label.filter-label { margin-left: 5px; }
    .action-btn { margin: 0 2px; }
    #transferModal { position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: #f4f6f8; padding: 20px; border-radius: 8px; z-index: 1000; min-width: 300px; }
    #overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(242, 199, 199, 0.4); z-index: 900; }
    .close-btn { float: right; cursor: pointer; font-weight: bold; color: #f00; }
    #chartContainer { width: 80%; margin: auto; }
    canvas { max-width: 100%; }
    .verification-message { color: #12984c; margin-top: 10px; }
  </style>
</head>
<body>
  <!-- Login Page -->
  <div id="loginPage" class="form-container">
    <h2>Login to PCR-EPR</h2>
    <form id="loginForm">
      <label>Email:</label>
      <input type="email" id="emailInput" placeholder="Enter email" required><br>
      <label>Password:</label>
      <input type="password" id="passwordInput" placeholder="Enter password" required><br>
      <button type="submit" aria-label="Login to the app">Login</button>
    </form>
    <p><a href="#" id="showSignupLink" aria-label="Switch to signup form">Don't have an account? Sign up</a></p>
    <p id="verificationMessage" class="verification-message hidden">Please verify your email with the OTP before logging in.</p>
  </div>

  <!-- Signup Page -->
  <div id="signupPage" class="form-container hidden">
    <h2>Create Account</h2>
    <form id="signupForm">
      <label>Email:</label>
      <input type="email" id="signupEmailInput" placeholder="Enter email" required><br>
      <label>Password:</label>
      <input type="password" id="signupPasswordInput" placeholder="Enter password (min 6 characters)" required><br>
      <label>Confirm Password:</label>
      <input type="password" id="confirmPasswordInput" placeholder="Confirm password" required><br>
      <button type="submit" aria-label="Create account">Sign Up</button>
    </form>
    <!-- OTP Verification Section -->
    <div id="otpSection" class="form-container hidden">
      <h2>Verify OTP</h2>
      <form id="otpForm">
        <label>Enter OTP:</label>
        <input type="text" id="otpInput" placeholder="Enter 6-digit OTP" pattern="[0-9]{6}" required><br>
        <button type="submit" aria-label="Verify OTP">Verify OTP</button>
      </form>
      <p><a href="#" id="resendOtpLink" aria-label="Resend OTP">Resend OTP</a></p>
    </div>
    <p id="otpMessage" class="verification-message hidden">A 6-digit OTP has been sent to your email. Please enter it to verify your account.</p>
    <p><a href="#" id="showLoginLink" aria-label="Switch to login form">Already have an account? Login</a></p>
  </div>

  <h1>PCR-EPR Member Management</h1>
  <p>Presbyterian Church of Rwanda-EPR has 7 presbyteries.</p>
  <button id="startBtn" class="hidden" aria-label="Tangiza gukurikirana abakristo">Kanda Hano!</button>
  <button id="viewDataBtn" class="hidden" aria-label="Reba Abakristo Data">Reba Abakristo Data</button>
  <button id="logoutBtn" class="hidden" aria-label="Logout">Logout</button>

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
        <select id="maritalStatusSelect" required aria-label="Hitamo Irangamimerere">
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
    <div id="chartContainer">
      <canvas id="memberChart"></canvas>
    </div>
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
    <select id="transferParish" aria-label="Hitamo Paruwasi"><option value="">--Hitamo--</option></select><br>
    <label>Ishuri:</label>
    <select id="transferSchool" aria-label="Hitamo Ishuri"></select><br>
    <label>Itorero ry'ibanze:</label>
    <select id="transferSchool2" aria-label="Hitamo Itorero ry'ibanze"><option value="">--Hitamo Itorero--</option></select><br>
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
    </div>
    <button type="button" id="addTransferChildName" aria-label="Ongera Izina ry'Umwana">Ongera Umwana</button>
    <button id="transferSave" aria-label="Bika Amakuru">Bika</button>
    <button id="transferCancel" aria-label="Hagarika">Hagarika</button>
  </div>

  <script>
    // Firebase Configuration
    // REPLACE THIS: Go to Firebase Console > Project Settings > General > Your apps > Web app
    // Copy the firebaseConfig object and paste it here
    const firebaseConfig = {
      apiKey: "your-api-key", // Replace with your actual API key
      authDomain: "your-project-id.firebaseapp.com", // Replace with your project’s authDomain
      projectId: "your-project-id", // Replace with your project ID
      storageBucket: "your-project-id.appspot.com", // Replace with your storage bucket
      messagingSenderId: "your-messaging-sender-id", // Replace with your messaging sender ID
      appId: "your-app-id" // Replace with your app ID
    };

    // Initialize Firebase
    try {
      firebase.initializeApp(firebaseConfig);
      console.log('Firebase initialized successfully');
    } catch (e) {
      console.error('Firebase initialization error:', e);
      alert('Error initializing Firebase. Please check your configuration: ' + e.message);
      throw e;
    }
    const db = firebase.firestore();
    const auth = firebase.auth();

    // Data
    let members = [];
    let archive = [];
    let currentPresbytery = '', currentParish = '';
    let editingIndex = -1;
    let transferIndex = -1;
    let familyEditIndex = -1;
    let tempUser = null; // Store temp user data during OTP verification

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
    const loginPage = document.getElementById('loginPage');
    const loginForm = document.getElementById('loginForm');
    const signupPage = document.getElementById('signupPage');
    const signupForm = document.getElementById('signupForm');
    const otpSection = document.getElementById('otpSection');
    const otpForm = document.getElementById('otpForm');
    const resendOtpLink = document.getElementById('resendOtpLink');
    const showSignupLink = document.getElementById('showSignupLink');
    const showLoginLink = document.getElementById('showLoginLink');
    const logoutBtn = document.getElementById('logoutBtn');
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
    const otpMessage = document.getElementById('otpMessage');
    const verificationMessage = document.getElementById('verificationMessage');

    // Authentication with Email Verification
    auth.onAuthStateChanged(user => {
      if (user) {
        if (user.emailVerified) {
          loginPage.classList.add('hidden');
          signupPage.classList.add('hidden');
          otpSection.classList.add('hidden');
          startBtn.classList.remove('hidden');
          viewDataBtn.classList.remove('hidden');
          logoutBtn.classList.remove('hidden');
          verificationMessage.classList.add('hidden');
          loadMembers();
          loadArchive();
        } else {
          verificationMessage.classList.remove('hidden');
          auth.signOut(); // Force logout if not verified
        }
      } else {
        loginPage.classList.remove('hidden');
        signupPage.classList.add('hidden');
        otpSection.classList.add('hidden');
        startBtn.classList.add('hidden');
        viewDataBtn.classList.add('hidden');
        logoutBtn.classList.add('hidden');
        presbyteriesPage.classList.add('hidden');
        memberFormPage.classList.add('hidden');
        dataPage.classList.add('hidden');
        familyInfoPage.classList.add('hidden');
        archivePage.classList.add('hidden');
        transferModal.classList.add('hidden');
        overlay.classList.add('hidden');
      }
    });

    loginForm.addEventListener('submit', async e => {
      e.preventDefault();
      const email = document.getElementById('emailInput').value.trim();
      const password = document.getElementById('passwordInput').value;
      if (!email || !password) {
        alert('Please enter both email and password.');
        return;
      }
      try {
        const userCredential = await auth.signInWithEmailAndPassword(email, password);
        if (!userCredential.user.emailVerified) {
          verificationMessage.classList.remove('hidden');
          await auth.signOut();
          alert('Please verify your email with the OTP before logging in.');
        } else {
          alert('Logged in successfully!');
          loginForm.reset();
        }
      } catch (e) {
        let errorMessage;
        switch (e.code) {
          case 'auth/invalid-email':
            errorMessage = 'Invalid email format.';
            break;
          case 'auth/user-not-found':
            errorMessage = 'No user found with this email. Please sign up.';
            break;
          case 'auth/wrong-password':
            errorMessage = 'Incorrect password. Please try again.';
            break;
          case 'auth/too-many-requests':
            errorMessage = 'Too many attempts. Please try again later.';
            break;
          default:
            errorMessage = 'Login failed: ' + e.message;
        }
        alert(errorMessage);
        console.error('Login error:', e);
      }
    });

    signupForm.addEventListener('submit', async e => {
      e.preventDefault();
      const email = document.getElementById('signupEmailInput').value.trim();
      const password = document.getElementById('signupPasswordInput').value;
      const confirmPassword = document.getElementById('confirmPasswordInput').value;
      if (!email || !password || !confirmPassword) {
        alert('Please fill in all fields.');
        return;
      }
      if (password !== confirmPassword) {
        alert('Passwords do not match!');
        return;
      }
      try {
        // Create user but don't sign in yet
        const userCredential = await auth.createUserWithEmailAndPassword(email, password);
        tempUser = { uid: userCredential.user.uid, email };
        await auth.signOut(); // Sign out to prevent auto-login
        // Call Cloud Function to send OTP
        const response = await fetch('https://us-central1-your-project-id.cloudfunctions.net/sendOtp', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email })
        });
        const result = await response.json();
        if (result.success) {
          signupForm.classList.add('hidden');
          otpSection.classList.remove('hidden');
          otpMessage.classList.remove('hidden');
          alert('Account created! A 6-digit OTP has been sent to your email.');
        } else {
          throw new Error(result.error || 'Failed to send OTP');
        }
      } catch (e) {
        let errorMessage;
        switch (e.code) {
          case 'auth/email-already-in-use':
            errorMessage = 'This email is already registered. Please log in or use a different email.';
            break;
          case 'auth/invalid-email':
            errorMessage = 'Invalid email format.';
            break;
          case 'auth/weak-password':
            errorMessage = 'Password must be at least 6 characters long.';
            break;
          default:
            errorMessage = 'Sign up failed: ' + e.message;
        }
        alert(errorMessage);
        console.error('Signup error:', e);
      }
    });

    otpForm.addEventListener('submit', async e => {
      e.preventDefault();
      const otp = document.getElementById('otpInput').value.trim();
      if (!otp || otp.length !== 6) {
        alert('Please enter a valid 6-digit OTP.');
        return;
      }
      try {
        const response = await fetch('https://us-central1-your-project-id.cloudfunctions.net/verifyOtp', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email: tempUser.email, otp })
        });
        const result = await response.json();
        if (result.success) {
          // Sign in the user to update emailVerified
          await auth.signInWithEmailAndPassword(tempUser.email, document.getElementById('signupPasswordInput').value);
          await auth.currentUser.updateProfile({ emailVerified: true });
          alert('OTP verified! You can now log in.');
          otpSection.classList.add('hidden');
          loginPage.classList.remove('hidden');
          signupPage.classList.add('hidden');
          otpForm.reset();
          tempUser = null;
        } else {
          alert(result.error || 'Invalid or expired OTP.');
        }
      } catch (e) {
        alert('OTP verification failed: ' + e.message);
        console.error('OTP verification error:', e);
      }
    });

    resendOtpLink.addEventListener('click', async e => {
      e.preventDefault();
      if (!tempUser) {
        alert('No pending signup. Please sign up again.');
        return;
      }
      try {
        const response = await fetch('https://us-central1-your-project-id.cloudfunctions.net/sendOtp', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email: tempUser.email })
        });
        const result = await response.json();
        if (result.success) {
          alert('OTP resent successfully. Check your email.');
        } else {
          alert(result.error || 'Failed to resend OTP.');
        }
      } catch (e) {
        alert('Error resending OTP: ' + e.message);
        console.error('Resend OTP error:', e);
      }
    });

    showSignupLink.addEventListener('click', e => {
      e.preventDefault();
      loginPage.classList.add('hidden');
      signupPage.classList.remove('hidden');
      otpSection.classList.add('hidden');
      verificationMessage.classList.add('hidden');
    });

    showLoginLink.addEventListener('click', e => {
      e.preventDefault();
      signupPage.classList.add('hidden');
      otpSection.classList.add('hidden');
      loginPage.classList.remove('hidden');
      otpMessage.classList.add('hidden');
    });

    logoutBtn.addEventListener('click', async () => {
      try {
        await auth.signOut();
        alert('Logged out successfully');
      } catch (e) {
        alert('Logout failed: ' + e.message);
        console.error('Logout error:', e);
      }
    });

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

    for (let y = 1950; y <= 2025; y++) {
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
    async function getNextMemberNo() {
      try {
        const allMembers = await db.collection('members').get();
        const allArchive = await db.collection('archive').get();
        const allNos = [
          ...allMembers.docs.map(doc => parseInt(doc.data().no)),
          ...allArchive.docs.map(doc => parseInt(doc.data().no))
        ].filter(n => !isNaN(n));
        return allNos.length ? Math.max(...allNos) + 1 : 1;
      } catch (e) {
        console.error('Error generating member number:', e);
        alert('Error generating member number: ' + e.message);
        return 1;
      }
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
      logoutBtn.classList.add('hidden');
      presbyteriesPage.classList.remove('hidden');
      renderPresbyteries();
    };

    backToMain.onclick = () => {
      presbyteriesPage.classList.add('hidden');
      startBtn.classList.remove('hidden');
      viewDataBtn.classList.remove('hidden');
      logoutBtn.classList.remove('hidden');
    };

    viewDataBtn.onclick = () => {
      startBtn.classList.add('hidden');
      viewDataBtn.classList.add('hidden');
      logoutBtn.classList.add('hidden');
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
      logoutBtn.classList.remove('hidden');
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

    // Load Data from Firestore
    async function loadMembers() {
      try {
        const snapshot = await db.collection('members').get();
        members = snapshot.docs.map(doc => ({ docId: doc.id, ...doc.data() }));
        renderTable();
        renderChart();
      } catch (e) {
        console.error('Error loading members:', e);
        alert('Error loading members: ' + e.message);
      }
    }

    async function loadArchive() {
      try {
        const snapshot = await db.collection('archive').get();
        archive = snapshot.docs.map(doc => ({ docId: doc.id, ...doc.data() }));
        renderArchive();
      } catch (e) {
        console.error('Error loading archive:', e);
        alert('Error loading archive: ' + e.message);
      }
    }

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
            if (editingIndex < 0) {
              getNextMemberNo().then(no => autoNoField.value = no);
            }
            updateLocalChurchesDropdown();
          }
        };
        presbyList.appendChild(select);
      }
    }

    // Member Form Submit
    memberForm.addEventListener('submit', async function(e) {
      e.preventDefault();
      const idInput = document.getElementById('idInput').value;
      const snapshot = await db.collection('members').where('id', '==', idInput).get();
      if (!snapshot.empty && snapshot.docs[0].id !== members[editingIndex]?.docId) {
        alert('Irangamuntu iri mu bice byabitswe! Hitamo irindi.');
        return;
      }
      const selectedSchool = document.getElementById('schoolSelect').value;
      const selectedLocalChurch = document.getElementById('schoolSelect2').value;
      const selectedEducation = document.getElementById('educationSelect').value;
      const selectedJob = document.getElementById('jobStatusSelect').value;
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
        maritalStatus: document.getElementById('maritalStatusSelect').value,
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
      try {
        if (editingIndex >= 0) {
          memberData.spouse = members[editingIndex].spouse || '';
          memberData.marriageYear = members[editingIndex].marriageYear || '';
          memberData.children = members[editingIndex].children || '0';
          memberData.childNames = members[editingIndex].childNames || [];
          await db.collection('members').doc(members[editingIndex].docId).set(memberData);
          alert('Amakuru yahinduwe!');
          editingIndex = -1;
        } else {
          await db.collection('members').add(memberData);
          alert('Amakuru yabitswe!');
        }
        memberForm.reset();
        getNextMemberNo().then(no => autoNoField.value = no);
        await loadMembers();
      } catch (e) {
        console.error('Error saving member:', e);
        alert('Error saving member: ' + e.message);
      }
    });

    // Family Form Submit
    familyForm.addEventListener('submit', async function(e) {
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
          await db.collection('members').doc(members[familyEditIndex].docId).set(members[familyEditIndex]);
          alert('Amakuru y\'umuryango yabitswe!');
          familyForm.reset();
          updateChildNameInputs(0);
          familyInfoPage.classList.add('hidden');
          dataPage.classList.remove('hidden');
          tableContainer.classList.remove('hidden');
          renderTable();
        } catch (e) {
          console.error('Error saving family data:', e);
          alert('Error saving family data: ' + e.message);
        }
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
            document.getElementById('maritalStatusSelect').value = m.maritalStatus;
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
          delBtn.onclick = async function() {
            if (confirm(`Urashaka gusiba ${m.name}?`)) {
              try {
                await db.collection('members').doc(m.docId).delete();
                await loadMembers();
                renderChart();
              } catch (e) {
                console.error('Error deleting member:', e);
                alert('Error deleting member: ' + e.message);
              }
            }
          };
          tdActions.appendChild(delBtn);
          const archBtn = document.createElement('button');
          archBtn.textContent = 'Bika mu Bubiko';
          archBtn.className = 'action-btn';
          archBtn.setAttribute('aria-label', `Bika ${m.name} mu bubiko`);
          archBtn.onclick = async function() {
            try {
              await db.collection('archive').add(m);
              await db.collection('members').doc(m.docId).delete();
              await loadMembers();
              await loadArchive();
              renderChart();
            } catch (e) {
              console.error('Error archiving member:', e);
              alert('Error archiving member: ' + e.message);
            }
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
    window.renderTable = renderTable;

    // Filter Parish
    function updateFilterParish() {
      filterParish.innerHTML = '<option value="">--Byose--</option>';
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
      const csvHeader = [...Object.keys(members[0]).filter(k => k !== 'childNames' && k !== 'docId'), 'childNames'].join(',') + '\n';
      const csvRows = members.map(m => {
        const values = Object.values(m).map((v, i) => {
          if (Object.keys(m)[i] === 'childNames') {
            return `"${(v || []).join(';')}"`;
          }
          if (Object.keys(m)[i] === 'docId') return '';
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
        resBtn.onclick = async function() {
          try {
            await db.collection('members').add(m);
            await db.collection('archive').doc(m.docId).delete();
            await loadMembers();
            await loadArchive();
            renderChart();
          } catch (e) {
            console.error('Error restoring member:', e);
            alert('Error restoring member: ' + e.message);
          }
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
      transferPresby.innerHTML = '<option value="">--Hitamo--</option>';
      Object.keys(presbyteries).forEach(p => {
        let o = document.createElement('option');
        o.value = p;
        o.textContent = p;
        transferPresby.appendChild(o);
      });
      transferPresby.value = m.presbytery;
      transferPresby.onchange = function() {
        transferParish.innerHTML = '<option value="">--Hitamo--</option>';
        presbyteries[transferPresby.value].forEach(par => {
          let o = document.createElement('option');
          o.value = par;
          o.textContent = par;
          transferParish.appendChild(o);
        });
      };
      transferPresby.onchange();
      transferParish.value = m.parish;
      transferSchool.innerHTML = '<option value="">--Hitamo--</option>';
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

    transferSave.onclick = async function() {
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
          await db.collection('members').doc(members[transferIndex].docId).set(members[transferIndex]);
          alert('Kohereza byagenze neza!');
          renderChart();
          await loadMembers();
        } catch (e) {
          console.error('Error transferring member:', e);
          alert('Error transferring member: ' + e.message);
        }
        transferIndex = -1;
        closeTransferModal();
        renderTable();
      }
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
  </script>
</body>
</html>

