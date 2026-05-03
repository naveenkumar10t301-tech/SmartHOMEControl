<!DOCTYPE html>
<html>
<head>
  <title>Smart Home</title>
  <style>
    body { font-family: Arial; text-align:center; background:#f4f4f4; }
    .card {
      background:white; margin:10px; padding:15px;
      border-radius:10px; display:inline-block; width:200px;
      box-shadow:0 0 10px #ccc;
    }
    .on { color:green; font-weight:bold; }
    .off { color:red; font-weight:bold; }
    button {
      padding:10px; border:none; border-radius:5px;
      cursor:pointer;
    }
  </style>
</head>

<body>

<h2>🏠 Smart Home Control</h2>

<div id="devices"></div>

<script>
const API_KEY = "$2a$10$4x1fBVrV6BNKIOEM9sy6CuGolje9LviSTecr8wmRtXBaWBWD9/xT2";
const BIN_ID = "69f72cc336566621a81d16db";

const url = "https://api.jsonbin.io/v3/b/" + BIN_ID;

// Device names
const names = [
  "Light 1",
  "Light 2",
  "Fan 1",
  "Fan 2",
  "Fan 3",
  "Light 3"
];

// Load data
async function loadData() {
  let res = await fetch(url + "/latest", {
    headers: {"X-Master-Key": API_KEY}
  });

  let json = await res.json();
  let data = json.record;

  let html = "";

  for (let i = 1; i <= 6; i++) {
    let state = data["d"+i];

    html += `
      <div class="card">
        <h3>${names[i-1]}</h3>
        <p class="${state ? 'on':'off'}">
          ${state ? "🟢 ON":"🔴 OFF"}
        </p>
        <button onclick="toggle(${i},${state})">
          Toggle
        </button>
      </div>
    `;
  }

  document.getElementById("devices").innerHTML = html;
}

// Toggle
async function toggle(device, currentState) {

  let res = await fetch(url + "/latest", {
    headers: {"X-Master-Key": API_KEY}
  });

  let json = await res.json();
  let data = json.record;

  data["d"+device] = currentState ? 0 : 1;

  await fetch(url, {
    method: "PUT",
    headers: {
      "Content-Type":"application/json",
      "X-Master-Key": API_KEY
    },
    body: JSON.stringify(data)
  });

  loadData();
}

// Auto refresh
setInterval(loadData,2000);
loadData();

</script>

</body>
</html>
