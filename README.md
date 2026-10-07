<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Калькулятор Рассрочки</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        body { background-color: #0b0b0b; color: #ffffff; display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }
        .calc-card { background: #161616; width: 100%; max-width: 400px; border-radius: 20px; padding: 25px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); }
        .input-group { margin-bottom: 20px; }
        label { display: block; font-size: 14px; color: #aeaeae; margin-bottom: 8px; }
        .input-wrapper { position: relative; display: flex; align-items: center; }
        input[type="number"] { width: 100%; background: #222; border: 1px solid #333; padding: 12px; border-radius: 10px; color: #fff; font-size: 16px; font-weight: bold; }
        .currency { position: absolute; right: 15px; color: #aeaeae; font-size: 16px; }
        input[type="range"] { width: 100%; margin-top: 10px; accent-color: #e53935; }
        .range-limits { display: flex; justify-content: space-between; font-size: 11px; color: #666; margin-top: 4px; }
        .divider { border-top: 1px solid #222; margin: 20px 0; }
        .result-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; }
        .result-label { font-size: 14px; color: #aeaeae; }
        .result-value { font-size: 18px; font-weight: bold; }
        .main-total { flex-direction: column; align-items: flex-start; margin-bottom: 20px; }
        .main-total .result-value { font-size: 32px; color: #ffffff; margin-top: 5px; }
    </style>
</head>
<body>

<div class="calc-card">
    <div class="input-group">
        <label>Стоимость товара</label>
        <div class="input-wrapper">
            <input type="number" id="totalCost" value="100000" oninput="calculate()">
            <span class="currency">₽</span>
        </div>
        <input type="range" id="totalCostSlider" min="5000" max="500000" step="5000" value="100000" oninput="syncSlider('totalCost')">
        <div class="range-limits"><span>5 000 ₽</span><span>500 000 ₽</span></div>
    </div>

    <div class="input-group">
        <label>Первый взнос</label>
        <div class="input-wrapper">
            <input type="number" id="firstPay" value="20000" oninput="calculate()">
            <span class="currency">₽</span>
        </div>
        <input type="range" id="firstPaySlider" min="0" max="100000" step="1000" value="20000" oninput="syncSlider('firstPay')">
        <div class="range-limits"><span>0 ₽</span><span id="maxFirstPayLabel">100 000 ₽</span></div>
    </div>

    <div class="input-group">
        <label>Срок рассрочки</label>
        <div class="input-wrapper">
            <input type="number" id="months" value="5" oninput="calculate()">
            <span class="currency">мес.</span>
        </div>
        <input type="range" id="monthsSlider" min="1" max="12" step="1" value="5" oninput="syncSlider('months')">
        <div class="range-limits"><span>1 мес.</span><span>12 мес.</span></div>
    </div>

    <div class="divider"></div>

    <div class="result-row main-total">
        <span class="result-label">Ежемесячный платёж</span>
        <span class="result-value" id="monthlyPaymentDisplay">0 ₽</span>
    </div>

    <div class="result-row">
        <span class="result-label">Накидка (5% в мес.)</span>
        <span class="result-value" id="markupDisplay">0 ₽</span>
    </div>

    <div class="result-row">
        <span class="result-label">Общая стоимость товара</span>
        <span class="result-value" id="finalCostDisplay">0 ₽</span>
    </div>
</div>

<script>
function syncSlider(id) {
    document.getElementById(id).value = document.getElementById(id + 'Slider').value;
    calculate();
}

function calculate() {
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
    let monthlyRate = 0.05; 
    
    let markup = debt * monthlyRate * months; 
    let finalCost = totalCost + markup; 
    let monthlyPayment = months > 0 ? (debt + markup) / months : 0; 

    document.getElementById('markupDisplay').innerText = Math.round(markup).toLocaleString() + ' ₽';
    document.getElementById('finalCostDisplay').innerText = Math.round(finalCost).toLocaleString() + ' ₽';
    document.getElementById('monthlyPaymentDisplay').innerText = Math.round(monthlyPayment).toLocaleString() + ' ₽';
}
calculate();
</script>
</body>
</html>
