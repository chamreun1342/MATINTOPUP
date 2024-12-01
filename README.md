<h1 class="title">MATINTOPUP</h1>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mobile Legends Top-Up</title>
  <style>
    /* General Styles */
    body {
      font-family: 'Arial', sans-serif;
      background: #0d1117; /* Dark background */
      color: #fff;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .container {
      width: 350px;
      background: #161b22;
      border-radius: 15px;
      padding: 20px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
    }

    .title {
      text-align: center;
      font-size: 24px;
      color: #00b4d8; /* Neon blue color */
      margin-bottom: 20px;
    }

    .form-group {
      margin-bottom: 15px;
    }

    .form-group label {
      display: block;
      font-size: 14px;
      color: #a1a1a1;
      margin-bottom: 5px;
    }

    .form-group input, .form-group select {
      width: 100%;
      padding: 10px;
      font-size: 14px;
      border-radius: 8px;
      border: none;
      outline: none;
      background: #21262d; /* Darker input field */
      color: #fff;
    }

    .form-group input:focus, .form-group select:focus {
      border: 2px solid #00b4d8; /* Neon focus effect */
    }

    .button {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      background: linear-gradient(45deg, #00b4d8, #0077b6);
      color: #fff;
      text-transform: uppercase;
      font-weight: bold;
      cursor: pointer;
      transition: 0.3s ease;
      box-shadow: 0 4px 15px rgba(0, 180, 216, 0.4);
    }

    .button:hover {
      background: linear-gradient(45deg, #00d4ff, #0078c8);
      box-shadow: 0 6px 20px rgba(0, 180, 216, 0.6);
    }

    .result {
      text-align: center;
      margin-top: 15px;
      color: #00ff88; /* Success message color */
    }
  </style>
</head>
<body>
  <div class="container">
      <h1 class="title">MATIN</h1>
    <h2 class="title">Top-Up Diamonds</h2>
    <form id="topupForm">
      <div class="form-group">
        <label for="gameID">Game ID</label>
        <input type="text" id="gameID" placeholder="Enter your Game ID" required>
      </div>
      <div class="form-group">
        <label for="serverID">Server ID</label>
        <input type="text" id="serverID" placeholder="Enter your Server ID" required>
      </div>
      <div class="form-group">
        <label for="package">Select Package</label>
        <select id="package" required>
          <option value="" disabled selected>Select a package</option>
          <option value="11">11 Diamonds - $0.19</option>
          <option value="22">22 Diamonds - $0.38</option>
          <option value="56">56 Diamonds - $0.89</option>
        </select>
      </div>
      <button type="button" class="button" onclick="processTopup()">Top Up</button>
    </form>
    <div id="result" class="result"></div>
  </div>

  <script>
    function processTopup() {
      const gameID = document.getElementById('gameID').value;
      const serverID = document.getElementById('serverID').value;
      const packageSelected = document.getElementById('package').value;

      if (!gameID || !serverID || !packageSelected) {
        alert("Please fill out all fields.");
        return;
      }

      // Simulate a successful top-up process
      document.getElementById('result').innerText = `Top-up successful! Game ID: ${gameID}, Server ID: ${serverID}, Package: ${packageSelected} Diamonds.`;
    }
  </script>
</body>
</html>
