# beachgame
<!DOCTYPE html>
<html lang="nl">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🏖️ Strand Speurtocht</title>

    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            overflow: hidden;
        }

        /* STARTSCHERM */

        #startScreen {
            position: fixed;
            inset: 0;
            background: #f4d28c;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            z-index: 9999;
        }

        #startScreen h1 {
            font-size: 3em;
        }

        #startScreen p {
            font-size: 1.5em;
            max-width: 600px;
            padding: 20px;
        }

        #startBtn {
            font-size: 2em;
            padding: 15px 35px;
            border: none;
            border-radius: 20px;
            background: #ffb703;
            cursor: pointer;
        }

        #startBtn:hover {
            background: #ffca3a;
        }

        /* ZIJBALK */

        #sidebar {
            position: fixed;
            left: 0;
            top: 0;
            width: 220px;
            height: 100vh;
            background: white;
            padding: 15px;
            border-right: 3px solid #ddd;
            box-sizing: border-box;
            z-index: 10;
        }

        #timer {
            font-size: 28px;
            font-weight: bold;
            margin-bottom: 20px;
        }

        #lijst div {
            margin: 12px 0;
            font-size: 24px;
        }

        .gevonden {
            color: green;
            text-decoration: line-through;
        }

        /* STRAND */

        #strand {
            margin-left: 220px;
            width: calc(100vw - 220px);
            height: 100vh;
            background: #E9C46A;
            position: relative;
            overflow: hidden;
        }

        /* SCHEP */

        .schep {
            position: absolute;
            font-size: 55px;
            pointer-events: none;
            z-index: 200;
            animation: pop 0.5s forwards;
        }

        @keyframes pop {
            from {
                opacity: 1;
                transform: scale(0.5);
            }

            to {
                opacity: 0;
                transform: scale(1.4);
            }
        }

        /* GAT */

        .gat {
            position: absolute;
            width: 90px;
            height: 90px;
            border-radius: 50%;
            background: #d3a75e;
            pointer-events: none;
            z-index: 50;
        }

        /* VOORWERPEN */

        .item {
            position: absolute;
            width: 120px;
            height: 120px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 70px;
            color: transparent;
            z-index: 100;
        }

        .gevondenItem {
            color: black !important;
            animation: bounce .4s;
        }

        @keyframes bounce {
            0% {
                transform: scale(0);
            }

            70% {
                transform: scale(1.4);
            }

            100% {
                transform: scale(1);
            }
        }

        /* EINDSCHERM */

        #win {
            display: none;
            position: absolute;
            inset: 0;
            background: rgba(255, 255, 255, 0.95);
            justify-content: center;
            align-items: center;
            flex-direction: column;
            text-align: center;
            font-size: 2em;
            z-index: 999;
        }
    </style>
</head>

<body>

    <div id="startScreen">
        <h1>🏖️ Strand Speurtocht</h1>

        <p>
            Schep in het zand om de verborgen voorwerpen te vinden!
        </p>

        <button id="startBtn">START</button>
    </div>

    <div id="sidebar">

        <h2>Zoek:</h2>

        <div id="timer">⏱️ 0 sec</div>

        <div id="lijst">
            <div id="l1">🪣 Emmer</div>
            <div id="l2">🥄 Schep</div>
            <div id="l3">🐚 Schelp</div>
            <div id="l4">⚽ Bal</div>
            <div id="l5">🦀 Krab</div>
        </div>

    </div>

    <div id="strand">

        <div class="item" id="item1" style="left:10%;top:15%;">🪣</div>
        <div class="item" id="item2" style="left:65%;top:18%;">🥄</div>
        <div class="item" id="item3" style="left:35%;top:45%;">🐚</div>
        <div class="item" id="item4" style="left:70%;top:65%;">⚽</div>
        <div class="item" id="item5" style="left:20%;top:70%;">🦀</div>

        <div id="win"></div>

    </div>

    <script>

        let seconden = 0;
        let timerInterval;
        let gevonden = 0;

        const startBtn = document.getElementById("startBtn");
        const startScreen = document.getElementById("startScreen");
        const strand = document.getElementById("strand");

        /* START */

        startBtn.onclick = function () {

            startScreen.style.display = "none";

            timerInterval = setInterval(function () {

                seconden++;

                document.getElementById("timer").innerHTML =
                    "⏱️ " + seconden + " sec";

            }, 1000);

        };

        /* VOORWERPEN */

        const items = [
            { id: "item1", lijst: "l1" },
            { id: "item2", lijst: "l2" },
            { id: "item3", lijst: "l3" },
            { id: "item4", lijst: "l4" },
            { id: "item5", lijst: "l5" }
        ];

        function schep(x, y) {

            const rect = strand.getBoundingClientRect();

            x = x - rect.left;
            y = y - rect.top;

            /* GAT */

            const gat = document.createElement("div");

            gat.className = "gat";
            gat.style.left = (x - 45) + "px";
            gat.style.top = (y - 45) + "px";

            strand.appendChild(gat);

            /* SCHEP BOVEN HET GAT */

            const shovel = document.createElement("div");

            shovel.className = "schep";
            shovel.innerHTML = "🥄";
            shovel.style.left = (x - 25) + "px";
            shovel.style.top = (y - 25) + "px";

            strand.appendChild(shovel);

            setTimeout(function () {
                shovel.remove();
            }, 500);

            /* CONTROLEREN */

            items.forEach(function (item) {

                const el = document.getElementById(item.id);

                if (el.classList.contains("gevondenItem")) {
                    return;
                }

                const r = el.getBoundingClientRect();

                const binnen =
                    x + rect.left >= r.left &&
                    x + rect.left <= r.right &&
                    y + rect.top >= r.top &&
                    y + rect.top <= r.bottom;

                if (binnen) {

                    el.classList.add("gevondenItem");

                    document
                        .getElementById(item.lijst)
                        .classList.add("gevonden");

                    gevonden++;

                    if (gevonden === items.length) {

                        clearInterval(timerInterval);

                        const win =
                            document.getElementById("win");

                        win.style.display = "flex";

                        win.innerHTML =
                            "🎉 Goed gedaan!<br><br>" +
                            "Je hebt alle voorwerpen gevonden!<br><br>" +
                            "⏱️ Tijd: " + seconden + " seconden";
                    }
                }

            });

        }

        strand.addEventListener("click", function (e) {
            schep(e.clientX, e.clientY);
        });

        strand.addEventListener("touchstart", function (e) {

            const t = e.touches[0];

            schep(t.clientX, t.clientY);

        });

    </script>

</body>

</html>
