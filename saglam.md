<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>NatureSync Tree | Control Center</title>
    <style>
        body { margin: 0; padding: 0; background: #000; height: 100vh; display: flex; align-items: center; justify-content: center; font-family: 'Segoe UI', sans-serif; overflow: hidden; color: #d8f3dc; }
        .forest-layer { position: absolute; width: 150%; height: 150%; z-index: -1; background: radial-gradient(circle at center, rgba(64, 145, 108, 0.6) 0%, rgba(27, 67, 50, 0.4) 30%, rgba(8, 28, 21, 0.8) 60%, rgba(0, 0, 0, 1) 90%); animation: intenseBreathe 8s ease-in-out infinite; }
        @keyframes intenseBreathe { 0%, 100% { transform: scale(0.9); opacity: 0.4; filter: blur(30px) brightness(0.8); } 50% { transform: scale(1.3); opacity: 1; filter: blur(15px) brightness(1.3); } }
        .card { background: rgba(10, 15, 12, 0.9); backdrop-filter: blur(30px); padding: 40px; border-radius: 40px; border: 2px solid rgba(116, 198, 157, 0.3); text-align: center; width: 420px; z-index: 10; box-shadow: 0 0 50px rgba(64, 145, 108, 0.3); }
        .slogan-box { margin-bottom: 30px; height: 60px; position: relative; cursor: pointer; }
        .slogan-box p { position: absolute; width: 100%; transition: all 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275); margin: 0; left: 0; }
        .slogan-tr-top { font-size: 1.6em; font-weight: bold; color: #74c69d; }
        .slogan-en-top { font-size: 1.2em; color: #95d5b2; opacity: 0; transform: translateY(20px); font-style: italic; }
        .slogan-box:hover .slogan-tr-top { opacity: 0; transform: translateY(-20px); }
        .slogan-box:hover .slogan-en-top { opacity: 1; transform: translateY(0); }
        .lcd-screen { background: #081c15; color: #95d5b2; font-family: 'Courier New', monospace; font-size: 22px; border: 2px solid #409167; width: 280px; height: 70px; outline: none; letter-spacing: 3px; padding: 10px; border-radius: 12px; margin: 25px auto; display: block; text-align: left; box-shadow: inset 0 0 20px rgba(0,0,0,0.8); resize: none; overflow: hidden; line-height: 35px; }
        .btn { width: 100%; padding: 18px; border-radius: 16px; border: none; font-weight: bold; cursor: pointer; transition: all 0.4s ease; margin: 12px 0; color: white; position: relative; overflow: hidden; display: flex; align-items: center; justify-content: center; }
        .btn span { transition: 0.4s; position: absolute; width: 100%; }
        .btn .en-text { opacity: 0; transform: translateY(25px); }
        .btn:hover .tr-text { opacity: 0; transform: translateY(-25px); }
        .btn:hover .en-text { opacity: 1; transform: translateY(0); }
        .btn-conn { background: #081c15; color: #74c69d; border: 1px solid #1b4332; }
        .btn-msg { background: #40916c; }
        .btn-time { background: #2d6a4f; }
        .btn:hover { transform: translateY(-3px); filter: brightness(1.2); }
        #status { margin-top: 25px; font-size: 0.8em; color: #74c69d; font-weight: bold; }
    </style>
</head>
<body>
    <div class="forest-layer"></div>
    <div class="card">
        <div class="slogan-box">
            <p class="slogan-tr-top">Senin Dünyan Senin Hayatın</p>
            <p class="slogan-en-top">Your World Your Life</p>
        </div>
        <textarea id="mInput" class="lcd-screen" placeholder="MAX 32 CHARS..." oninput="checkLCD(this)"></textarea>
        <button class="btn btn-conn" onclick="baglan()"><span class="tr-text">SİSTEMİ UYANDIR</span><span class="en-text">WAKE SYSTEM</span></button>
        <button class="btn btn-msg" onclick="gonder('M')"><span class="tr-text">DOĞANIN KALBİYLE UYUMLAN</span><span class="en-text">SYNC WITH NATURE</span></button>
        <button class="btn btn-time" onclick="gonder('T')"><span class="tr-text">ZAMANI EŞİTLE</span><span class="en-text">SYNC TIME</span></button>
        <p id="status">Standby... | Beklemede... 🍃</p>
    </div>
    <script>
        let port, writer;
        function checkLCD(el) {
            const trMap = {'ç':'c','Ç':'C','ğ':'g','Ğ':'G','ı':'i','İ':'I','ö':'o','Ö':'O','ş':'s','Ş':'S','ü':'u','Ü':'U'};
            el.value = el.value.replace(/[çÇğĞıİöÖşŞüÜ]/g, m => trMap[m]).toUpperCase();
            el.value = el.value.replace(/[^A-Z0-9\s]/g, '');
            let lines = el.value.split('\n');
            if (lines[0].length > 16) { el.value = lines[0].substring(0, 16) + '\n' + lines[0].substring(16) + (lines[1] ? lines[1] : ""); }
            let finalLines = el.value.split('\n');
            if (finalLines.length > 2) { el.value = finalLines[0].substring(0, 16) + '\n' + finalLines[1].substring(0, 16); }
            if (finalLines[1] && finalLines[1].length > 16) { el.value = finalLines[0] + '\n' + finalLines[1].substring(0, 16); }
        }
        async function baglan() {
            try { port = await navigator.serial.requestPort(); await port.open({ baudRate: 9600 }); const encoder = new TextEncoderStream(); encoder.readable.pipeTo(port.writable); writer = encoder.writable.getWriter(); document.getElementById("status").innerText = "SİSTEM UYANDI! | SYSTEM AWAKE! 🍃"; } catch (e) { alert("Hata!"); }
        }
        async function gonder(tip) {
            if (!writer) return alert("Önce bağlanın!");
            let data = "";
            if (tip === 'M') { data = "M:" + document.getElementById("mInput").value.replace('\n', ',') + "\n"; }
            else { const n = new Date(); const p = (v) => String(v).padStart(2, '0'); data = "T" + p(n.getDate()) + p(n.getMonth()+1) + n.getFullYear() + p(n.getHours()) + p(n.getMinutes()) + p(n.getSeconds()) + "\n"; }
            await writer.write(data);
        }
    </script>
</body>
</html>
