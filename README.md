<!doctype html>
<html lang="pl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Sprawdź i wyślij swój IP</title>
<style>
body { font-family:system-ui,-apple-system,Segoe UI,Roboto,sans-serif;
display:flex; align-items:center; justify-content:center; height:100vh; margin:0;
background:#f5f7fb; color:#111; }
.card { padding:1.6rem; border-radius:10px; box-shadow:0 8px 30px rgba(0,0,0,0.08);
background:white; text-align:center; width:320px; }
.ip { font-weight:700; margin:0.8rem 0; font-size:1.15rem; }
button { padding:.5rem 1rem; border-radius:8px; border:0; cursor:pointer; }
#status { font-size:.85rem; margin-top:.6rem; color: #555; }
</style>
</head>
<body>
<div class="card">
<h2>Twój publiczny adres IP</h2>
<div id="ip" class="ip">Ładuję…</div>
<button id="send" disabled>Wyślij mi (dobrowolne)</button>
<div id="status"></div>
<p style="font-size:.8rem; color:#666; margin-top:0.6rem;">
Klikając „Wyślij mi” wyrażasz zgodę na przesłanie twojego IP do właściciela strony.
</p>
</div>

<script>
const ipEl = document.getElementById('ip');
const sendBtn = document.getElementById('send');
const status = document.getElementById('status');
let currentIP = null;

async function fetchIP() {
try {
const res = await fetch('https://api.ipify.org?format=json');
if (!res.ok) throw new Error('Błąd pobierania');
const data = await res.json();
currentIP = data.ip;
ipEl.textContent = currentIP;
sendBtn.disabled = false;
} catch (e) {
ipEl.textContent = 'Nie udało się pobrać IP';
console.error(e);
}
}

sendBtn.addEventListener('click', async () => {
if (!currentIP) return;
// wyraźna zgoda: potwierdźmy jeszcze raz
const ok = confirm('Czy na pewno chcesz wysłać swój publiczny IP właścicielowi tej strony?');
if (!ok) return;
status.textContent = 'Wysyłam…';
try {
const res = await fetch('/report', {
method: 'POST',
headers: { 'Content-Type': 'application/json' },
body: JSON.stringify({ ip: currentIP, note: 'dobrowolne wysłanie przez odwiedzającego' })
});
if (!res.ok) throw new Error('Błąd serwera');
status.textContent = 'Wysłano — dzięki!';
} catch (e) {
status.textContent = 'Błąd wysyłki';
console.error(e);
}
});

fetchIP();
</script>
</body>
</html>

