<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>NatureSync | Control Center</title>
    <style>
        body { margin: 0; padding: 0; background: #000; height: 100vh; display: flex; align-items: center; justify-content: center; font-family: 'Segoe UI', sans-serif; overflow: hidden; color: #d8f3dc; }
        .forest-layer { position: absolute; width: 150%; height: 150%; z-index: -1; background: radial-gradient(circle at center, rgba(64, 145, 108, 0.6) 0%, rgba(27, 67, 50, 0.4) 30%, rgba(8, 28, 21, 0.8) 60%, rgba(0, 0, 0, 1) 90%); animation: intenseBreathe 8s ease-in-out infinite; }
        @keyframes intenseBreathe { 0%, 100% { transform: scale(0.9); opacity: 0.4; filter: blur(30px) brightness(0.8); } 50% { transform: scale(1.3); opacity: 1; filter: blur(15px) brightness(1.3); } }
        .card { background: rgba(10, 15, 12, 0.9); backdrop-filter: blur(30px); padding: 40px; border-radius: 40px; border: 2px solid rgba(116, 198, 157, 0.3); text-align: center; width: 420px; z-index: 10; box-shadow: 0 0 50px rgba(64, 145, 108, 0.3); }
        .slogan-box { margin-bottom: 30px; height: 60px; position: relative; cursor: pointer; overflow: hidden; }
        .slogan-box p { position: absolute; width: 100%; margin: 0; transition: 0.5s ease-in-out; }
        .slogan-tr { color: #74c69d; font-size: 1.5em; font-weight: bold; top: 15px; }
        .slogan-en { color: #95d5b2; font-size: 1.2em; top: 70px; font-style: italic; }
        .slogan-de { color: #52b788; font-size: 1.2em; top: 70px; font-style: italic; }
        .slogan-box:hover .slogan-tr { top: -50px; opacity: 0; }
        .slogan-box:hover .slogan-en { animation: slideCycleEN 4s infinite; }
        .slogan-box:hover .slogan-de { animation: slideCycleDE 4s infinite; }
        @keyframes slideCycleEN { 0%, 45% { top: 15px; opacity: 1; } 50%, 100% { top: -50px; opacity: 0; } }
        @keyframes slideCycleDE { 0%, 45% { top: 70px; opacity: 0; } 50%, 95% { top: 15px; opacity: 1; } 100% { top: -50px; opacity: 0; } }
        
        /* MESAJ ALANI VE EMOJI BUTONU */
        .lcd-container { position: relative; width: 90%; margin: 20px auto; }
        .lcd-screen { background: #081c15; color: #95d5b2; font-family: 'Courier New', monospace; font-size: 18px; border: 2px solid #409167; width: 100%; height: 50px; outline: none; padding: 10px; border-radius: 12px; text-align: center; box-shadow: inset 0 0 20px rgba(0,0,0,0.8); text-transform: uppercase; resize: none; overflow: hidden; }
        #emojiBtn { position: absolute; right: 10px; bottom: 8px; cursor: pointer; font-size: 20px; z-index: 5; filter: grayscale(1); transition: 0.3s; }
        #emojiBtn:hover { filter: grayscale(0); transform: scale(1.2); }
        #emojiBox { display: none; position: absolute; bottom: 60px; right: 0; background: #081c15; border: 2px solid #409167; padding: 10px; border-radius: 12px; z-index: 100; box-shadow: 0 0 20px #000; width: 180px; }
        #emojiBox span { cursor: pointer; padding: 8px; font-size: 20px; display: inline-block; transition: 0.2s; }
        #emojiBox span:hover { transform: scale(1.3); }

        .btn { width: 100%; height: 60px; border-radius: 16px; border: none; font-weight: bold; cursor: pointer; transition: 0.4s; margin: 12px 0; color: white; position: relative; overflow: hidden; }
        .btn span { position: absolute; width: 100%; left: 0; transition: 0.5s; }
        .tr-txt { top: 20px; }
        .en-txt, .de-txt { top: 70px; opacity: 0; }
        .btn:hover .tr-txt { top: -50px; opacity: 0; }
        .btn:hover .en-txt { animation: slideCycleEN 4s infinite; }
        .btn:hover .de-txt { animation: slideCycleDE 4s infinite; }
        .btn-conn { background: #081c15; color: #74c69d; border: 1px solid #1b4332; }
        .btn-msg { background: #40916c; }
        #status { margin-top: 20px; font-size: 0.8em; color: #74c69d; }
    </style>
</head>
<body>
    <div class="forest-layer"></div>
    <div class="card">
        <div class="slogan-box">
            <p class="slogan-tr">Senin Dünyan, Senin Hayatın</p>
            <p class="slogan-en">Your World, Your Life</p>
            <p class="slogan-de">Deine Welt, Dein Leben</p>
        </div>
        
        <div class="lcd-container">
            <textarea id="mInput" class="lcd-screen" placeholder="MESAJ YAZIN..." maxlength="60" oninput="fixText(this)"></textarea>
            <div id="emojiBtn" onclick="toggleEmoji()">🍃</div>
            <div id="emojiBox">
                <span onclick="addEmoji('\x03')">❤</span> <span onclick="addEmoji('\x01')">☺</span> <span onclick="addEmoji('\x2A')">★</span> <span onclick="addEmoji('\xF8')">°</span> <span onclick="addEmoji('\x18')">↑</span> 
                <span onclick="addEmoji('\x19')">↓</span>
                <span onclick="addEmoji('\x1A')">→</span>
            </div>
        </div>
        
        <button class="btn btn-conn" onclick="baglan()"><span class="tr-txt">SİSTEMİ UYANDIR</span><span class="en-txt">WAKE SYSTEM</span><span class="de-txt">SYSTEM AUFWACHEN</span></button>
        <button class="btn btn-msg" onclick="gonder()"><span class="tr-txt">SİSTEMİ GÜNCELLE</span><span class="en-txt">UPDATE SYSTEM</span><span class="de-txt">SYSTEM AKTUALISIEREN</span></button>
        <p id="status">Standby... | Beklemede... 🍃</p>
    </div>

    <script>
        let port, writer;

        // KARAKTER DÜZELTME VE SÜZGEÇ
        function fixText(el) {
            const trMap = {'ç':'C','Ç':'C','ğ':'G','Ğ':'G','ı':'I','İ':'I','ö':'O','Ö':'O','ş':'S','Ş':'S','ü':'U','Ü':'U'};
            let val = el.value;
            let corrected = "";
            for(let i=0; i<val.length; i++) {
                corrected += trMap[val[i]] || val[i];
            }
            // Sadece A-Z, 0-9, boşluk ve bizim özel hex kodlu emojilere izin ver
            el.value = corrected.toUpperCase().replace(/[^A-Z0-9\s\x03\x01\x2A\xF8\x18\x19\x1A]/g, '');
        }

        function toggleEmoji() {
            const box = document.getElementById("emojiBox");
            box.style.display = box.style.display === "none" ? "block" : "none";
        }

        function addEmoji(hexChar) {
            const input = document.getElementById("mInput");
            input.value += hexChar;
            fixText(input);
            document.getElementById("emojiBox").style.display = "none";
        }

        async function baglan() {
            try {
                port = await navigator.serial.requestPort();
                await port.open({ baudRate: 9600 });
                const encoder = new TextEncoderStream();
                encoder.readable.pipeTo(port.writable);
                writer = encoder.writable.getWriter();
                document.getElementById("status").innerText = "SİSTEM UYANDI! | SYSTEM AWAKE! 🍃";
            } catch (e) { alert("Bağlantı hatası!"); }
        }

        async function gonder() {
            if (!writer) return alert("Önce bağlanın!");
            let msg = document.getElementById("mInput").value.replace(/\n/g, ' ');
            if (msg === "") msg = "SENIN DUNYAN SENIN HAYATIN";
            const n = new Date();
            const p = (v) => String(v).padStart(2, '0');
            const data = "M:" + msg + "|S:" + p(n.getHours()) + ":" + p(n.getMinutes()) + "|T:" + p(n.getDate()) + "." + p(n.getMonth() + 1) + "." + n.getFullYear() + "." + n.getDay() + "\n";
            try {
                await writer.write(data);
                document.getElementById("status").innerText = "BAŞARIYLA GÜNCELLENDİ! ✅";
            } catch(e) { alert("Hata!"); }
        }
    </script>
</body>
</html>
