<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pontos de Recarga Elétrica — Distrito Federal</title>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    font-family: 'Inter', -apple-system, sans-serif;
    background: #f5f7f2;
    color: #1a2a1a;
    min-height: 100vh;
  }

  header {
    background: #fff;
    border-bottom: 1px solid #e0e8d8;
    padding: 1.1rem 1.8rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 0.75rem;
    box-shadow: 0 1px 4px rgba(0,0,0,0.06);
  }

  .header-left {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .logo-bolt {
    width: 36px; height: 36px;
    background: #2d7a1a;
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 18px;
    flex-shrink: 0;
  }

  .header-left h1 {
    font-size: 1rem;
    font-weight: 700;
    color: #1a2a1a;
    line-height: 1.2;
  }

  .header-left p {
    font-size: 0.72rem;
    color: #6a8a6a;
    margin-top: 1px;
  }

  .stats-row {
    display: flex;
    gap: 1.2rem;
    flex-wrap: wrap;
  }

  .stat {
    text-align: center;
    background: #f0f7e8;
    border: 1px solid #d0e8c0;
    border-radius: 8px;
    padding: 6px 14px;
  }

  .stat-num {
    font-size: 1.1rem;
    font-weight: 700;
    color: #2d7a1a;
    line-height: 1;
  }

  .stat-label {
    font-size: 0.65rem;
    color: #6a8a6a;
    margin-top: 1px;
    line-height: 1.3;
  }

  .filters {
    padding: 0.6rem 1.8rem;
    display: flex;
    gap: 0.45rem;
    flex-wrap: wrap;
    background: #fff;
    border-bottom: 1px solid #e0e8d8;
    align-items: center;
  }

  .filters-label {
    font-size: 0.72rem;
    font-weight: 600;
    color: #8aaa8a;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    margin-right: 4px;
  }

  .filter-btn {
    padding: 5px 13px;
    border-radius: 20px;
    border: 1.5px solid #d0e0c8;
    background: #fff;
    color: #4a6a4a;
    font-size: 0.75rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.18s;
    font-family: 'Inter', sans-serif;
  }

  .filter-btn:hover { border-color: #5aaa3a; color: #2d7a1a; background: #f5fbf0; }
  .filter-btn.active { background: #2d7a1a; border-color: #2d7a1a; color: #fff; }
  .filter-btn.active-blue  { background: #1a5aa0; border-color: #1a5aa0; color: #fff; }
  .filter-btn.active-amber { background: #a07010; border-color: #a07010; color: #fff; }
  .filter-btn.active-teal  { background: #0a7a5a; border-color: #0a7a5a; color: #fff; }

  #map {
    height: calc(100vh - 120px);
    width: 100%;
  }

  /* Leaflet popup styling */
  .leaflet-popup-content-wrapper {
    background: #fff;
    border: 1px solid #d8e8cc;
    border-radius: 12px;
    box-shadow: 0 6px 24px rgba(0,0,0,0.12);
    color: #1a2a1a;
    padding: 0;
  }

  .leaflet-popup-tip { background: #fff; }
  .leaflet-popup-content { margin: 0; min-width: 220px; }

  .popup-inner { padding: 14px 16px; }

  .popup-badge {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    padding: 2px 8px;
    border-radius: 10px;
    display: inline-block;
    margin-bottom: 7px;
  }

  .badge-rapido { background: #ddeeff; color: #1a5aa0; }
  .badge-lento  { background: #fff3cc; color: #8a6010; }

  .popup-nome {
    font-size: 13.5px;
    font-weight: 700;
    color: #1a2a1a;
    margin-bottom: 3px;
    line-height: 1.3;
  }

  .popup-end {
    font-size: 11.5px;
    color: #6a8a6a;
    line-height: 1.5;
    margin-bottom: 10px;
  }

  .popup-footer {
    border-top: 1px solid #eef4e8;
    padding: 8px 16px;
    display: flex;
    gap: 5px;
    flex-wrap: wrap;
    background: #fafcf8;
    border-radius: 0 0 12px 12px;
  }

  .tag {
    font-size: 10px;
    padding: 2px 8px;
    border-radius: 10px;
    font-weight: 500;
  }

  .tag-rapido { background: #ddeeff; color: #1a5aa0; }
  .tag-lento  { background: #fff3cc; color: #8a6010; }
  .tag-gratis { background: #ddffee; color: #0a7a4a; }
  .tag-pago   { background: #fff0e8; color: #a04820; }
  .tag-24h    { background: #f0eeff; color: #5a40b0; }
  .tag-gama   { background: #e8f5e0; color: #2d7a1a; border: 1px solid #b0d890; }

  .leaflet-control-zoom a {
    background: #fff !important;
    color: #2d7a1a !important;
    border-color: #d0e8c0 !important;
    font-weight: 600 !important;
  }

  .leaflet-control-zoom a:hover { background: #f0f8e8 !important; }

  .leaflet-control-attribution {
    background: rgba(255,255,255,0.85) !important;
    color: #9aaa9a !important;
    font-size: 9px;
  }

  .leaflet-control-attribution a { color: #5a8a5a !important; }

  .legend {
    position: absolute;
    bottom: 1.5rem;
    right: 1rem;
    z-index: 1000;
    background: rgba(255,255,255,0.96);
    border: 1px solid #d8e8cc;
    border-radius: 12px;
    padding: 12px 16px;
    font-size: 11.5px;
    color: #4a6a4a;
    box-shadow: 0 4px 16px rgba(0,0,0,0.08);
  }

  .legend-title {
    font-size: 10px;
    font-weight: 700;
    color: #8aaa8a;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 9px;
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 6px;
    font-size: 11.5px;
    color: #3a5a3a;
  }

  .legend-dot {
    width: 13px; height: 13px;
    border-radius: 50%;
    flex-shrink: 0;
    border: 2px solid rgba(0,0,0,0.08);
  }

  .gama-badge {
    display: inline-block;
    background: #e8f5e0;
    border: 1.5px dashed #5aaa3a;
    border-radius: 6px;
    padding: 2px 8px;
    font-size: 10px;
    font-weight: 600;
    color: #2d7a1a;
    margin-top: 8px;
    width: 100%;
    text-align: center;
  }

  .leaflet-tooltip {
    background: #fff;
    border: 1px solid #d0e8c0;
    border-radius: 6px;
    color: #2d7a1a;
    font-family: 'Inter', sans-serif;
    font-size: 11px;
    font-weight: 600;
    padding: 4px 10px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  }
</style>
</head>
<body>

<header>
  <div class="header-left">
    <div class="logo-bolt">⚡</div>
    <div>
      <h1>Pontos de Recarga Elétrica — Distrito Federal</h1>
      <p>Eletropostos públicos e privados · Dados: ABVE, GDF, YellotMob, PlugShare 2026</p>
    </div>
  </div>
  <div class="stats-row">
    <div class="stat">
      <div class="stat-num">360+</div>
      <div class="stat-label">eletropostos<br>no DF</div>
    </div>
    <div class="stat">
      <div class="stat-num">2º</div>
      <div class="stat-label">em vendas de<br>elétricos no Brasil</div>
    </div>
    <div class="stat">
      <div class="stat-num">R$ 42</div>
      <div class="stat-label">recarga completa<br>em casa</div>
    </div>
  </div>
</header>

<div class="filters">
  <span class="filters-label">Filtrar:</span>
  <button class="filter-btn active"       onclick="filtrar('todos',  this)">🔌 Todos</button>
  <button class="filter-btn"              onclick="filtrar('rapido', this)">⚡ Recarga Rápida DC</button>
  <button class="filter-btn"              onclick="filtrar('lento',  this)">🔋 Recarga Lenta AC</button>
  <button class="filter-btn"             onclick="filtrar('gratis', this)">✅ Gratuito</button>

</div>

<div id="map"></div>

<div class="legend">
  <div class="legend-title">Tipo de ponto</div>
  <div class="legend-item"><div class="legend-dot" style="background:#1a6abf"></div> Recarga Rápida (DC)</div>
  <div class="legend-item"><div class="legend-dot" style="background:#c8900a"></div> Recarga Lenta (AC)</div>
  <div class="legend-item"><div class="legend-dot" style="background:#2d9a3a"></div> Gratuito público</div>
  <div class="legend-item"><div class="legend-dot" style="background:#c03a2a"></div> Concessionária</div>

</div>

<script>
const map = L.map('map', {
  center: [-15.84, -47.97],
  zoom: 11,
  zoomControl: true
});

L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
  attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> &copy; <a href="https://carto.com/">CARTO</a>',
  subdomains: 'abcd',
  maxZoom: 19
}).addTo(map);

const pontos = [
  // ── GAMA (destaque) ─────────────────────────────────────────
  { lat:-16.0215, lng:-48.0612, nome:'Super Adega — Gama', end:'Setor Leste, Área Industrial Lt 1 Parte 1 — Gama-DF, 72460-400', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'YellotMob', gama:true },
  { lat:-16.0078, lng:-48.0501, nome:'Shopping Gama', end:'Setor Central — Gama-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Zletric', gama:true },
  { lat:-16.0130, lng:-48.0560, nome:'Adm. Regional do Gama', end:'Setor Central — Gama-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:true },

  // ── SHOPPINGS ────────────────────────────────────────────────
  { lat:-15.7268, lng:-47.8958, nome:'Shopping Iguatemi Brasília', end:'CA 4 – Lago Norte, Brasília-DF, 71503-504', tipo:'rapido', gratuito:false, h24:true,  conectores:'CCS / CHAdeMO / Tipo 2', operador:'EDP · Porsche', gama:false },
  { lat:-15.8395, lng:-47.9206, nome:'Shopping Parkshopping', end:'SMAS Trecho 1 – Guará, Brasília-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / CHAdeMO / Tipo 2', operador:'Vibra · Zletric', gama:false },
  { lat:-15.7965, lng:-47.8836, nome:'Shopping Conjunto Nacional', end:'SDS, Asa Norte – Brasília-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Zletric', gama:false },
  { lat:-15.8313, lng:-48.0498, nome:'Shopping Taguatinga', end:'Pistão Sul, Taguatinga-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Zletric', gama:false },
  { lat:-15.8781, lng:-48.0575, nome:'Shopping Samambaia', end:'Av. Comercial — Samambaia-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Zletric', gama:false },
  { lat:-15.8397, lng:-47.9764, nome:'Shopping Águas Claras', end:'Av. das Araucárias – Águas Claras-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / Tipo 2', operador:'Vibra', gama:false },

  // ── AEROPORTO ────────────────────────────────────────────────
  { lat:-15.8697, lng:-47.9208, nome:'Aeroporto Internacional de Brasília', end:'Lago Sul – Brasília-DF, 71608-900', tipo:'rapido', gratuito:false, h24:true, conectores:'CCS / CHAdeMO / Tipo 2', operador:'Vibra · EDP', gama:false },

  // ── CONCESSIONÁRIAS ──────────────────────────────────────────
  { lat:-15.8710, lng:-47.9190, nome:'GWM Jorlan-ev — Aeroporto', end:'Setor de Concessionárias – Lago Sul-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / Tipo 2', operador:'GWM', gama:false },
  { lat:-15.8340, lng:-47.9700, nome:'GWM Jorlan-ev — Asa Norte', end:'SEPN 505 Lote 01 – Asa Norte-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / Tipo 2', operador:'GWM', gama:false },
  { lat:-15.8180, lng:-48.0320, nome:'GWM Jorlan-ev — SIA', end:'SIA Trecho 2, Lote 320/340 – Brasília-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / Tipo 2', operador:'GWM', gama:false },
  { lat:-15.8710, lng:-47.9210, nome:'Geely Jorlan-ev — Aeroporto', end:'SHIS – Setor de Concessionárias Aeroporto', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Geely', gama:false },
  { lat:-15.8690, lng:-47.9215, nome:'GAC Jorlan Motors — Aeroporto', end:'Setor de Concessionárias UC4.010A – Lago Sul', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / Tipo 2', operador:'GAC', gama:false },

  // ── POSTOS PÚBLICOS GDF (gratuitos) ──────────────────────────
  { lat:-15.7870, lng:-47.9020, nome:'Adm. Regional do Plano Piloto', end:'SBN Quadra 2 Bloco K – Brasília-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8390, lng:-48.0570, nome:'Adm. Regional de Samambaia', end:'Centro Urbano – Samambaia-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8295, lng:-48.0625, nome:'Adm. Regional de Taguatinga', end:'QI 27 – Taguatinga-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.9010, lng:-48.0650, nome:'Adm. Regional do Recanto das Emas', end:'Recanto das Emas – Brasília-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8808, lng:-48.0327, nome:'Adm. Regional do Riacho Fundo', end:'AC 3 CLN 7 Bloco A – Riacho Fundo-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8080, lng:-48.0700, nome:'Adm. Regional de Ceilândia', end:'QNM 13, Módulo B – Ceilândia-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8480, lng:-48.0120, nome:'Adm. Regional do Sudoeste', end:'SIG Quadra 6 – Sudoeste-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.6290, lng:-47.7720, nome:'Adm. Regional de Planaltina', end:'Av. Uberdan Cardoso – Planaltina-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.7420, lng:-47.6770, nome:'Adm. Regional do Paranoá', end:'Praça Central Lt. 1 – Paranoá-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.6528, lng:-48.0219, nome:'Adm. Regional de Sobradinho', end:'Quadra Central Lote A – Sobradinho-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.6908, lng:-48.1381, nome:'Adm. Regional de Brazlândia', end:'Setor Tradicional Q. 16 – Brazlândia-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },
  { lat:-15.8397, lng:-47.9764, nome:'Adm. Regional de Águas Claras', end:'Rua Manacá – Águas Claras-DF', tipo:'lento', gratuito:true, h24:false, conectores:'Tipo 2 (AC)', operador:'GDF / VEM-DF', gama:false },

  // ── POSTOS / REDE ─────────────────────────────────────────────
  { lat:-15.8220, lng:-47.9180, nome:'Shell Select — Eixão Sul (24h)', end:'Eixo Rodoviário Sul, altura SQS 108/109 – Asa Sul', tipo:'rapido', gratuito:false, h24:true, conectores:'CCS (DC) / Tipo 2', operador:'Cascol / Vibra', gama:false },
  { lat:-15.7850, lng:-47.8920, nome:'Vibra Energy — Asa Norte', end:'Eixo Rodoviário Norte – Asa Norte-DF', tipo:'rapido', gratuito:false, h24:true, conectores:'CCS / CHAdeMO', operador:'Vibra', gama:false },
  { lat:-15.8135, lng:-47.9800, nome:'godrive — SIA Guará', end:'SIA/SUL Trecho 1, Lotes 1160–1210 – Guará-DF', tipo:'rapido', gratuito:false, h24:false, conectores:'CCS / CHAdeMO / Tipo 2', operador:'godrive / BYD', gama:false },
  { lat:-15.8140, lng:-47.9960, nome:'Núcleo Bandeirante — DF-003', end:'SPMS S/N DF-003 – Núcleo Bandeirante-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'godrive', gama:false },

  // ── PARK WAY / LAGO SUL ───────────────────────────────────────
  { lat:-15.9200, lng:-47.9650, nome:'Posto Vibra — Park Way', end:'SMPW Quadra 23 – Park Way-DF', tipo:'rapido', gratuito:false, h24:true, conectores:'CCS / Tipo 2', operador:'Vibra', gama:false },
  { lat:-15.8610, lng:-47.9070, nome:'Clube do Congresso — Lago Sul', end:'SHIS QI 19 – Lago Sul-DF', tipo:'lento', gratuito:false, h24:false, conectores:'Tipo 2 (AC)', operador:'Privado', gama:false },
];

function corPonto(p) {
  if (p.gama)    return '#2d7a1a';
  if (p.gratuito) return '#2d9a3a';
  if (p.tipo === 'rapido') return '#1a6abf';
  if (p.operador.match(/GWM|Geely|GAC/)) return '#c03a2a';
  return '#c8900a';
}

function svgMarker(cor, rapido, gama) {
  const r = gama ? 10 : rapido ? 8 : 7;
  const stroke = gama ? '#fff' : '#fff';
  const sw = gama ? 2.5 : 1.5;
  const h = (r*2)+14;
  const cx = r+4;
  const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="${cx*2}" height="${h}" viewBox="0 0 ${cx*2} ${h}">
    <circle cx="${cx}" cy="${r+2}" r="${r}" fill="${cor}" stroke="${stroke}" stroke-width="${sw}"/>
    <text x="${cx}" y="${r+7}" text-anchor="middle" font-size="${gama?10:rapido?9:8}" fill="#fff" font-family="Arial" font-weight="bold">${gama?'★':rapido?'⚡':'●'}</text>
    <line x1="${cx}" y1="${r*2+2}" x2="${cx}" y2="${h-1}" stroke="${cor}" stroke-width="2.5" stroke-linecap="round"/>
    <circle cx="${cx}" cy="${h-1}" r="2" fill="${cor}"/>
  </svg>`;
  return L.divIcon({
    html: svg,
    className: '',
    iconSize: [cx*2, h],
    iconAnchor: [cx, h-1],
    popupAnchor: [0, -(h-1)]
  });
}

let marcadores = [];

pontos.forEach(p => {
  const cor = corPonto(p);
  const marker = L.marker([p.lat, p.lng], {
    icon: svgMarker(cor, p.tipo==='rapido', p.gama),
    zIndexOffset: p.gama ? 1000 : 0
  });

  const badgeClass = p.tipo === 'rapido' ? 'badge-rapido' : 'badge-lento';
  const badgeText  = p.tipo === 'rapido' ? '⚡ Recarga Rápida DC' : '🔋 Recarga Lenta AC';

  const tags = [
    p.tipo === 'rapido' ? '<span class="tag tag-rapido">⚡ Rápida DC</span>' : '<span class="tag tag-lento">🔋 Lenta AC</span>',
    p.gratuito ? '<span class="tag tag-gratis">✅ Gratuito</span>' : '<span class="tag tag-pago">💳 Pago</span>',
    p.h24 ? '<span class="tag tag-24h">🕐 24h</span>' : '',
    ''
  ].join('');

  marker.bindPopup(`
    <div class="popup-inner">
      <span class="popup-badge ${badgeClass}">${badgeText}</span>
      <div class="popup-nome">${p.nome}</div>
      <div class="popup-end">${p.end}<br><span style="color:#8aaa6a;font-size:10px;margin-top:3px;display:block">${p.conectores} · ${p.operador}</span></div>
    </div>
    <div class="popup-footer">${tags}</div>
  `, { maxWidth: 270 });

  marker._tipo    = p.tipo;
  marker._gratuito = p.gratuito;
  marker._gama    = p.gama;
  marker.addTo(map);
  marcadores.push(marker);
});



let activeFilter = 'todos';

function filtrar(tipo, btn) {
  activeFilter = tipo;
  document.querySelectorAll('.filter-btn').forEach(b => {
    b.classList.remove('active','active-blue','active-amber','active-teal');
  });
  const cls = tipo==='rapido'?'active-blue': tipo==='lento'?'active-amber': tipo==='gratis'?'active-teal': tipo==='gama'?'active':'active';
  btn.classList.add(cls);

  marcadores.forEach(m => {
    let show = false;
    if (tipo==='todos')  show = true;
    if (tipo==='rapido') show = m._tipo==='rapido';
    if (tipo==='lento')  show = m._tipo==='lento';
    if (tipo==='gratis') show = m._gratuito;
    show ? (!map.hasLayer(m) && m.addTo(map)) : (map.hasLayer(m) && map.removeLayer(m));
  });

  if (tipo==='todos') map.flyTo([-15.84, -47.97], 11, {duration:1});
}
</script>
</body>
</html>
 
    iconSize: [cx*2, h],
    iconAnchor: [cx, h-1],
