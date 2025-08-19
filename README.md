<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Calculadora de Préstamos - Caja de Ahorro</title>
  <!-- Bootstrap 5 -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">

  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: linear-gradient(135deg, #e8f5e8, #c8e6c9);
      min-height: 100vh;
      padding: 20px;
    }

    .container-custom {
      max-width: 900px;
      background: white;
      border-radius: 15px;
      box-shadow: 0 10px 30px rgba(76, 175, 80, 0.3);
      overflow: hidden;
    }

    .header {
      background: linear-gradient(135deg, #4caf50, #66bb6a);
      color: white;
      padding: 30px;
      text-align: center;
    }

    .results {
      background: linear-gradient(135deg, #f1f8e9, #e8f5e8);
      border: 2px solid #c8e6c9;
      border-radius: 10px;
      padding: 25px;
      position: sticky;
      top: 20px;
    }

    .result-item {
      display: flex;
      justify-content: space-between;
      border-bottom: 1px solid #c8e6c9;
      padding: 8px 0;
    }

    .result-item:last-child {
      border-bottom: none;
      font-weight: bold;
      font-size: 1.2em;
      color: #2e7d32;
    }

    .warning {
      background: #fff3e0;
      border: 2px solid #ff9800;
      border-radius: 8px;
      padding: 15px;
      margin-top: 20px;
      text-align: center;
    }

    @media print {
      .btn, .warning {
        display: none !important;
      }
      body {
        background: white;
        padding: 0;
      }
    }
  </style>
</head>
<body>
  <div class="container container-custom p-0">
    <div class="header">
      <h1 class="h2 fw-bold">Calculadora de Préstamos</h1>
      <p class="mb-0">Caja de Ahorro</p>
    </div>

    <div class="p-4">
      <div class="row g-4">
        <!-- Formulario -->
        <div class="col-12 col-lg-6">
          <form id="loanForm">
            <div class="mb-3">
              <label for="memberStatus" class="form-label fw-bold text-success">¿Eres miembro activo de la caja de ahorro?</label>
              <select id="memberStatus" class="form-select" required>
                <option value="">Selecciona una opción</option>
                <option value="active">Sí, soy miembro activo</option>
                <option value="inactive">No soy miembro activo</option>
              </select>
            </div>

            <div id="unionStatus" class="mb-3 d-none">
              <label for="unionMember" class="form-label fw-bold text-success">Tipo de empleado:</label>
              <select id="unionMember" class="form-select">
                <option value="">Selecciona una opción</option>
                <option value="union">Sindicalizado</option>
                <option value="nonunion">No Sindicalizado</option>
              </select>
            </div>

            <div class="mb-3">
              <label for="loanAmount" class="form-label fw-bold text-success">Monto del préstamo (MXN):</label>
              <input type="number" id="loanAmount" class="form-control" min="500" step="500" placeholder="Ingresa el monto en múltiplos de $500" required>
              <div id="amountError" class="invalid-feedback d-block d-none"></div>
            </div>

            <div class="mb-3">
              <label for="loanTerm" class="form-label fw-bold text-success">Plazo (meses):</label>
              <select id="loanTerm" class="form-select" required>
                <option value="">Selecciona el plazo</option>
              </select>
              <div id="termError" class="invalid-feedback d-block d-none"></div>
            </div>

            <button type="submit" class="btn btn-success mb-2">Calcular Préstamo</button>
            <button type="button" class="btn btn-outline-success mb-2 d-none" id="printBtn" onclick="window.print()">Imprimir Resultados</button>
          </form>

          <div id="warningSection" class="warning d-none">
            <p class="text-danger fw-bold">Esta simulación no asegura la aprobación de tu préstamo</p>
          </div>
        </div>

        <!-- Resultados -->
        <div class="col-12 col-lg-6">
          <div id="results" class="results d-none">
            <h3 class="h5 text-success text-center mb-3">Resultados del Cálculo</h3>
            <div class="result-item">
              <span>Monto solicitado:</span>
              <span id="displayAmount">$0</span>
            </div>
            <div class="result-item">
              <span>Plazo:</span>
              <span id="displayTerm">0 meses</span>
            </div>
            <div class="result-item">
              <span>Tasa de interés mensual:</span>
              <span id="displayRate">0%</span>
            </div>
            <div class="result-item">
              <span>Total de intereses:</span>
              <span id="displayInterest">$0</span>
            </div>
            <div class="result-item">
              <span>Total a pagar:</span>
              <span id="displayTotal">$0</span>
            </div>
            <div class="result-item">
              <span>Pago semanal:</span>
              <span id="displayWeeklyPayment">$0</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Tu JS (lo dejé igual, solo ajustando clases d-none/d-block de Bootstrap) -->
  <script>
    const memberStatusSelect = document.getElementById('memberStatus');
    const unionStatusDiv = document.getElementById('unionStatus');
    const unionMemberSelect = document.getElementById('unionMember');
    const loanAmountInput = document.getElementById('loanAmount');
    const loanTermSelect = document.getElementById('loanTerm');
    const resultsDiv = document.getElementById('results');
    const form = document.getElementById('loanForm');
    const amountError = document.getElementById('amountError');
    const warningSection = document.getElementById('warningSection');
    const printBtn = document.getElementById('printBtn');

    const loanConfig = {
      active: { maxAmount: 30000, maxTerm: 12, interestRate: 0.015 },
      inactive: {
        union: { maxAmount: 6000, maxTerm: 6, interestRate: 0.03 },
        nonunion: { maxAmount: 10000, maxTerm: 6, interestRate: 0.03 }
      }
    };

    memberStatusSelect.addEventListener('change', function() {
      if (this.value === 'inactive') {
        unionStatusDiv.classList.remove('d-none');
        unionMemberSelect.required = true;
      } else {
        unionStatusDiv.classList.add('d-none');
        unionMemberSelect.required = false;
        unionMemberSelect.value = '';
      }
      updateTermOptions();
      validateAmount();
    });

    unionMemberSelect.addEventListener('change', function() {
      updateTermOptions();
      validateAmount();
    });

    function updateTermOptions() {
      const status = memberStatusSelect.value;
      loanTermSelect.innerHTML = '<option value="">Selecciona el plazo</option>';
      let maxTerm = (status === 'active') ? 12 : (status === 'inactive' ? 6 : 0);
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
      
      amountError.classList.add('d-none');
      if (!amount || amount % 500 !== 0) {
        amountError.textContent = 'El monto debe ser múltiplo de $500';
        amountError.classList.remove('d-none');
        return false;
      }
      let maxAmount = 0;
      if (status === 'active') maxAmount = loanConfig.active.maxAmount;
      else if (status === 'inactive') {
        if (unionStatus === 'union') maxAmount = loanConfig.inactive.union.maxAmount;
        if (unionStatus === 'nonunion') maxAmount = loanConfig.inactive.nonunion.maxAmount;
      }
      if (amount > maxAmount) {
        amountError.textContent = `El monto máximo permitido es $${maxAmount.toLocaleString('es-MX')}`;
        amountError.classList.remove('d-none');
        return false;
      }
      if (amount < 500) {
        amountError.textContent = 'El monto mínimo es $500';
        amountError.classList.remove('d-none');
        return false;
      }
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

      let interestRate = 0;
      if (status === 'active') interestRate = loanConfig.active.interestRate;
      else if (status === 'inactive') {
        if (unionStatus === 'union') interestRate = loanConfig.inactive.union.interestRate;
        else if (unionStatus === 'nonunion') interestRate = loanConfig.inactive.nonunion.interestRate;
      }

      const totalInterest = amount * interestRate * term;
      const totalAmount = amount + totalInterest;
      let weeklyPayment = (status === 'active' && term === 12) ? totalAmount / 52 : totalAmount / (term * 4);

      document.getElementById('displayAmount').textContent = `$${amount.toLocaleString('es-MX')}`;
      document.getElementById('displayTerm').textContent = `${term} ${term === 1 ? 'mes' : 'meses'}`;
      document.getElementById('displayRate').textContent = `${(interestRate * 100).toFixed(1)}%`;
      document.getElementById('displayInterest').textContent = `$${totalInterest.toLocaleString('es-MX', {minimumFractionDigits: 2})}`;
      document.getElementById('displayTotal').textContent = `$${totalAmount.toLocaleString('es-MX', {minimumFractionDigits: 2})}`;
      document.getElementById('displayWeeklyPayment').textContent = `$${weeklyPayment.toLocaleString('es-MX', {minimumFractionDigits: 2})}`;

      resultsDiv.classList.remove('d-none');
      warningSection.classList.remove('d-none');
      printBtn.classList.remove('d-none');
    });
  </script>
</body>
</html>
