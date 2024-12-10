<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dumb Stocks</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
      background-color: #d3d3d3; /* Grey background */
      color: black; /* Black text */
    }

    header {
      background-color: #444;
      color: white;
      padding: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    #currencyDisplay {
      font-size: 18px;
      margin-right: 20px;
    }

    #graph-container {
      width: 80%;
      margin: 20px auto;
      background: #555;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
      border-radius: 8px;
      padding: 20px;
    }

    footer {
      background-color: #222;
      color: #ddd;
      padding: 10px;
      position: fixed;
      bottom: 0;
      width: 100%;
    }

    input[type="number"], input[type="password"], button {
      padding: 10px;
      font-size: 16px;
      margin: 5px;
      border-radius: 5px;
      border: none;
    }

    button {
      background-color: #444;
      color: white;
      cursor: pointer;
    }

    button:hover {
      background-color: #333;
    }

    label {
      font-weight: bold;
      display: block;
      margin-top: 10px;
    }

    #valueDisplay {
      font-size: 18px;
      margin-top: 20px;
    }

    #statusDisplay {
      margin-top: 5px;
      font-weight: bold;
      padding: 5px;
      display: inline-block;
    }

    #statusDisplay.up {
      background-color: #2e7d32; /* Green */
    }

    #statusDisplay.down {
      background-color: #e53935; /* Red */
    }
  </style>
</head>
<body>
  <header>
    <h1>Dumb Stocks</h1>
    <div id="currencyDisplay">Currency: $50</div>
  </header>

  <div>
    <label for="passcodeInput">Enter Passcode:</label>
    <input type="password" id="passcodeInput" placeholder="Enter passcode">
    <button onclick="checkPasscode()">Submit</button>
  </div>

  <div id="controls" style="display: none;">
    <div>
      <button onclick="closePanel()">Close Panel</button>
    </div>
    <div>
      <label for="graphName">Graph Name:</label>
      <input type="text" id="graphName" placeholder="Enter graph name">
      <button onclick="updateGraphName()">Set Name</button>
    </div>
    <div>
      <label for="refreshInterval">Graph Refresh Interval (ms):</label>
      <input type="number" id="refreshInterval" placeholder="Set interval in ms">
      <button onclick="updateRefreshInterval()">Set Interval</button>
    </div>
    <div>
      <label for="randomnessRange">Randomness Range (±):</label>
      <input type="number" id="randomnessRange" placeholder="Set randomness range">
      <button onclick="updateRandomness()">Set Range</button>
    </div>
    <div>
      <label for="setValue">Set Graph Value:</label>
      <input type="number" id="setValue" placeholder="Enter value">
      <button onclick="setGraphValue()">Set Value</button>
    </div>
  </div>

  <div id="graph-container">
    <canvas id="lineChart"></canvas>
    <div id="valueDisplay">
      Current Value: <span id="currentValue">50</span>
      <div id="statusDisplay" class="up">Status: Up</div>
    </div>
    <div>
      <button onclick="deposit()">Deposit</button>
      <input type="number" id="depositAmount" placeholder="Enter deposit amount">
      <button onclick="withdraw()">Withdraw</button>
    </div>
  </div>

  <footer>
    <p>Thank you for visiting our Dumb Stocks Tracker!</p>
  </footer>

  <script>
    let value = 50; // Starting value
    let peakValue = value;
    let currency = 50; // Starting currency
    let intervalId;
    let refreshInterval = 2000; // Default refresh interval (2 seconds)
    let randomnessRange = 10; // Default randomness range (±10)
    const passcode = "12345"; // Correct passcode

    const currentValueElement = document.getElementById('currentValue');
    const currencyDisplayElement = document.getElementById('currencyDisplay');
    const statusDisplayElement = document.getElementById('statusDisplay');

    function updateDisplay() {
      currentValueElement.textContent = value;
      currencyDisplayElement.textContent = `Currency: $${currency}`;
      if (value < peakValue) {
        statusDisplayElement.textContent = `Status: Down`;
        statusDisplayElement.className = "down";
      } else {
        statusDisplayElement.textContent = `Status: Up`;
        statusDisplayElement.className = "up";
      }
    }

    function updateChart() {
      const now = new Date().toLocaleTimeString();
      const randomChange = Math.floor(Math.random() * randomnessRange * 2 - randomnessRange);
      value += randomChange;

      if (value > peakValue) peakValue = value;
      updateDisplay();
      addDataPoint(now, value);
    }

    function addDataPoint(label, newValue) {
      data.labels.push(label);
      data.datasets[0].data.push(newValue);
      if (data.labels.length > 10) {
        data.labels.shift();
        data.datasets[0].data.shift();
      }
      lineChart.update();
    }

    function checkPasscode() {
      const inputPasscode = document.getElementById('passcodeInput').value;
      if (inputPasscode === passcode) {
        document.getElementById('controls').style.display = "block";
        alert("Control panel unlocked!");
      } else {
        alert("Incorrect passcode!");
      }
    }

    function closePanel() {
      document.getElementById('controls').style.display = "none";
    }

    function updateGraphName() {
      const newName = document.getElementById('graphName').value;
      if (newName) {
        data.datasets[0].label = newName;
        lineChart.update();
      }
    }

    function updateRefreshInterval() {
      const newInterval = parseInt(document.getElementById('refreshInterval').value);
      if (newInterval > 0) {
        clearInterval(intervalId);
        refreshInterval = newInterval;
        intervalId = setInterval(updateChart, refreshInterval);
        alert(`Graph refresh interval set to ${newInterval}ms.`);
      }
    }

    function updateRandomness() {
      const newRange = parseInt(document.getElementById('randomnessRange').value);
      if (newRange > 0) {
        randomnessRange = newRange;
        alert(`Randomness range set to ±${newRange}.`);
      }
    }

    function setGraphValue() {
      const newValue = parseInt(document.getElementById('setValue').value);
      if (!isNaN(newValue)) {
        value = newValue;
        if (value > peakValue) peakValue = value;
        updateDisplay();
        alert(`Graph value set to ${newValue}.`);
      }
    }

    function deposit() {
      const amount = parseInt(document.getElementById('depositAmount').value);
      if (!isNaN(amount) && amount > 0) {
        currency -= amount;
        peakValue = value;
        updateDisplay();
        alert(`Deposited $${amount}.`);
      } else {
        alert("Invalid deposit amount.");
      }
    }

    function withdraw() {
      const payout = value;
      currency += payout;
      updateDisplay();
      alert(`Withdrew $${payout}.`);
    }

    const data = {
      labels: [],
      datasets: [{
        label: 'Value Over Time',
        data: [],
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.4
      }]
    };

    const config = {
      type: 'line',
      data: data,
      options: {
        responsive: true,
        plugins: {
          legend: {
            display: true
          }
        },
        scales: {
          x: {
            title: {
              display: true,
              text: 'Time'
            }
          },
          y: {
            title: {
              display: true,
              text: 'Value'
            }
          }
        }
      }
    };

    const ctx = document.getElementById('lineChart').getContext('2d');
    const lineChart = new Chart(ctx, config);

    intervalId = setInterval(updateChart, refreshInterval);
  </script>
</body>
</html>
