<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tayyib Finance | Исламская рассрочка</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        body { background-color: #0d1117; color: #ffffff; display: flex; flex-direction: column; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }
        
        /* Вкладки сверху */
        .tabs-container { display: flex; width: 100%; max-width: 400px; background: #161b22; border: 1px solid #21262d; border-radius: 14px; padding: 5px; margin-bottom: 15px; }
        .tab-button { flex: 1; background: none; border: none; color: #8b949e; padding: 10px; font-size: 14px; font-weight: 600; cursor: pointer; border-radius: 10px; transition: all 0.2s; }
        .tab-button.active { background: #21262d; color: #c69b55; }

        .calc-card { background: #161b22; width: 100%; max-width: 400px; border-radius: 24px; padding: 30px; box-shadow: 0 15px 35px rgba(0,0,0,0.6); border: 1px solid #21262d; }
        .brand-header { text-align: center; margin-bottom: 25px; }
        .brand-title { font-size: 24px; font-weight: 800; color: #c69b55; letter-spacing: 1px; text-transform: uppercase; }
        .brand-subtitle { font-size: 12px; color: #8b949e; margin-top: 4px; text-transform: uppercase; letter-spacing: 2px; }
        
        .input-group { margin-bottom: 20px; }
        label { display: block; font-size: 13px; color: #8b949e; margin-bottom: 8px; font-weight: 500; }
        .input-wrapper { position: relative; display: flex; align-items: center; }
        input[type="number"] { width: 100%; background: #0d1117; border: 1px solid #30363d; padding: 14px; border-radius: 12px; color: #fff; font-size: 18px; font-weight: bold; transition: border 0.2s; }
        input[type="number"]:focus { border-color: #c69b55; outline: none; }
        .currency { position: absolute; right: 15px; color: #8b949e; font-size: 16px; font-weight: 600; }
        input[type="range"] { width: 100%; margin-top: 12px; accent-color: #2ea043; }
        .range-limits { display: flex; justify-content: space-between; font-size: 11px; color: #484f58; margin-top: 5px; }
        
        .divider { border-top: 1px solid #21262d; margin: 25px 0; }
        .result-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px; }
        .result-label { font-size: 14px; color: #8b949e; }
        .result-value { font-size: 18px; font-weight: 700; color: #f0f6fc; }
        
        .main-total { flex-direction: column; align-items: flex-start; margin-bottom: 25px; background: #0d1117; padding: 15px; border-radius: 14px; border: 1px solid #21262d; }
        .main-total .result-label { font-size: 13px; color: #8b949e; }
        .main-total .result-value { font-size: 34px; color: #2ea043; margin-top: 5px; font-weight: 800; }
        .btn-action { display: block; width: 100%; background: linear-gradient(135deg, #1f883d 0%, #15652c 100%); color: white; border: none; padding: 16px; border-radius: 14px; font-size: 16px; font-weight: 700; text-align: center; cursor: pointer; box-shadow: 0 4px 12px rgba(31,136,61,0.3); }

        /* Переключаемые блоки калькуляторов */
        .calc-view { display: none; }
        .calc-view.active { display: block; }

        /* Админ-результаты */
        .admin-result { background: #0d1117; border: 1px solid #21262d; padding: 14px; border-radius: 12px; margin-top: 12px; display: flex; justify-content: space-between; font-size: 14px; }
        .admin-result span:last-child { font-weight: bold; color: #58a6ff; }
        .admin-result.my-share { border-color: #bb63cd; background: rgba(187,99,205,0.03); }
        .admin-result.my-share span:last-child { color: #ff79c6; font-size: 16px; }
    </style>
</head>
<body>

<!-- Переключатель между калькуляторами -->
<div class="tabs-container">
    <button class="tab-button active" onclick="switchTab('client')">Для клиента</button>
    <button class="tab-button" onclick="switchTab('admin')">Для фонда (Админ)</button>
</div>

<div class="calc-card">
    <div class="brand-header">
        <div class="brand-title">Tayyib Finance</div>
        <div class="brand-subtitle" id="calcSubtitle">Дозволенная рассрочка</div>
    </div>

    <!-- Общие поля ввода (синхронизируются) -->
    <div class="input-group">
        <label>Стоимость товара</label>
        <div class="input-wrapper">
            <input type="number" id="totalCost" value="100000" oninput="calculateAll()">
            <span class="currency">₽</span>
        </div>
        <input type="range" id="totalCostSlider" min="5000" max="500000" step="5000" value="100000" oninput="syncSlider('totalCost')">
        <div class="range-limits"><span>5 000 ₽</span><span>500 000 ₽</span></div>
    </div>

    <div class="input-group">
        <label>Первый взнос</label>
        <div class="input-wrapper">
            <input type="number" id="firstPay" value="20000" oninput="calculateAll()">
            <span class="currency">₽</span>
        </div>
        <input type="range" id="firstPaySlider" min="0" max="100000" step="1000" value="20000" oninput="syncSlider('firstPay')">
        <div class="range-limits"><span>0 ₽</span><span id="maxFirstPayLabel">100 000 ₽</span></div>
    </div>

    <div class="input-group">
        <label>Срок рассрочки</label>
        <div class="input-wrapper">
            <input type="number" id="months" value="5" oninput="calculateAll()">
            <span class="currency">мес.</span>
        </div>
        <input type="range" id="monthsSlider" min="1" max="12" step="1" value="5" oninput="syncSlider('months')">
        <div class="range-limits"><span>1 мес.</span><span>12 мес.</span></div>
    </div>

    <!-- 1. ВИД ДЛЯ КЛИЕНТА -->
    <div id="clientCalcView" class="calc-view active">
        <div class="divider"></div>
        <div class="result-row main-total">
            <span class="result-label">Ежемесячный платёж</span>
            <span class="result-value" id="clientMonthlyDisplay">0 ₽</span>
        </div>
        <div class="result-row">
            <span class="result-label">Накидка Мурабаха</span>
            <span class="result-value" id="clientMarkupDisplay">0 ₽</span>
        </div>
        <div class="result-row">
            <span class="result-label">Итоговая цена товара</span>
            <span class="result-value" id="clientFinalCostDisplay">0 ₽</span>
        </div>
        <button class="btn-action" style="margin-top: 15px;">Оформить Мурабаха</button>
    </div>

    <!-- 2. ВИД ДЛЯ АДМИНА -->
    <div id="adminCalcView" class="calc-view">
        <div class="divider"></div>
        <div class="input-group">
            <label style="color: #c69b55;">Установить процент фонда (% в месяц):</label>
            <div class="input-wrapper">
                <input type="number" id="adminPercent" value="5" step="0.1" oninput="calculateAll()">
                <span class="currency">%</span>
            </div>
        </div>
        <div class="admin-result">
            <span>Сумма долга фонда:</span>
            <span id="adminDebtDisplay">0 ₽</span>
        </div>
        <div class="admin-result">
            <span>Ежемесячный платёж:</span>
            <span id="adminMonthlyDisplay">0 ₽</span>
        </div>
        <div class="admin-result">
            <span>Общая чистая прибыль:</span>
            <span id="adminProfitDisplay">0 ₽</span>
        </div>
        <div class="admin-result my-share">
            <span>Твой доход управляющего (40%):</span>
            <span id="myEarningsDisplay">0 ₽</span>
        </div>
    </div>
</div>

<script>
function syncSlider(id) {
    document.getElementById(id).value = document.getElementById(id + 'Slider').value;
    calculateAll();
}

function switchTab(type) {
    document.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
    document.querySelectorAll('.calc-view').forEach(view => view.classList.remove('active'));
    
    if(type === 'client') {
        document.querySelectorAll('.tab-button')[0].classList.add('active');
        document.getElementById('clientCalcView').classList.add('active');
        document.getElementById('calcSubtitle').innerText = "Дозволенная рассрочка";
    } else {
        document.querySelectorAll('.tab-button')[1].classList.add('active');
        document.getElementById('adminCalcView').classList.add('active');
        document.getElementById('calcSubtitle').innerText = "Внутренний учет фонда";
    }
}

function calculateAll() {
    let totalCost = parseFloat(document.getElementById('totalCost').value) || 0;
    let firstPaySlider = document.getElementById('firstPaySlider');
    firstPaySlider.max = totalCost;
    document.getElementById('maxFirstPayLabel').innerText = totalCost.toLocaleString() + ' ₽';
    
    let firstPay = parseFloat(document.getElementById('firstPay').value) || 0;
    if (firstPay > totalCost) { firstPay = totalCost; document.getElementById('firstPay').value = firstPay; }
    document.getElementById('firstPaySlider').value = firstPay;

    let months = parseInt(document.getElementById('months').value) || 1;
    document.getElementById('monthsSlider').value = months;

    let debt = totalCost - firstPay;

    // Расчет для КЛИЕНТА (по умолчанию жестко зашито 5% в месяц, но без упоминания на экране)
    let clientRate = 0.05;
    let clientMarkup = debt * clientRate * months;
    let clientFinalCost = totalCost + clientMarkup;
    let clientMonthlyPayment = months > 0 ? (debt + clientMarkup) / months : 0;

    document.getElementById('clientMonthlyDisplay').innerText = Math.round(clientMonthlyPayment).toLocaleString() + ' ₽';
    document.getElementById('clientMarkupDisplay').innerText = Math.round(clientMarkup).toLocaleString() + ' ₽';
