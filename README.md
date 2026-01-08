<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Legnano Knights - Gameday Live</title>
    <style>
        :root {
            --knights-red: #e30613;
            --knights-black: #050505;
            --knights-white: #ffffff;
        }

        body {
            font-family: 'Montserrat', sans-serif;
            background: radial-gradient(circle at top, #300205 0%, var(--knights-black) 100%);
            background-attachment: fixed;
            color: var(--knights-white);
            margin: 0; padding: 0; text-align: center;
        }

        header { padding: 30px 20px 10px; }
        .main-logo { width: 100px; margin-bottom: 10px; filter: drop-shadow(0 0 15px var(--knights-red)); }
        h1 { font-size: 26px; margin: 0; text-transform: uppercase; letter-spacing: 3px; font-weight: 900; }
        
        .container { padding: 10px 20px; max-width: 480px; margin: auto; }
        
        .section-title { font-size: 1.1em; text-transform: uppercase; letter-spacing: 2px; color: var(--knights-red); font-weight: 800; margin: 25px 0 12px; }

        .white-card { background: var(--knights-white); color: #111; border-radius: 12px; padding: 15px; box-shadow: 0 15px 35px rgba(0,0,0,0.6); border-bottom: 6px solid var(--knights-red); margin-bottom: 15px; text-align: left; }
        .info-row { padding: 8px 0; border-bottom: 1px solid #eee; font-size: 0.95em; display: flex; justify-content: space-between; }
        .label { font-weight: 800; color: var(--knights-red); text-transform: uppercase; font-size: 0.7em; }

        .curiosity-box { background: linear-gradient(135deg, var(--knights-red) 0%, #800000 100%); color: white; border-radius: 12px; padding: 20px; font-style: italic; margin-bottom: 20px; }

        .player-pill { background: var(--knights-white); color: #000; margin: 8px 0; padding: 10px 15px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; font-size: 0.82em; }
        .number { background: var(--knights-red); color: white; padding: 2px 8px; border-radius: 4px; margin-right: 10px; font-weight: bold; }

        table { width: 100%; border-collapse: collapse; background: white; color: black; border-radius: 12px; overflow: hidden; }
        th { background: #f4f4f4; padding: 10px; font-size: 0.7em; text-align: left; }
        td { padding: 12px; border-bottom: 1px solid #eee; font-size: 0.85em; }

        .social-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 30px; }
        .s-btn { padding: 15px; border-radius: 8px; text-decoration: none; color: white; font-weight: bold; font-size: 0.75em; text-transform: uppercase; text-align: center; }
        .inst { background: linear-gradient(45deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888); }
        
        #loading-status { font-size: 10px; opacity: 0.5; margin-bottom: 10px; }
    </style>
</head>
<body>

    <header>
        <img src="https://via.placeholder.com/150x150?text=KNIGHTS" id="main-logo-img" class="main-logo">
        <h1 id="main-title">LEGNANO KNIGHTS</h1>
    </header>

    <div class="container">
        <div id="loading-status">Sincronizzazione Live...</div>

        <div class="section-title">📅 Programma</div>
        <div id="container-programma" class="white-card"></div>

        <div class="section-title">🏟️ Prossima Partita</div>
        <div id="container-partita" class="white-card"></div>

        <div class="section-title">💡 Curiosità</div>
        <div id="container-curiosita" class="curiosity-box"></div>

        <div class="section-title">⚔️ Roster</div>
        <div id="container-roster"></div>

        <div class="section-title">🏆 Classifica</div>
        <table id="container-classifica">
            <thead><tr><th>POS</th><th>SQUADRA</th><th>PT</th></tr></thead>
            <tbody></tbody>
        </table>

        <div class="section-title">📱 Seguici sui nostri canali</div>
        <div id="container-social" class="social-grid"></div>

        <img src="https://via.placeholder.com/150x150?text=KNIGHTS" id="footer-logo-img" style="width:100px; margin-top:30px; opacity:0.8;">
        <div style="font-size: 10px; opacity: 0.4; padding: 20px 0 100px;">
            SOEVIS ARENA • KNIGHTS LANDING PAGE
        </div>
    </div>

    <script>
        // --- https://docs.google.com/spreadsheets/d/e/2PACX-1vQBU0Aws1giNhI6XWXogkA61v9unhit2bb58OhtTm5VXdYCvxUI73SP_lfcV5qXBa7T89ukZq8F7PR6/pub?output=csv ---
        const CSV_URL = "INSERISCI_QUI_IL_TUO_LINK_CSV";

        async function fetchData() {
            try {
                const response = await fetch(CSV_URL);
                const data = await response.text();
                const rows = data.split('\n').slice(1);

                // Pulizia contenitori
                document.getElementById('container-programma').innerHTML = "";
                document.getElementById('container-partita').innerHTML = "";
                document.getElementById('container-roster').innerHTML = "";
                document.getElementById('container-classifica').getElementsByTagName('tbody')[0].innerHTML = "";
                document.getElementById('container-social').innerHTML = "";

                rows.forEach(row => {
                    const [sezione, chiave, valore, extra] = row.split(',').map(item => item ? item.trim() : "");
                    if (!sezione) return;

                    const s = sezione.toLowerCase();

                    if (s === "programma") {
                        document.getElementById('container-programma').innerHTML += 
                            `<div class="info-row"><span>${chiave}</span><strong>${valore}</strong></div>`;
                    }
                    if (s === "partita") {
                        document.getElementById('container-partita').innerHTML += 
                            `<div class="info-row"><span class="label">${chiave}</span><strong>${valore}</strong></div>`;
                    }
                    if (s === "curiosita") {
                        document.getElementById('container-curiosita').innerText = valore;
                    }
                    if (s === "roster") {
                        document.getElementById('container-roster').innerHTML += 
                            `<div class="player-pill">
                                <div><span class="number">${chiave}</span>${valore}</div>
                                <div style="color:#666; font-size:0.9em;">${extra}</div>
                            </div>`;
                    }
                    if (s === "classifica") {
                        const tbody = document.getElementById('container-classifica').getElementsByTagName('tbody')[0];
                        const newRow = tbody.insertRow();
                        newRow.innerHTML = `<td>${chiave}</td><td>${valore}</td><td><strong>${extra}</strong></td>`;
                    }
                    if (s === "social") {
                        document.getElementById('container-social').innerHTML += 
                            `<a href="${valore}" class="s-btn inst">${chiave}</a>`;
                    }
                    if (s === "logo") {
                        if(chiave === "header") document.getElementById('main-logo-img').src = valore;
                        if(chiave === "footer") document.getElementById('footer-logo-img').src = valore;
                    }
                });

                document.getElementById('loading-status').innerText = "Dati aggiornati alle " + new Date().toLocaleTimeString();
            } catch (error) {
                document.getElementById('loading-status').innerText = "Errore di connessione al Foglio Google";
            }
        }

        fetchData();
        // Aggiorna ogni 60 secondi
        setInterval(fetchData, 60000);
    </script>
</body>
</html>
