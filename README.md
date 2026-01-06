# Imhoff-card
Interactive map for Imhoff Caravan 
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<title>Imhoff Caravan Park Map</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>

<style>
body { margin:0; font-family:Arial,sans-serif; }
#map { height:100vh; }

.lang-btn {
  position:absolute; top:10px; right:10px;
  z-index:999;
  background:#fff; padding:8px 12px;
  border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,.2);
  cursor:pointer;
}
.popup-link {
  display:inline-block;
  margin-top:8px;
  padding:6px 10px;
  background:#007aff;
  color:#fff;
  text-decoration:none;
  border-radius:6px;
}
</style>
</head>

<body>

<div class="lang-btn" onclick="toggleLang()">🌍 DE / EN</div>
<div id="map"></div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('sw.js');
}

let lang = 'de';

const text = {
  de: { route: "Route starten" },
  en: { route: "Start navigation" }
};

function toggleLang() {
  lang = lang === 'de' ? 'en' : 'de';
  loadMarkers();
}

const imhoff = [-34.1449, 18.3567];
const map = L.map('map').setView(imhoff, 10);

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{
  attribution:'© OpenStreetMap'
}).addTo(map);

L.circle(imhoff,{radius:50000,color:'#007aff',fillOpacity:0.06}).addTo(map);

const icon = L.icon({
  iconUrl:'https://cdn-icons-png.flaticon.com/512/684/684908.png',
  iconSize:[38,38],
  iconAnchor:[19,38]
});

const places = [
  {name:"Noordhoek", lat:-34.1076, lon:18.3553},
  {name:"Hout Bay", lat:-34.0433, lon:18.3567},
  {name:"Boulders Beach", lat:-34.1976, lon:18.4517},
  {name:"Cape Town", lat:-33.9249, lon:18.4241}
];

let markers = [];

function getNavLink(lat, lon) {
  const ua = navigator.userAgent.toLowerCase();
  if (/iphone|ipad|mac/.test(ua)) {
    return `https://maps.apple.com/?daddr=${lat},${lon}`;
  }
  return `https://www.google.com/maps/dir/?api=1&destination=${lat},${lon}`;
}

function loadMarkers() {
  markers.forEach(m => map.removeLayer(m));
  markers = [];

  places.forEach(p => {
    const link = getNavLink(p.lat,p.lon);
    const m = L.marker([p.lat,p.lon],{icon})
      .addTo(map)
      .bindPopup(`
        <strong>${p.name}</strong><br>
        <a class="popup-link" href="${link}" target="_blank">
          ${text[lang].route}
        </a>
      `);
    markers.push(m);
  });
}

loadMarkers();
</script>

</body>

</html>
