<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Car Flipper – Game Automation UI</title>
  <!-- Poppins & Inter (fallback) -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:opsz@14..32&family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background: #0b0b0b;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Poppins', 'Inter', sans-serif;
    }

    /* ----- Window (draggable illusion) ----- */
    .window {
      width: 720px;
      height: 470px;
      background: rgba(22, 22, 22, 0.75);
      backdrop-filter: blur(16px) saturate(180%);
      -webkit-backdrop-filter: blur(16px) saturate(180%);
      border-radius: 20px;
      border: 1px solid rgba(59, 130, 246, 0.25);
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.8), 0 0 0 1px rgba(59, 130, 246, 0.15), 0 0 20px rgba(59, 130, 246, 0.2);
      display: flex;
      flex-direction: column;
      overflow: hidden;
      transition: box-shadow 0.2s ease;
      cursor: default;
      user-select: none;
    }

    /* ----- title bar ----- */
    .title-bar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 10px 18px;
      background: rgba(0, 0, 0, 0.2);
      border-bottom: 1px solid rgba(255, 255, 255, 0.03);
      flex-shrink: 0;
    }

    .title-left {
      display: flex;
      align-items: center;
      gap: 8px;
      font-weight: 600;
      font-size: 15px;
      color: #f0f4ff;
      letter-spacing: 0.2px;
    }
    .title-left span {
      background: linear-gradient(135deg, #3B82F6, #22C55E);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .title-controls {
      display: flex;
      gap: 12px;
      color: #a0b4d0;
      font-size: 14px;
      font-weight: 400;
    }
    .title-controls span {
      cursor: pointer;
      transition: color 0.2s;
      line-height: 1;
    }
    .title-controls span:hover {
      color: #f0f4ff;
    }
    .title-controls span:last-child:hover {
      color: #ff5f5f;
    }

    /* ----- body (sidebar + main) ----- */
    .body {
      display: flex;
      flex: 1;
      min-height: 0;
    }

    /* ----- sidebar ----- */
    .sidebar {
      width: 58px;
      background: rgba(0, 0, 0, 0.25);
      backdrop-filter: blur(4px);
      border-right: 1px solid rgba(255, 255, 255, 0.04);
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 16px 0 12px;
      gap: 12px;
      flex-shrink: 0;
    }

    .sidebar-item {
      width: 42px;
      height: 42px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      border-radius: 12px;
      color: #7a8aa8;
      transition: 0.2s ease;
      cursor: default;
      position: relative;
    }
    .sidebar-item.active {
      color: #b8d0ff;
      background: rgba(59, 130, 246, 0.15);
      box-shadow: 0 0 12px rgba(59, 130, 246, 0.3);
    }
    .sidebar-item.active::after {
      content: '';
      position: absolute;
      left: -2px;
      top: 20%;
      height: 60%;
      width: 3px;
      background: #3B82F6;
      border-radius: 4px;
      box-shadow: 0 0 10px #3B82F6;
    }
    .sidebar-item:not(.active):hover {
      color: #c0d4f0;
      background: rgba(255, 255, 255, 0.03);
    }

    /* ----- main content ----- */
    .main {
      flex: 1;
      padding: 14px 16px 10px 16px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    /* tabs */
    .tabs {
      display: flex;
      gap: 18px;
      font-size: 13px;
      font-weight: 500;
      color: #8f9fc0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.04);
      padding-bottom: 8px;
      margin-bottom: 14px;
      position: relative;
    }
    .tab-item {
      cursor: default;
      transition: 0.2s;
      padding-bottom: 6px;
      position: relative;
    }
    .tab-item.active {
      color: #d6e4ff;
    }
    .tab-item.active::after {
      content: '';
      position: absolute;
      bottom: -1px;
      left: 0;
      width: 100%;
      height: 2px;
      background: #3B82F6;
      border-radius: 4px;
      animation: tabGlow 1.4s ease-in-out infinite alternate;
    }
    @keyframes tabGlow {
      0% { box-shadow: 0 0 4px #3B82F6; opacity: 0.7; }
      100% { box-shadow: 0 0 14px #3B82F6; opacity: 1; }
    }

    /* scrollable content */
    .content {
      flex: 1;
      overflow-y: auto;
      padding-right: 4px;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }
    .content::-webkit-scrollbar {
      width: 3px;
    }
    .content::-webkit-scrollbar-track {
      background: transparent;
    }
    .content::-webkit-scrollbar-thumb {
      background: #3B82F6;
      border-radius: 10px;
    }

    /* ----- cards row (dashboard) ----- */
    .cards-grid {
      display: grid;
      grid-template-columns: 1fr 1fr 1fr 1fr;
      gap: 10px;
      margin-bottom: 6px;
    }
    .stat-card {
      background: rgba(255, 255, 255, 0.03);
      backdrop-filter: blur(4px);
      border-radius: 16px;
      padding: 12px 10px 10px;
      border: 1px solid rgba(255, 255, 255, 0.03);
      box-shadow: 0 8px 18px -6px rgba(0,0,0,0.6);
      transition: 0.2s;
      display: flex;
      flex-direction: column;
    }
    .stat-card .icon {
      font-size: 22px;
      margin-bottom: 4px;
    }
    .stat-card .label {
      font-size: 10px;
      text-transform: uppercase;
      letter-spacing: 0.4px;
      color: #7f90b0;
    }
    .stat-card .value {
      font-size: 20px;
      font-weight: 600;
      color: #f0f6ff;
      line-height: 1.3;
      background: linear-gradient(135deg, #e0ecff, #b0d0ff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }
    .stat-card.glow-blue {
      box-shadow: 0 0 12px rgba(59, 130, 246, 0.15);
    }
    .stat-card.glow-green {
      box-shadow: 0 0 12px rgba(34, 197, 94, 0.12);
    }

    /* ----- base / toggles ----- */
    .section-card {
      background: rgba(255, 255, 255, 0.02);
      backdrop-filter: blur(4px);
      border-radius: 18px;
      padding: 14px 16px;
      border: 1px solid rgba(255, 255, 255, 0.02);
      box-shadow: 0 6px 16px rgba(0,0,0,0.4);
      margin-bottom: 6px;
    }
    .section-title {
      font-size: 13px;
      font-weight: 600;
      color: #c8d8f0;
      margin-bottom: 10px;
      letter-spacing: 0.2px;
    }
    .toggle-row {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 6px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.02);
    }
    .toggle-row:last-child {
      border-bottom: none;
    }
    .toggle-row .t-icon {
      font-size: 18px;
      width: 26px;
      text-align: center;
    }
    .toggle-row .t-info {
      flex: 1;
    }
    .toggle-row .t-info .t-title {
      font-size: 13px;
      font-weight: 500;
      color: #dce6f5;
    }
    .toggle-row .t-info .t-desc {
      font-size: 10px;
      color: #7c8db0;
    }
    /* iOS toggle */
    .toggle {
      width: 38px;
      height: 22px;
      background: #2a3346;
      border-radius: 40px;
      position: relative;
      cursor: default;
      transition: 0.25s;
      flex-shrink: 0;
      box-shadow: inset 0 1px 3px rgba(0,0,0,0.5);
    }
    .toggle.active {
      background: #3B82F6;
      box-shadow: 0 0 12px #3B82F6, inset 0 1px 2px rgba(255,255,255,0.2);
    }
    .toggle.active.green {
      background: #22C55E;
      box-shadow: 0 0 12px #22C55E, inset 0 1px 2px rgba(255,255,255,0.2);
    }
    .toggle::after {
      content: '';
      width: 16px;
      height: 16px;
      background: white;
      border-radius: 50%;
      position: absolute;
      top: 3px;
      left: 3px;
      transition: 0.25s cubic-bezier(0.34, 1.2, 0.64, 1);
      box-shadow: 0 2px 6px rgba(0,0,0,0.3);
    }
    .toggle.active::after {
      left: 19px;
    }

    /* status bar */
    .status-bar {
      background: rgba(0, 0, 0, 0.3);
      border-radius: 40px;
      padding: 6px 18px;
      display: flex;
      align-items: center;
      gap: 8px;
      margin-top: 4px;
      font-size: 12px;
      border: 1px solid rgba(255, 255, 255, 0.02);
      backdrop-filter: blur(4px);
    }
    .status-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: #22C55E;
      box-shadow: 0 0 8px #22C55E;
      transition: 0.3s;
    }
    .status-dot.idle {
      background: #7a8aa8;
      box-shadow: none;
    }
    .status-dot.running {
      background: #3B82F6;
      box-shadow: 0 0 12px #3B82F6;
    }
    .status-dot.done {
      background: #22C55E;
      box-shadow: 0 0 12px #22C55E;
    }
    .status-dot.stopped {
      background: #f87171;
      box-shadow: 0 0 12px #f87171;
    }
    .status-text {
      color: #c0d0ec;
      font-weight: 400;
      letter-spacing: 0.2px;
    }

    /* logs */
    .logs-panel {
      background: rgba(0, 0, 0, 0.5);
      border-radius: 14px;
      padding: 8px 12px;
      font-family: 'Inter', monospace;
      font-size: 11px;
      color: #9bbf7a;
      max-height: 70px;
      overflow-y: auto;
      border: 1px solid rgba(34, 197, 94, 0.08);
      box-shadow: inset 0 0 20px rgba(0,0,0,0.6);
      margin-top: 4px;
    }
    .logs-panel::-webkit-scrollbar {
      width: 2px;
    }
    .logs-panel::-webkit-scrollbar-thumb {
      background: #22C55E;
      border-radius: 10px;
    }
    .log-line {
      padding: 2px 0;
      border-bottom: 1px solid rgba(255,255,255,0.02);
      color: #b0d6a0;
    }
    .log-line:last-child {
      border-bottom: none;
    }

    /* settings extras */
    .settings-row {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: 10px 16px;
      padding: 6px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.02);
    }
    .settings-row label {
      color: #b0c4e0;
      font-size: 12px;
      font-weight: 400;
      display: flex;
      align-items: center;
      gap: 6px;
    }
    .settings-row input[type="range"] {
      width: 100px;
      accent-color: #3B82F6;
      background: transparent;
    }
    .settings-row .val {
      color: #d0e0ff;
      font-weight: 500;
      font-size: 12px;
      min-width: 30px;
    }
    .btn-group {
      display: flex;
      gap: 8px;
      margin-top: 6px;
    }
    .btn {
      background: rgba(59, 130, 246, 0.12);
      border: 1px solid rgba(59, 130, 246, 0.15);
      color: #c0d4fc;
      padding: 4px 16px;
      border-radius: 30px;
      font-size: 11px;
      font-weight: 500;
      transition: 0.2s;
      cursor: default;
    }
    .btn:hover {
      background: rgba(59, 130, 246, 0.2);
      border-color: #3B82F6;
      color: white;
    }
    .btn.green {
      background: rgba(34, 197, 94, 0.12);
      border-color: rgba(34, 197, 94, 0.2);
      color: #a0d8a0;
    }

    /* helpers */
    .flex-col { display: flex; flex-direction: column; }
    .gap-1 { gap: 6px; }
    .mt-1 { margin-top: 4px; }

    /* scrollable content inner spacing */
    .content > div:last-child {
      margin-bottom: 2px;
    }

    /* responsive small adjustments */
    @media (max-width: 740px) {
      .window { width: 96vw; height: 90vh; max-height: 500px; }
    }
  </style>
</head>
<body>

<div class="window">
  <!-- title bar -->
  <div class="title-bar">
    <div class="title-left">🚗 <span>Car Flipper</span></div>
    <div class="title-controls">
      <span>🗕</span> <span>🗖</span> <span>✕</span>
    </div>
  </div>

  <div class="body">
    <!-- sidebar -->
    <div class="sidebar">
      <div class="sidebar-item active">🏠</div>
      <div class="sidebar-item">🚗</div>
      <div class="sidebar-item">🏢</div>
      <div class="sidebar-item">📦</div>
      <div class="sidebar-item">🎁</div>
      <div class="sidebar-item">⚙</div>
      <div class="sidebar-item">📄</div>
    </div>

    <!-- main -->
    <div class="main">
      <!-- tabs -->
      <div class="tabs">
        <div class="tab-item active">Auto</div>
        <div class="tab-item">Run Once</div>
      </div>

      <!-- content -->
      <div class="content">

        <!-- dashboard cards -->
        <div class="cards-grid">
          <div class="stat-card glow-blue"><div class="icon">💰</div><div class="label">Money</div><div class="value">$5,420,000</div></div>
          <div class="stat-card glow-green"><div class="icon">🧩</div><div class="label">Parts</div><div class="value">1,245</div></div>
          <div class="stat-card glow-blue"><div class="icon">🚘</div><div class="label">Cars Sold</div><div class="value">482</div></div>
          <div class="stat-card glow-green"><div class="icon">⏱️</div><div class="label">Runtime</div><div class="value">02:14:35</div></div>
        </div>

        <!-- Base section -->
        <div class="section-card">
          <div class="section-title">🏢 Base</div>
          <div class="toggle-row"><span class="t-icon">💰</span><div class="t-info"><div class="t-title">Claim Cash</div><div class="t-desc">Collect daily cash</div></div><div class="toggle active green"></div></div>
          <div class="toggle-row"><span class="t-icon">🧩</span><div class="t-info"><div class="t-title">Claim Parts</div><div class="t-desc">Collect parts</div></div><div class="toggle active"></div></div>
          <div class="toggle-row"><span class="t-icon">⬆️</span><div class="t-info"><div class="t-title">Buy Upgrades</div><div class="t-desc">Purchase upgrades</div></div><div class="toggle"></div></div>
          <div class="toggle-row"><span class="t-icon">📦</span><div class="t-info"><div class="t-title">Claim Containers</div><div class="t-desc">Open containers</div></div><div class="toggle active green"></div></div>
          <div class="toggle-row"><span class="t-icon">🎁</span><div class="t-info"><div class="t-title">Open Containers</div><div class="t-desc">Reveal rewards</div></div><div class="toggle"></div></div>
          <div class="toggle-row"><span class="t-icon">📋</span><div class="t-info"><div class="t-title">Claim Quests</div><div class="t-desc">Quest rewards</div></div><div class="toggle active"></div></div>
          <div class="toggle-row"><span class="t-icon">🗑️</span><div class="t-info"><div class="t-title">Deliver Junk</div><div class="t-desc">Sell junk items</div></div><div class="toggle"></div></div>
        </div>

        <!-- Cars section -->
        <div class="section-card">
          <div class="section-title">🚗 Cars</div>
          <div class="toggle-row"><span class="t-icon">🔧</span><div class="t-info"><div class="t-title">Fix Cars</div><div class="t-desc">Repair vehicles</div></div><div class="toggle active"></div></div>
          <div class="toggle-row"><span class="t-icon">💲</span><div class="t-info"><div class="t-title">Sell Fixed Cars</div><div class="t-desc">Sell repaired</div></div><div class="toggle active green"></div></div>
          <div class="toggle-row"><span class="t-icon">🔄</span><div class="t-info"><div class="t-title">Update Stands</div><div class="t-desc">Refresh display</div></div><div class="toggle"></div></div>
          <div class="toggle-row"><span class="t-icon">🏷️</span><div class="t-info"><div class="t-title">Buy, Fix & Sell Cheapest</div><div class="t-desc">Auto flip cheapest</div></div><div class="toggle active"></div></div>
        </div>

        <!-- Rewards -->
        <div class="section-card">
          <div class="section-title">🎁 Rewards</div>
          <div class="toggle-row"><span class="t-icon">⏳</span><div class="t-info"><div class="t-title">Claim Playtime Rewards</div><div class="t-desc">Time-based rewards</div></div><div class="toggle active green"></div></div>
          <div class="toggle-row"><span class="t-icon">📆</span><div class="t-info"><div class="t-title">Claim Daily Rewards</div><div class="t-desc">Daily login</div></div><div class="toggle active"></div></div>
        </div>

        <!-- Settings (condensed) -->
        <div class="section-card">
          <div class="section-title">⚙ Settings</div>
          <div class="settings-row">
            <label>Delay <span class="val">1.5s</span></label>
            <input type="range" min="0.5" max="30" step="0.5" value="1.5">
            <label><span style="opacity:0.7;">⚡</span> Auto Reconnect</label>
            <div class="toggle active" style="width:32px; height:18px;"></div>
            <label><span style="opacity:0.7;">🛡️</span> Anti AFK</label>
            <div class="toggle active green" style="width:32px; height:18px;"></div>
          </div>
          <div class="settings-row">
            <label>💾 Auto Save</label>
            <div class="toggle active" style="width:32px; height:18px;"></div>
            <label>🔔 Notifications</label>
            <div class="toggle" style="width:32px; height:18px;"></div>
            <label>🎮 FPS Limit</label>
            <div class="toggle active green" style="width:32px; height:18px;"></div>
          </div>
          <div class="btn-group">
            <span class="btn">Save Config</span>
            <span class="btn">Load Config</span>
            <span class="btn" style="border-color:rgba(255,80,80,0.2); color:#f0a0a0;">Reset</span>
          </div>
        </div>

        <!-- Logs panel -->
        <div class="logs-panel">
          <div class="log-line">12:41 Claimed Cash</div>
          <div class="log-line">12:43 Bought Upgrade</div>
          <div class="log-line">12:45 Sold Vehicle</div>
          <div class="log-line">12:47 Delivered Junk</div>
        </div>

        <!-- Status bar -->
        <div class="status-bar">
          <span class="status-dot running"></span>
          <span class="status-text">● Running BuyFixAndSellCar</span>
          <span style="margin-left:auto; color:#6a7a9a; font-size:10px;">🟢 online</span>
        </div>

      </div> <!-- end content -->
    </div> <!-- end main -->
  </div> <!-- end body -->
</div> <!-- end window -->

</body>
</html>
