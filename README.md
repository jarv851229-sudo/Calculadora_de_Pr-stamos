
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Calculadora de Préstamos - Caja de Ahorro </title>
<style>
/* Estilos generales */
* {margin: 0; padding: 0; box-sizing: border-box;}
body {font-family: 'Arial', sans-serif; background: linear-gradient(135deg, #e8f5e8, #c8e6c9); min-height: 100vh; padding: 20px;}
.container {max-width: 800px; margin: 0 auto; background: white; border-radius: 15px; box-shadow: 0 10px 30px rgba(76, 175, 80, 0.3); overflow: hidden;}
.header {background: linear-gradient(135deg, #c1ffc3, #66bb6a); color: white; padding: 30px; text-align: center;}
.header h1 {font-size: 2.2em; margin-bottom: 10px; text-shadow: 0 2px 4px rgba(0,0,0,0.3);}
.header p {font-size: 1.1em; opacity: 0.9;}
.form-container {padding: 30px;}
.main-content {display: flex; gap: 30px; align-items: flex-start;}
.form-section {flex: 1; min-width: 350px;}
.results-section {flex: 1; min-width: 350px;}
.form-group {margin-bottom: 25px;}
label {display: block; margin-bottom: 8px; font-weight: bold; color: #2e7d32; font-size: 1.1em;}
select, input {width: 100%; padding: 12px; border: 2px solid #a5d6a7; border-radius: 8px; font-size: 1em; transition: all 0.3s ease;}
select:focus, input:focus {outline: none; border-color: #4caf50; box-shadow: 0 0 0 3px rgba(76, 175, 80, 0.2);}
.hidden {display: none;}
.btn {background: linear-gradient(135deg, #4caf50, #66bb6a); color: white; border: none; padding: 15px 30px; border-radius: 8px; font-size: 1.1em; font-weight: bold; cursor: pointer; transition: all 0.3s ease; margin-right: 10px; margin-bottom: 10px;}
.btn:hover {background: linear-gradient(135deg, #45a049, #5cb85c); transform: translateY(-2px); box-shadow: 0 5px 15px rgba(76, 175, 80, 0.4);}
.btn-print {background: linear-gradient(135deg, #81c784, #a5d6a7);}
.btn-print:hover {background: linear-gradient(135deg, #66bb6a, #81c784);}
.results {background: linear-gradient(135deg, #f1f8e9, #e8f5e8); border: 2px solid #c8e6c9; border-radius: 10px; padding: 25px; margin-top: 0; height: fit-content; position: sticky; top: 20px;}
.results h3 {color: #2e7d32; margin-bottom: 20px; font-size: 1.4em; text-align: center;}
.result-item {display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid #c8e6c9;}
.result-item:last-child {border-bottom: none; font-weight: bold; font-size: 1.2em; color: #2e7d32;}
.result-label {font-weight: bold; color: #388e3c;}
.result-value {font-weight: bold; color: #1b5e20;}
.warning {background: #fff3e0; border: 2px solid #ff9800; border-radius: 8px; padding: 15px; margin-top: 20px; text-align: center;}
.warning-text {color: #e53935; font-weight: bold; font-size: 1.1em; text-transform: uppercase;}
.error {color: #d32f2f; background: #ffebee; border: 1px solid #e57373; border-radius: 5px; padding: 10px; margin-top: 10px;}

/* Botón interactivo con vibración */
.btn-link {
    display: inline-block;
    background: linear-gradient(135deg, #4caf50, #66bb6a);
    color: white;
    padding: 15px 30px;
    border-radius: 8px;
    text-decoration: none;
    font-weight: bold;
    font-size: 1.1em;
    text-align: center;
    transition: all 0.1s ease;
    animation: vibrate 0.3s infinite;
}

/* Animación vibración */
@keyframes vibrate {
    0% { transform: translate(0px, 0px) rotate(0deg); }
    20% { transform: translate(-2px, 2px) rotate(-1deg); }
    40% { transform: translate(-2px, -2px) rotate(1deg); }
    60% { transform: translate(2px, 2px) rotate(0deg); }
    80% { transform: translate(2px, -2px) rotate(1deg); }
    100% { transform: translate(0px, 0px) rotate(0deg); }
}

@media print {body {background: white; padding: 0;} .btn, .warning {display: none !important;} .container {box-shadow: none; border: 1px solid #ddd;}}
@media (max-width: 600px) {.header h1 {font-size: 1.8em;} .form-container {padding: 20px;} .btn {width: 100%; margin-right: 0;} .main-content {flex-direction: column;} .results-section {order: -1;}}
</style>
</head>
<body>
<div class="container">
    <div class="header">
        <>
        <h1>Calculadora de Préstamos</h1>
        <p>Caja de Ahorro</p>
    </div>
    <div class="form-container">
        <div class="main-content">
            <div class="form-section">
                <form id="loanForm">
                    <div class="form-group">
                        <label for="memberStatus">¿Eres miembro activo de la caja de ahorro?</label>
                        <select id="memberStatus" required>
                            <option value="">Selecciona una opción</option>
                            <option value="active">Sí, soy miembro activo</option>
                            <option value="inactive">No soy miembro activo</option>
                        </select>
                    </div>
                    <div id="unionStatus" class="form-group hidden">
                        <label for="unionMember">Tipo de empleado:</label>
                        <select id="unionMember">
                            <option value="">Selecciona una opción</option>
                            <option value="union">Sindicalizado</option>
                            <option value="nonunion">No Sindicalizado</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="loanAmount">Monto del préstamo (MXN):</label>
                        <input type="number" id="loanAmount" min="500" step="500" placeholder="Ingresa el monto en múltiplos de $500" required>
                        <div id="amountError" class="error hidden"></div>
                    </div>
                    <div class="form-group">
                        <label for="loanTerm">Plazo (meses):</label>
                        <select id="loanTerm" required>
                            <option value="">Selecciona el plazo</option>
                        </select>
                        <div id="termError" class="error hidden"></div>
                    </div>
                    <button type="submit" class="btn">Calcular Préstamo</button>
                    <button type="button" class="btn btn-print hidden" id="printBtn" onclick="window.print()">Imprimir Resultados</button>
                </form>
                <div id="warningSection" class="warning hidden">
                    <p class="warning-text">Esta simulación no asegura la aprobación de tu préstamo</p>
                </div>
            </div>
            <div class="results-section">
                <div id="results" class="results hidden">
                    <h3>Resultados del Cálculo</h3>
                    <div class="result-item">
                        <span class="result-label">Monto solicitado:</span>
                        <span class="result-value" id="displayAmount">$0</span>
                    </div>
                    <div class="result-item">
                        <span class="result-label">Plazo:</span>
                        <span class="result-value" id="displayTerm">0 meses</span>
                    </div>
                    <div class="result-item">
                        <span class="result-label">Tasa de interés mensual:</span>
                        <span class="result-value" id="displayRate">0%</span>
                    </div>
                    <div class="result-item">
                        <span class="result-label">Total de intereses:</span>
                        <span class="result-value" id="displayInterest">$0</span>
                    </div>
                    <div class="result-item">
                        <span class="result-label">Total a pagar:</span>
                        <span class="result-value" id="displayTotal">$0</span>
                    </div>
                    <div class="result-item">
                        <span class="result-label">Pago semanal:</span>
                        <span class="result-value" id="displayWeeklyPayment">$0</span>
                    </div>
                    <!-- Botón vibrante -->
                    <div class="result-item" style="justify-content:center; margin-top:15px;">
                        <a id="applyBtn" href="https://forms.office.com/r/VyTHXsh0Cj" target="_blank" class="btn-link hidden">
                            SOLICITA AQUI TU PRESTAMO
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
const memberStatusSelect = document.getElementById('memberStatus');
const unionStatusDiv = document.getElementById('unionStatus');
const unionMemberSelect = document.getElementById('unionMember');
const loanAmountInput = document.getElementById('loanAmount');
const loanTermSelect = document.getElementById('loanTerm');
const resultsDiv = document.getElementById('results');
const applyBtn = document.getElementById('applyBtn');
const printBtn = document.getElementById('printBtn');
const warningSection = document.getElementById('warningSection');
const form = document.getElementById('loanForm');

const loanConfig = {
    active: { maxAmount: 30000, maxTerm: 12, interestRate: 0.015 },
    inactive: { 
        union: { maxAmount: 6000, maxTerm: 6, interestRate: 0.03 },
        nonunion: { maxAmount: 10000, maxTerm: 6, interestRate: 0.03 }
    }
};

memberStatusSelect.addEventListener('change', function() {
    const status = this.value;
    if (status === 'inactive') { unionStatusDiv.classList.remove('hidden'); unionMemberSelect.required = true; }
    else { unionStatusDiv.classList.add('hidden'); unionMemberSelect.required = false; unionMemberSelect.value = ''; }
    updateTermOptions();
    validateAmount();
});

unionMemberSelect.addEventListener('change', function() { updateTermOptions(); validateAmount(); });

function updateTermOptions() {
    const status = memberStatusSelect.value;
    loanTermSelect.innerHTML = '<option value="">Selecciona el plazo</option>';
    let maxTerm = (status === 'active') ? 12 : 6;
    for (let i = 1; i <= maxTerm; i++) {
        const option = document.createElement('option');
        option.value = i;
        option.textContent = `${i} ${i === 1 ? 'mes' : 'meses'}`;
        loanTermSelect.appendChild(option);
    }
}

function validateAmount() {
    const amount = parseFloat(loanAmountInput.value);
    const status = memberStatusSelect.value;
    const unionStatus = unionMemberSelect.value;
    const amountError = document.getElementById('amountError');
    amountError.classList.add('hidden');
    if (!amount || amount % 500 !== 0) { amountError.textContent = 'El monto debe ser múltiplo de $500'; amountError.classList.remove('hidden'); return false; }
    let maxAmount = 0;
    if (status === 'active') maxAmount = loanConfig.active.maxAmount;
    else if (status === 'inactive') maxAmount = (unionStatus==='union')?loanConfig.inactive.union.maxAmount:loanConfig.inactive.nonunion.maxAmount;
    if (amount > maxAmount) { amountError.textContent = `El monto máximo permitido es $${maxAmount.toLocaleString('es-MX')}`; amountError.classList.remove('hidden'); return false; }
    if (amount < 500) { amountError.textContent = 'El monto mínimo es $500'; amountError.classList.remove('hidden'); return false; }
    return true;
}

loanAmountInput.addEventListener('input', validateAmount);

form.addEventListener('submit', function(e) {
    e.preventDefault();
    if (!validateAmount()) return;
    const amount = parseFloat(loanAmountInput.value);
    const term = parseInt(loanTermSelect.value);
    const status = memberStatusSelect.value;
    const unionStatus = unionMemberSelect.value;
    let interestRate = (status==='active')?loanConfig.active.interestRate:(unionStatus==='union')?loanConfig.inactive.union.interestRate:loanConfig.inactive.nonunion.interestRate;

    const totalInterest = amount * interestRate * term;
    const totalAmount = amount + totalInterest;
    let weeklyPayment = (status==='active' && term===12)?totalAmount/52:totalAmount/(term*4);

    document.getElementById('displayAmount').textContent = `${amount.toLocaleString('es-MX')}`;
    document.getElementById('displayTerm').textContent = `${term} ${term===1?'mes':'meses'}`;
    document.getElementById('displayRate').textContent = `${(interestRate*100).toFixed(1)}%`;
    document.getElementById('displayInterest').textContent = `${totalInterest.toLocaleString('es-MX',{minimumFractionDigits:2, maximumFractionDigits:2})}`;
    document.getElementById('displayTotal').textContent = `${totalAmount.toLocaleString('es-MX',{minimumFractionDigits:2, maximumFractionDigits:2})}`;
    document.getElementById('displayWeeklyPayment').textContent = `${weeklyPayment.toLocaleString('es-MX',{minimumFractionDigits:2, maximumFractionDigits:2})}`;

    resultsDiv.classList.remove('hidden');
    warningSection.classList.remove('hidden');
    printBtn.classList.remove('hidden');
    applyBtn.classList.remove('hidden');
});
</script>
</body>
</html>

