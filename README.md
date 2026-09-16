<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>Spell Timer</title>
<style>
    body {
        background: #222;
        color: white;
        font-family: Arial;
        text-align: center;
        margin-top: 80px;
    }

    #barContainer {
        width: 75%;
        height: 40px;
        background: #686868;
        margin: 0 auto;
        border-radius: 10px;
        overflow: hidden;
    }

    #bar {
        height: 100%;
        width: 100%;
        background: #9ff169;
        transition: width 0.2s linear;
    }

    #spellInfo {
        width: 70%;
        margin: 20px auto; 
        background: #333;
        color: #ddd;
        border: 1px solid #555;
        border-radius: 8px;
        padding: 15px;
        font-size: 18px;
        text-align: left; 
        line-height: 1.6; 
        box-sizing: border-box;
    }
    #spellInfo .title {
        font-weight: bold; 
        font-size: 19px;   
        color: #ffffff;            
        display: inline-block; 
        margin-bottom: 8px; 
    }

    button {
        margin: 15px;
        padding: 12px 20px;
        font-size: 20px;
	font-weight: bold;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        color: black;
        transition: transform 0.2s ease, background-color 0.2s ease;
    }

    /* 각 버튼 기본 색상 */
#startBtn { background: #f6da7e; }
    #swapBtn { background: #6cc2ed; }
    #resetBtn { background: #e18e8e; }

    /* 호버(마우스 오버) 효과 */
    #startBtn:hover:not(:disabled) { 
        background: #e0b25b; 
        transform: scale(1.1); 
    }
    #swapBtn:hover { 
        background: #5f97dd; 
        transform: scale(1.1); 
    }
    #resetBtn:hover { 
        background: #d65b5b; 
        transform: scale(1.1); 
    }

    #startBtn:disabled {
        background: #b5b5b5;
        color: #848484;
        cursor: not-allowed;
        transform: none; 
    }
</style>
</head>
<body>

<h1>보조특성 타이머</h1>
<div id="barContainer">
    <div id="bar"></div>
</div>

<h2 id="timeText">스펠 사용 가능</h2>

<button id="startBtn">타이머 시작</button>
<button id="swapBtn">스펠 교체(100초)</button>
<button id="resetBtn">초기화</button>
<div id="spellInfo">
  <span class="title">스펠 첫 사용 / 쿨타임 정보</span><br>순찰자 : 30초 / 쿨타임 90초<br>비정상 : 40초 / 쿨타임 90초<br>흥분 : 40초 / 쿨타임 100초<br>형상이동 : 45초 / 쿨타임 100초<br>텔레포트 : 50초 / 쿨타임 100초<br>플래시 : 60초 / 쿨타임 150초</div>
<script>
    let maxCooldown = 150;   // 기본 쿨타임
    let currentCooldown = 150;
    let remaining = 0;
    let timer = null;
    let startTime = null;

    const bar = document.getElementById("bar");
    const timeText = document.getElementById("timeText");
    const startBtn = document.getElementById("startBtn");
    const swapBtn = document.getElementById("swapBtn");
    const resetBtn = document.getElementById("resetBtn");
    const spellInfo = document.getElementById("spellInfo");

    function updateBar() {
        const percent = ((maxCooldown - remaining) / maxCooldown) * 100;
        bar.style.width = percent + "%";

        if (remaining <= 0) {
            clearInterval(timer);
            timer = null;
            bar.style.background = "#9ff169";
            bar.style.width = "100%";
            timeText.innerText = "스펠 사용 가능";
            
            startBtn.disabled = false; 
        } else {
            timeText.innerText = remaining.toFixed(1) + "초 남음";
        }
    }

    function startTimer() {
        if (timer) return;

        // 시작 버튼을 비활성화
        startBtn.disabled = true;

        bar.style.background = "#db504c";
        startTime = Date.now();

        timer = setInterval(() => {
            const elapsed = (Date.now() - startTime) / 1000;
            remaining = currentCooldown - elapsed;

            updateBar();

            if (remaining <= 0) {
                currentCooldown = maxCooldown;
                remaining = 0;
                updateBar();
            }
        }, 100);
    }

   function swapSpell() {
        const newCooldown = 100;
        swapBtn.innerText = "스펠 교체됨";

        if (!timer) {
            maxCooldown = newCooldown;
            currentCooldown = newCooldown;
            return;
        }
        const ratio = remaining / maxCooldown;
        const newRemaining = newCooldown * ratio;

        maxCooldown = newCooldown;
        currentCooldown = newRemaining;
        remaining = newRemaining;

        startTime = Date.now(); 
        updateBar();
    }

    function resetTimer() {
        clearInterval(timer);
        timer = null;
        maxCooldown = 150;
        currentCooldown = 150;
        remaining = 0;
        
        bar.style.width = "100%";
        bar.style.background = "#4CAF50";
        timeText.innerText = "스펠 사용 가능";

        startBtn.disabled = false;
        swapBtn.innerText = "스펠 교체(100초)";
    }

    // 버튼들에 이벤트(클릭) 연결
    startBtn.onclick = startTimer;
    swapBtn.onclick = swapSpell;
    resetBtn.onclick = resetTimer;
</script>
</body>
</html>
