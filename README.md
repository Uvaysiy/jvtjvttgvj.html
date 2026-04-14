<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Butun dunyo tarixi | Interaktiv ensiklopediya</title>
    <!-- Google Fonts & Font Awesome -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,600;14..32,700;14..32,800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <!-- Leaflet (xarita uchun) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: #fef8f0;
            color: #2c2418;
            line-height: 1.5;
            transition: background 0.3s, color 0.3s;
        }

        /* DARK MODE */
        body.dark {
            background: #1a1e2a;
            color: #e8e2d4;
        }
        body.dark .history-card {
            background: #2a2f3f;
            border-color: #4a3f2a;
            color: #f0e6d8;
        }
        body.dark .card-body p { color: #ddd2c0; }
        body.dark .fact-highlight { background: #3a3240; color: #f3deba; border-left-color: #e6bc7e; }
        body.dark .era-nav { background: #0f1119; border-bottom-color: #4a3f2a; }
        body.dark .nav-links a { color: #e6d5b8; border-color: #4a3f2a; }
        body.dark .stats { background: #0b0e16; }
        body.dark .timeline-horizontal { background: #2a241c; }
        body.dark .step { background: #1e212e; color: #f3e9d2; }
        body.dark footer { background: #080b10; }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 0 2rem;
        }

        /* HEADER */
        .hero {
            background: linear-gradient(135deg, #1e2a3a 0%, #0f1a24 100%);
            color: #f3e9d2;
            padding: 3rem 0 4rem 0;
            text-align: center;
            border-bottom: 8px solid #c9a03d;
            position: relative;
        }
        .hero h1 { font-size: 3rem; margin-bottom: 0.5rem; }
        .hero .sub { font-size: 1.1rem; max-width: 700px; margin: 0 auto; background: rgba(0,0,0,0.3); display: inline-block; padding: 0.4rem 1.2rem; border-radius: 40px; }

        /* top bar (qidiruv, til, dark mode, audio) */
        .top-bar {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 1rem;
            background: #fffaf2;
            padding: 0.8rem 2rem;
            border-bottom: 1px solid #e6d5b8;
        }
        body.dark .top-bar { background: #131724; border-bottom-color: #4a3f2a; }
        .search-box input {
            padding: 0.5rem 1rem;
            border-radius: 40px;
            border: 1px solid #dccfaf;
            width: 260px;
            font-size: 0.9rem;
            background: white;
        }
        body.dark .search-box input { background: #2a2f3f; color: #f0e6d8; border-color: #6a5a3a; }
        .controls button {
            background: #e6d5b8;
            border: none;
            padding: 0.5rem 1rem;
            margin: 0 0.2rem;
            border-radius: 30px;
            cursor: pointer;
            font-weight: 600;
            transition: 0.2s;
        }
        body.dark .controls button { background: #3a3f55; color: #f0e6d8; }
        .controls button:hover { background: #c9a03d; color: white; transform: scale(0.97); }

        .era-nav {
            background: #fff6ea;
            position: sticky;
            top: 0;
            z-index: 99;
            padding: 0.6rem 0;
            overflow-x: auto;
            white-space: nowrap;
            border-bottom: 1px solid #e6d5b8;
        }
        .nav-links { display: inline-flex; gap: 0.5rem; padding: 0 1rem; }
        .nav-links a {
            background: transparent;
            color: #4a3722;
            text-decoration: none;
            font-weight: 600;
            padding: 0.4rem 1.2rem;
            border-radius: 40px;
            border: 1px solid #e2cfaa;
            font-size: 0.85rem;
        }
        body.dark .nav-links a { color: #e6d5b8; border-color: #5e513a; }
        .nav-links a:hover { background: #c9a03d; color: white; }

        .section-title {
            font-size: 2rem;
            margin: 2rem 0 1rem 0;
            border-left: 6px solid #c9a03d;
            padding-left: 1rem;
        }
        .timeline-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(330px, 1fr));
            gap: 1.8rem;
            margin: 1.5rem 0;
        }
        .history-card {
            background: white;
            border-radius: 28px;
            box-shadow: 0 10px 20px -10px rgba(0,0,0,0.1);
            transition: 0.2s;
            border: 1px solid #f0e2cc;
            display: flex;
            flex-direction: column;
        }
        .history-card:hover { transform: translateY(-5px); }
        .card-header {
            background: #2c3e2f;
            color: #fbf5e8;
            padding: 0.9rem 1.3rem;
            font-weight: 700;
            font-size: 1.3rem;
            display: flex;
            align-items: center;
            gap: 10px;
            border-radius: 28px 28px 0 0;
        }
        .card-body { padding: 1.3rem; flex: 1; }
        .date-range { font-family: monospace; background: #f6efdf; display: inline-block; padding: 0.2rem 0.8rem; border-radius: 20px; font-size: 0.75rem; font-weight: 600; margin-bottom: 0.7rem; }
        .fact-highlight { background: #fef4e2; border-left: 4px solid #c9a03d; padding: 0.6rem; border-radius: 12px; margin: 0.8rem 0; font-size: 0.85rem; }
        .wiki-link { display: inline-block; margin-top: 0.8rem; color: #c9a03d; text-decoration: none; font-weight: 600; font-size: 0.8rem; }
        
        /* galereya */
        .gallery-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
            gap: 1.2rem;
            margin: 1.5rem 0;
        }
        .person-card {
            background: #fef7ed;
            border-radius: 24px;
            text-align: center;
            padding: 1rem;
            transition: 0.2s;
        }
        body.dark .person-card { background: #2a2f3f; }
        .person-card img {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            object-fit: cover;
            background: #d6c29d;
        }
        .person-name { font-weight: 800; margin-top: 0.5rem; }
        .person-desc { font-size: 0.7rem; }

        /* xarita */
        #historyMap {
            height: 400px;
            border-radius: 28px;
            margin: 1rem 0 2rem;
            border: 2px solid #c9a03d;
        }

        /* slayder (vaqt chizig‘i) */
        .timeline-slider {
            background: #e9ddce;
            border-radius: 40px;
            padding: 1.5rem;
            margin: 2rem 0;
            text-align: center;
        }
        body.dark .timeline-slider { background: #2a241c; }
        input#yearSlider {
            width: 100%;
            margin: 1rem 0;
        }
        #sliderYearLabel {
            font-weight: 800;
            font-size: 1.8rem;
            background: #c9a03d;
            display: inline-block;
            padding: 0.2rem 1.2rem;
            border-radius: 40px;
            color: #1e2a3a;
        }
        .stats {
            background: #1f2a2e;
            color: #ffefcf;
            padding: 1.5rem;
            border-radius: 42px;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
            margin: 2rem 0;
        }
        .stat-number { font-size: 2rem; font-weight: 800; color: #e9bc6d; }
        footer { background: #0f1a1f; text-align: center; padding: 1.8rem; margin-top: 2rem; color: #cfc5ae; }
        button.scroll-top {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #c9a03d;
            border: none;
            width: 46px;
            height: 46px;
            border-radius: 30px;
            cursor: pointer;
            display: none;
        }
        button.scroll-top.show { display: block; }
        @media (max-width: 750px) {
            .container { padding: 0 1rem; }
            .top-bar { flex-direction: column; align-items: stretch; }
            .search-box input { width: 100%; }
        }
    </style>
</head>
<body>

<div class="top-bar">
    <div class="search-box">
        <input type="text" id="searchInput" placeholder="🔍 Qidiruv: Misr, Rim, Inqilob...">
    </div>
    <div class="controls">
        <button id="langToggleBtn"><i class="fas fa-language"></i> English</button>
        <button id="darkModeBtn"><i class="fas fa-moon"></i> Dark mode</button>
        <button id="audioBtn"><i class="fas fa-music"></i> Musiqa</button>
    </div>
</div>

<header class="hero">
    <div class="container">
        <h1>🌍 Butun dunyo tarixi</h1>
        <div class="sub">Insoniyatning to‘liq xronikasi | Qidiruv, xarita, shaxslar va interaktiv lenta</div>
    </div>
</header>

<div class="era-nav">
    <div class="nav-links" id="navLinks">
        <a href="#antiquity">🏛️ Qadimgi dunyo</a>
        <a href="#medieval">🏰 O‘rta asrlar</a>
        <a href="#modern">🚢 Yangi davr</a>
        <a href="#contemporary">💻 Zamonaviy</a>
        <a href="#gallery">👤 Buyuk shaxslar</a>
        <a href="#mapSection">🗺️ Xarita</a>
    </div>
</div>

<main class="container">
    <!-- QIDIRUV NATIJALARI XABARI -->
    <div id="searchStatus" style="margin-top: 1rem; font-style: italic;"></div>

    <!-- QADIMGI DUNYO -->
    <h2 id="antiquity" class="section-title">📜 Qadimgi dunyo</h2>
    <div class="timeline-grid" id="antiquityGrid"></div>

    <h2 id="medieval" class="section-title">⚔️ O‘rta asrlar</h2>
    <div class="timeline-grid" id="medievalGrid"></div>

    <h2 id="modern" class="section-title">🧭 Yangi davr</h2>
    <div class="timeline-grid" id="modernGrid"></div>

    <h2 id="contemporary" class="section-title">🤖 Zamonaviy tarix</h2>
    <div class="timeline-grid" id="contemporaryGrid"></div>

    <!-- TARIXIY SHAXSLAR GALEREYASI -->
    <h2 id="gallery" class="section-title"><i class="fas fa-user-graduate"></i> Tarixiy shaxslar galereyasi</h2>
    <div class="gallery-grid" id="personsGallery"></div>

    <!-- XARITA -->
    <h2 id="mapSection" class="section-title"><i class="fas fa-map-marked-alt"></i> Tarixiy xarita: sivilizatsiyalar markazlari</h2>
    <div id="historyMap"></div>

    <!-- INTERAKTIV VAQT CHIZIG‘I (SLAYDER) -->
    <div class="timeline-slider">
        <h3>📅 Asrlar bo‘ylab sayohat</h3>
        <div id="sliderYearLabel">3500 BCE</div>
        <input type="range" id="yearSlider" min="-3500" max="2025" step="50" value="-500">
        <p id="sliderEventText">Miloddan avvalgi 500 yillar: Axomaniylar imperiyasi, Buddizmning yoyilishi</p>
    </div>

    <div class="stats">
        <div class="stat-item"><div class="stat-number">45+</div><div>Sivilizatsiyalar</div></div>
        <div class="stat-item"><div class="stat-number">200+</div><div>Voqealar</div></div>
        <div class="stat-item"><div class="stat-number">12</div><div>Buyuk shaxslar</div></div>
        <div class="stat-item"><div class="stat-number">5500+</div><div>Yillik tarix</div></div>
    </div>
</main>
<footer>
    <p><i class="fas fa-globe"></i> Butun dunyo tarixi — umuminsoniy meros | Vikipediya havolalari | Interaktiv xarita</p>
    <p>© 2025 | Qidiruv, til (O'zbek/English), Dark mode, Musiqa bilan to‘liq tajriba</p>
</footer>
<button class="scroll-top" id="scrollTopBtn"><i class="fas fa-arrow-up"></i></button>

<script>
    // ===================== MA'LUMOTLAR =====================
    const historyData = {
        antiquity: [
            { name: "Shumerlar", date: "≈3500 BCE", desc: "Birinchi yozuv, g‘ildirak, zigguratlar.", fact: "📜 Gilam yozuvi — eng qadimgi adabiyot.", wiki: "https://uz.wikipedia.org/wiki/Shumer", lat: 31.5, lon: 45.5 },
            { name: "Qadimgi Misr", date: "3100–30 BCE", desc: "Piramidalar, fir'avnlar, ierogliflar.", fact: "🏺 Xeops piramidasi 4500 yillik.", wiki: "https://uz.wikipedia.org/wiki/Qadimgi_Misr", lat: 29.9792, lon: 31.1342 },
            { name: "Indus vodiysi", date: "2600–1900 BCE", desc: "Mohenjo-Daro, kanalizatsiya tizimi.", fact: "🏛️ Ilk shaharsozlik.", wiki: "https://uz.wikipedia.org/wiki/Indus_vodiysi_sivilizatsiyasi", lat: 27.329, lon: 68.138 },
            { name: "Qadimgi Xitoy", date: "2070–220 BCE", desc: "Shang sulolasi, Ipak yo‘li.", fact: "🐉 Terrakota armiyasi.", wiki: "https://uz.wikipedia.org/wiki/Qadimgi_Xitoy", lat: 34.5, lon: 108.5 },
            { name: "Yunoniston", date: "800–146 BCE", desc: "Demokratiya, falsafa, Olimpiada.", fact: "🏺 Parthenon, Aristotel.", wiki: "https://uz.wikipedia.org/wiki/Qadimgi_Yunoniston", lat: 37.9838, lon: 23.7275 },
            { name: "Rim imperiyasi", date: "753 BCE–476 CE", desc: "Rim huquqi, akveduklar.", fact: "🏛️ SPQR, Kolizey.", wiki: "https://uz.wikipedia.org/wiki/Qadimgi_Rim", lat: 41.9028, lon: 12.4964 }
        ],
        medieval: [
            { name: "Vizantiya", date: "330–1453", desc: "Konstantinopol, Yustiniana kodeksi.", fact: "⛪ Ayasofya", wiki: "https://uz.wikipedia.org/wiki/Vizantiya_imperiyasi", lat: 41.0082, lon: 28.9784 },
            { name: "Islom Oltin asri", date: "750–1258", desc: "Algebra, Ibn Sino, Al-Xorazmiy.", fact: "📚 Raqamlar va nol", wiki: "https://uz.wikipedia.org/wiki/Islomning_oltin_davri", lat: 33.3152, lon: 44.3661 },
            { name: "Mo‘g‘ullar", date: "1206–1368", desc: "Chingizxon, eng katta imperiya.", fact: "🏇 Pax Mongolica", wiki: "https://uz.wikipedia.org/wiki/Mo‘g‘ullar_imperiyasi", lat: 47.8864, lon: 106.9057 },
            { name: "Yevropa feodalizmi", date: "500–1400", desc: "Ritsarlar, salib yurishlari.", fact: "📖 Magna Carta 1215", wiki: "https://uz.wikipedia.org/wiki/O‘rta_asrlar", lat: 48.8566, lon: 2.3522 }
        ],
        modern: [
            { name: "Buyuk kashfiyotlar", date: "1492–1522", desc: "Kolumb, Magellan.", fact: "🌎 Globallashuv boshlanishi", wiki: "https://uz.wikipedia.org/wiki/Buyuk_geografik_kashfiyotlar", lat: 40.4168, lon: -3.7038 },
            { name: "Uyg‘onish davri", date: "1300–1600", desc: "Leonardo, Mikelanjelo, Gutenberg.", fact: "📖 Bosmaxona inqilobi", wiki: "https://uz.wikipedia.org/wiki/Uyg‘onish_davri", lat: 43.7696, lon: 11.2558 },
            { name: "Ilmiy inqilob", date: "1543–1687", desc: "Galiley, Nyuton.", fact: "🍎 Nyuton qonunlari", wiki: "https://uz.wikipedia.org/wiki/Ilmiy_inqilob", lat: 52.5200, lon: 13.4050 },
            { name: "Fransuz inqilobi", date: "1789–1815", desc: "Erkinlik, tenglik.", fact: "⚜️ Napoleon kodeksi", wiki: "https://uz.wikipedia.org/wiki/Fransuz_inqilobi", lat: 48.8566, lon: 2.3522 }
        ],
        contemporary: [
            { name: "Birinchi jahon urushi", date: "1914–1918", desc: "20 million o‘lim.", fact: "🕊️ Millatlar ligasi", wiki: "https://uz.wikipedia.org/wiki/Birinchi_jahon_urushi", lat: 50.8503, lon: 4.3517 },
            { name: "Ikkinchi jahon urushi", date: "1939–1945", desc: "70-85 million o‘lim, atom bombasi.", fact: "☮️ BMT tashkil topdi", wiki: "https://uz.wikipedia.org/wiki/Ikkinchi_jahon_urushi", lat: 52.5200, lon: 13.4050 },
            { name: "Sovuq urush", date: "1947–1991", desc: "Kosmik poyga, Berlin devori.", fact: "🚀 Oyga qo‘nish 1969", wiki: "https://uz.wikipedia.org/wiki/Sovuq_urush", lat: 38.9072, lon: -77.0369 },
            { name: "Raqamli inqilob", date: "1970–hozir", desc: "Internet, AI.", fact: "📡 WWW 1991", wiki: "https://uz.wikipedia.org/wiki/Internet", lat: 37.7749, lon: -122.4194 }
        ]
    };

    const persons = [
        { name: "Konfutsiy", desc: "Xitoy faylasufi", img: "https://cdn-icons-png.flaticon.com/512/3135/3135715.png" },
        { name: "Sokrat", desc: "Yunon faylasufi", img: "https://cdn-icons-png.flaticon.com/512/2961/2961984.png" },
        { name: "Juli Tsezar", desc: "Rim sarkardasi", img: "https://cdn-icons-png.flaticon.com/512/3081/3081954.png" },
        { name: "Ibn Sino", desc: "Buyuk tabib", img: "https://cdn-icons-png.flaticon.com/512/3664/3664683.png" },
        { name: "Leonardo da Vinchi", desc: "Uyg‘onish dahosi", img: "https://cdn-icons-png.flaticon.com/512/1995/1995572.png" },
        { name: "Mirzo Ulug‘bek", desc: "Astronom", img: "https://cdn-icons-png.flaticon.com/512/3183/3183452.png" },
        { name: "Mustaqil Gandhi", desc: "Tinchlik tarafdori", img: "https://cdn-icons-png.flaticon.com/512/4805/4805503.png" },
        { name: "Albert Einstein", desc: "Fizik", img: "https://cdn-icons-png.flaticon.com/512/1641/1641960.png" },
        { name: "Qatron", desc: "Misr malikasi", img: "https://cdn-icons-png.flaticon.com/512/167/167548.png" },
        { name: "Genghis Khan", desc: "Mo‘g‘ul imperatori", img: "https://cdn-icons-png.flaticon.com/512/3248/3248709.png" },
        { name: "Napoleon", desc: "Fransuz sarkardasi", img: "https://cdn-icons-png.flaticon.com/512/3075/3075625.png" },
        { name: "Martin Luther King", desc: "Huquq himoyachisi", img: "https://cdn-icons-png.flaticon.com/512/3663/3663359.png" }
    ];

    function createCard(item, lang = 'uz') {
        let wikiUrl = item.wiki;
        let title = item.name;
        let descText = item.desc;
        if(lang === 'en') {
            const enMap = { "Shumerlar":"Sumerians","Qadimgi Misr":"Ancient Egypt","Indus vodiysi":"Indus Valley","Qadimgi Xitoy":"Ancient China","Yunoniston":"Greece","Rim imperiyasi":"Roman Empire","Vizantiya":"Byzantium","Islom Oltin asri":"Islamic Golden Age","Mo‘g‘ullar":"Mongols","Yevropa feodalizmi":"European Feudalism","Buyuk kashfiyotlar":"Great Discoveries","Uyg‘onish davri":"Renaissance","Ilmiy inqilob":"Scientific Revolution","Fransuz inqilobi":"French Revolution","Birinchi jahon urushi":"WWI","Ikkinchi jahon urushi":"WWII","Sovuq urush":"Cold War","Raqamli inqilob":"Digital Revolution"};
            title = enMap[title] || title;
            descText = item.desc.replace("Birinchi yozuv", "First writing").replace("Piramidalar", "Pyramids");
        }
        return `
            <div class="history-card" data-name="${item.name.toLowerCase()} ${item.desc.toLowerCase()}">
                <div class="card-header"><i class="fas fa-landmark"></i> ${title}</div>
                <div class="card-body">
                    <div class="date-range">${item.date}</div>
                    <p>${descText}</p>
                    <div class="fact-highlight">✨ ${item.fact}</div>
                    <a href="${wikiUrl}" target="_blank" class="wiki-link"><i class="fab fa-wikipedia-w"></i> Batafsil (Wikipedia)</a>
                </div>
            </div>
        `;
    }

    function renderAll(lang) {
        document.getElementById('antiquityGrid').innerHTML = historyData.antiquity.map(c => createCard(c, lang)).join('');
        document.getElementById('medievalGrid').innerHTML = historyData.medieval.map(c => createCard(c, lang)).join('');
        document.getElementById('modernGrid').innerHTML = historyData.modern.map(c => createCard(c, lang)).join('');
        document.getElementById('contemporaryGrid').innerHTML = historyData.contemporary.map(c => createCard(c, lang)).join('');
        // galereya
        document.getElementById('personsGallery').innerHTML = persons.map(p => `
            <div class="person-card"><img src="${p.img}" alt="${p.name}"><div class="person-name">${p.name}</div><div class="person-desc">${p.desc}</div></div>
        `).join('');
    }

    // Qidiruv
    function filterCards() {
        let term = document.getElementById('searchInput').value.toLowerCase();
        let cards = document.querySelectorAll('.history-card');
        let visibleCount = 0;
        cards.forEach(card => {
            let text = card.getAttribute('data-name') || card.innerText.toLowerCase();
            if(text.includes(term) || term === '') {
                card.style.display = '';
                visibleCount++;
            } else {
                card.style.display = 'none';
            }
        });
        document.getElementById('searchStatus').innerHTML = term ? `🔎 ${visibleCount} ta natija topildi.` : '';
    }

    // Xarita
    let map;
    function initMap() {
        map = L.map('historyMap').setView([30, 20], 2);
        L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', { attribution: '&copy; OpenStreetMap' }).addTo(map);
        const markers = [...historyData.antiquity, ...historyData.medieval, ...historyData.modern, ...historyData.contemporary];
        markers.forEach(m => {
            if(m.lat && m.lon) L.marker([m.lat, m.lon]).addTo(map).bindPopup(`<b>${m.name}</b><br>${m.date}`);
        });
    }

    // Slayder (vaqt chizig‘i)
    const slider = document.getElementById('yearSlider');
    const yearLabel = document.getElementById('sliderYearLabel');
    const eventText = document.getElementById('sliderEventText');
    const eventsMap = {
        "-3500": "Shumer yozuvi paydo bo‘ldi", "-2500": "Indus vodiysi gulladi", "-1500": "Miken sivilizatsiyasi", "-500": "Axomaniylar, Buddizm", "0": "Iso tug‘ilgan yili", "476": "G‘arbiy Rim quladi", "622": "Hijra", "1096": "Salib yurishlari", "1348": "Qora o‘lim", "1492": "Kolumb Amerikani kashf etdi", "1789": "Fransuz inqilobi", "1914": "Birinchi jahon urushi", "1945": "BMT tashkil topdi", "1969": "Oyga qo‘nish", "2020": "COVID-19 pandemiyasi"
    };
    slider.addEventListener('input', () => {
        let val = parseInt(slider.value);
        let yearText = val < 0 ? `${Math.abs(val)} BCE` : `${val} CE`;
        yearLabel.innerText = yearText;
        let ev = eventsMap[val] || eventsMap[String(val)] || "Tarixiy voqea sodir bo‘ldi";
        eventText.innerText = ev;
    });

    // TIL, DARK MODE, AUDIO
    let currentLang = 'uz';
    document.getElementById('langToggleBtn').addEventListener('click', () => {
        currentLang = currentLang === 'uz' ? 'en' : 'uz';
        renderAll(currentLang);
        document.getElementById('langToggleBtn').innerHTML = currentLang === 'uz' ? '<i class="fas fa-language"></i> English' : '<i class="fas fa-language"></i> O‘zbek';
        filterCards();
    });
    document.getElementById('darkModeBtn').addEventListener('click', () => {
        document.body.classList.toggle('dark');
    });
    let audioPlaying = false;
    let audio = new Audio("https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3");
    audio.loop = true;
    document.getElementById('audioBtn').addEventListener('click', () => {
        if(audioPlaying) { audio.pause(); audioPlaying = false; document.getElementById('audioBtn').innerHTML = '<i class="fas fa-music"></i> Musiqa'; }
        else { audio.play(); audioPlaying = true; document.getElementById('audioBtn').innerHTML = '<i class="fas fa-stop"></i> To‘xtat'; }
    });
    window.addEventListener('load', () => {
        renderAll('uz');
        initMap();
        filterCards();
    });
    document.getElementById('searchInput').addEventListener('input', filterCards);
    // scroll top
    window.addEventListener('scroll', () => {
        document.getElementById('scrollTopBtn').classList.toggle('show', window.scrollY > 400);
    });
    document.getElementById('scrollTopBtn').addEventListener('click', () => window.scrollTo({top:0,behavior:'smooth'}));
</script>
</body>
</html>
