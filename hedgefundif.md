<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Copin IF — Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#07090f;--s1:#0c0f1a;--s2:#111520;--s3:#161c2d;
  --bdr:#1c2438;--bdr2:#243048;
  --acc:#4f8eff;--acc2:#00d4aa;--gold:#ffb84d;--rose:#ff5f87;
  --grn:#2dd4a0;--red:#ff4d6d;--pur:#9d7eff;--sky:#38bdf8;--ora:#fb923c;
  --txt:#dde4f0;--sub:#6b7fa3;--dim:#3d4f6e;
  --mono:'JetBrains Mono',monospace;--sans:'Space Grotesk',sans-serif;
}
*{margin:0;padding:0;box-sizing:border-box}
body{background:var(--bg);color:var(--txt);font-family:var(--sans);min-height:100vh;overflow-x:hidden}
  #dash{display:none;flex-direction:column;min-height:100vh}

/* UPLOAD */
#upload-screen{min-height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:36px;padding:40px;
  background:radial-gradient(ellipse 800px 500px at 15% 10%,rgba(79,142,255,.07) 0%,transparent 70%),
             radial-gradient(ellipse 600px 400px at 90% 80%,rgba(0,212,170,.06) 0%,transparent 65%),var(--bg)}
.brand{display:flex;flex-direction:column;align-items:center;gap:10px}
.brand-eye{font-family:var(--mono);font-size:10px;letter-spacing:5px;color:var(--acc);text-transform:uppercase}
.brand-name{font-size:64px;font-weight:700;letter-spacing:-3px;line-height:1;
  background:linear-gradient(135deg,#dde4f0 0%,var(--acc) 50%,var(--acc2) 100%);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
.brand-desc{color:var(--sub);font-size:14px}
.drop-zone{width:100%;max-width:480px;border:1.5px dashed var(--bdr2);border-radius:20px;
  padding:52px 36px;text-align:center;cursor:pointer;position:relative;
  background:linear-gradient(135deg,var(--s1),var(--s2));transition:all .3s}
.drop-zone:hover,.drop-zone.over{border-color:var(--acc);background:linear-gradient(135deg,#0a1020,#0f1830);
  box-shadow:0 0 60px rgba(79,142,255,.12)}
.drop-icon{font-size:52px;margin-bottom:20px;display:block}
.drop-zone h3{font-size:18px;font-weight:600;margin-bottom:8px}
.drop-zone p{color:var(--sub);font-size:13px;font-family:var(--mono)}
.drop-zone input{position:absolute;inset:0;opacity:0;cursor:pointer;width:100%}
.upload-pills{display:flex;gap:20px;flex-wrap:wrap;justify-content:center}
.pill{display:flex;align-items:center;gap:7px;font-family:var(--mono);font-size:10px;color:var(--sub)}
.pill-dot{width:5px;height:5px;border-radius:50%}

/* HEADER */
.header{height:56px;background:rgba(12,15,26,.95);backdrop-filter:blur(20px);
  border-bottom:1px solid var(--bdr);padding:0 28px;display:flex;align-items:center;gap:16px;
  position:sticky;top:0;z-index:100}
.h-logo{font-size:17px;font-weight:700;letter-spacing:-.3px}
.h-logo em{color:var(--acc);font-style:normal}
.h-sep{width:1px;height:20px;background:var(--bdr)}
.h-file{font-family:var(--mono);font-size:10px;color:var(--sub);background:var(--s3);
  padding:3px 10px;border-radius:6px;border:1px solid var(--bdr);max-width:280px;
  overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.h-right{margin-left:auto;display:flex;align-items:center;gap:10px}
.month-sel{background:var(--s2);border:1px solid var(--bdr);color:var(--txt);
  font-family:var(--sans);font-size:12px;padding:5px 10px;border-radius:8px;
  cursor:pointer;outline:none;min-width:110px}
.month-sel:hover{border-color:var(--acc)}
.btn-sm{background:transparent;border:1px solid var(--bdr);color:var(--sub);
  font-family:var(--sans);font-size:11px;padding:5px 12px;border-radius:8px;cursor:pointer;transition:all .2s}
.btn-sm:hover{border-color:var(--acc);color:var(--txt)}
.btn-exp{background:rgba(79,142,255,.12);border-color:rgba(79,142,255,.3);color:var(--acc)}
.btn-excel{background:rgba(45,212,160,.12);border-color:rgba(45,212,160,.3);color:var(--grn)}
.btn-excel:hover{background:rgba(45,212,160,.2)}
.btn-pdf{background:rgba(255,77,109,.12);border-color:rgba(255,77,109,.3);color:var(--red)}
.btn-pdf:hover{background:rgba(255,77,109,.2)}

/* TABS */
.tabs{display:flex;padding:0 28px;background:rgba(12,15,26,.9);
  border-bottom:1px solid var(--bdr);overflow-x:auto;gap:2px}
.tab{padding:11px 18px;font-size:12px;font-weight:600;cursor:pointer;
  border-bottom:2px solid transparent;color:var(--sub);transition:all .2s;white-space:nowrap}
.tab:hover{color:var(--txt)}
.tab.active{color:var(--acc);border-bottom-color:var(--acc)}
.tab-group-sep{width:1px;background:var(--bdr);margin:8px 6px;flex-shrink:0}


/* Live tab pulse dot */
.tab[data-tab="live"] { position: relative; }
.tab[data-tab="live"]::before {
  content: '';
  display: inline-block;
  width: 6px; height: 6px;
  background: var(--red);
  border-radius: 50%;
  margin-right: 5px;
  animation: pulse-dot 2s ease-in-out infinite;
  vertical-align: middle;
}
@keyframes pulse-dot {
  0%,100% { opacity:1; transform:scale(1); }
  50%      { opacity:.4; transform:scale(.7); }
}

/* CONTENT */
.content{padding:24px 28px}
.panel{display:none}.panel.active{display:block}

/* KPI */
.kpi-row{display:grid;grid-template-columns:repeat(auto-fill,minmax(175px,1fr));gap:14px;margin-bottom:24px}
.kpi{background:var(--s1);border:1px solid var(--bdr);border-radius:12px;
  padding:18px 20px;position:relative;overflow:hidden;transition:border-color .2s,transform .15s}
.kpi:hover{border-color:var(--bdr2);transform:translateY(-1px)}
.kpi-bar{position:absolute;top:0;left:0;right:0;height:2px}
.kpi-lbl{font-family:var(--mono);font-size:9px;letter-spacing:2px;color:var(--sub);text-transform:uppercase;margin-bottom:10px}
.kpi-val{font-size:22px;font-weight:700;letter-spacing:-1px;line-height:1.1}
.kpi-val.pos{color:var(--grn)}.kpi-val.neg{color:var(--red)}
.kpi-foot{font-size:10px;color:var(--sub);margin-top:6px;font-family:var(--mono)}
.kpi-chg{font-size:11px;margin-top:5px;display:inline-flex;align-items:center;gap:3px}
.kpi-chg.up{color:var(--grn)}.kpi-chg.dn{color:var(--red)}

/* CHART */
.g2{display:grid;grid-template-columns:1fr 1fr;gap:18px;margin-bottom:22px}
.g3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:18px;margin-bottom:22px}
.g4{display:grid;grid-template-columns:1fr 1fr 1fr 1fr;gap:14px;margin-bottom:22px}
.span2{grid-column:span 2}.span3{grid-column:span 3}
.card{background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:18px}
.card-hd{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:14px}
.card-title{font-size:12px;font-weight:600}
.card-sub{font-size:10px;color:var(--sub);font-family:var(--mono);margin-top:2px}
.cw{position:relative}
.h180{height:180px}.h220{height:220px}.h260{height:260px}.h300{height:300px}.h340{height:340px}

/* RISK SPECIFIC */
.risk-metric-grid{display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:22px}
.rm-card{background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 20px}
.rm-title{font-size:11px;font-weight:600;color:var(--sub);font-family:var(--mono);
  letter-spacing:1px;text-transform:uppercase;margin-bottom:14px;
  padding-bottom:10px;border-bottom:1px solid var(--bdr)}
.rm-row{display:flex;justify-content:space-between;align-items:center;
  padding:7px 0;border-bottom:1px solid rgba(28,36,56,.5)}
.rm-row:last-child{border-bottom:none}
.rm-label{font-size:12px;color:var(--sub)}
.rm-fund{font-family:var(--mono);font-size:13px;font-weight:600}
.rm-btc{font-family:var(--mono);font-size:12px;color:var(--sub)}
.rm-fund.pos{color:var(--grn)}.rm-fund.neg{color:var(--red)}
.rm-fund.warn{color:var(--gold)}
.compare-bar{display:flex;align-items:center;gap:8px;margin-top:4px}
.cb-label{font-size:10px;font-family:var(--mono);color:var(--sub);width:40px}
.cb-track{flex:1;height:6px;background:var(--s3);border-radius:3px;overflow:hidden}
.cb-fill{height:100%;border-radius:3px}
.cb-val{font-size:10px;font-family:var(--mono);width:55px;text-align:right}

/* DD GAUGE */
.dd-gauge{display:flex;flex-direction:column;align-items:center;gap:8px;padding:12px 0}
.dd-val{font-size:36px;font-weight:700;letter-spacing:-2px}
.dd-label{font-size:10px;font-family:var(--mono);color:var(--sub);letter-spacing:1px}
.dd-bar-wrap{width:100%;height:8px;background:var(--s3);border-radius:4px;overflow:hidden}
.dd-bar-fill{height:100%;border-radius:4px;transition:width .6s ease}

/* TABLE */
.tbl-wrap{background:var(--s1);border:1px solid var(--bdr);border-radius:12px;overflow:hidden;margin-bottom:22px}
.tbl-hd{display:flex;align-items:center;justify-content:space-between;padding:14px 18px;border-bottom:1px solid var(--bdr)}
.tbl-title{font-size:12px;font-weight:600}
.srch{background:var(--s3);border:1px solid var(--bdr);color:var(--txt);
  font-family:var(--mono);font-size:11px;padding:5px 10px;border-radius:7px;
  outline:none;width:180px;transition:border-color .2s}
.srch:focus{border-color:var(--acc)}
.tbl-scroll{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:12px}
thead th{padding:9px 14px;text-align:left;white-space:nowrap;
  font-family:var(--mono);font-size:9px;letter-spacing:1.5px;
  color:var(--sub);text-transform:uppercase;background:var(--s2);
  border-bottom:1px solid var(--bdr);cursor:pointer;user-select:none}
thead th:hover{color:var(--txt)}
tbody td{padding:9px 14px;border-bottom:1px solid rgba(28,36,56,.7)}
tbody tr:last-child td{border-bottom:none}
tbody tr:hover td{background:rgba(79,142,255,.04)}
td.r{text-align:right;font-family:var(--mono)}
td.pos{color:var(--grn);font-family:var(--mono);text-align:right}
td.neg{color:var(--red);font-family:var(--mono);text-align:right}
td.dim{color:var(--sub);font-family:var(--mono);text-align:right}
.tbl-foot{padding:10px 14px;border-top:1px solid var(--bdr);background:var(--s2);
  display:flex;gap:20px;font-family:var(--mono);font-size:10px;color:var(--sub);flex-wrap:wrap}
.tbl-foot strong{color:var(--txt)}

/* PROGRESS */
.prog-list{display:flex;flex-direction:column;gap:10px}
.prog-item{display:flex;flex-direction:column;gap:4px}
.prog-top{display:flex;justify-content:space-between}
.prog-label{font-size:11px;font-weight:500}
.prog-val{font-family:var(--mono);font-size:11px;color:var(--sub)}
.prog-bar{height:5px;background:var(--s3);border-radius:3px;overflow:hidden}
.prog-fill{height:100%;border-radius:3px;transition:width .6s}

/* BADGES */
.badge{display:inline-block;padding:2px 7px;border-radius:5px;font-size:10px;
  font-family:var(--mono);font-weight:600;letter-spacing:.3px;white-space:nowrap}
.b-hl{background:rgba(157,126,255,.15);color:#b8a3ff}
.b-bin{background:rgba(255,184,77,.12);color:#ffc966}
.b-bybit{background:rgba(255,95,135,.15);color:#ff7da0}
.b-okx{background:rgba(221,228,240,.1);color:#b0bcd4}
.b-ltr{background:rgba(56,189,248,.14);color:#7dd3f8}
.b-dcbl{background:rgba(45,212,160,.14);color:#5dddb7}
.b-mex{background:rgba(79,142,255,.14);color:#7aaeff}
.b-poly{background:rgba(236,72,153,.15);color:#f472b6}
.b-oth{background:rgba(107,127,163,.13);color:#8fa3c2}
.chip{display:inline-flex;align-items:center;gap:5px;padding:2px 9px;border-radius:20px;font-size:11px;font-weight:500}
.chip-dot{width:5px;height:5px;border-radius:50%;flex-shrink:0}
.c-tung{background:rgba(255,184,77,.1);color:#ffcf7d}
.c-viet{background:rgba(79,142,255,.1);color:#7aaeff}
.c-nam{background:rgba(45,212,160,.1);color:#5dddb7}
.c-vufm{background:rgba(157,126,255,.1);color:#b8a3ff}
.c-oth{background:rgba(107,127,163,.1);color:#8fa3c2}

/* SEC LABEL */
.sec{font-family:var(--mono);font-size:9px;letter-spacing:3px;color:var(--sub);
  text-transform:uppercase;margin-bottom:16px;display:flex;align-items:center;gap:10px}
.sec::after{content:'';flex:1;height:1px;background:var(--bdr)}

/* LOADING */
#loading{display:none;position:fixed;inset:0;background:rgba(7,9,15,.92);z-index:300;
  flex-direction:column;align-items:center;justify-content:center;gap:14px}
.spin{width:36px;height:36px;border:2.5px solid var(--bdr);border-top-color:var(--acc);
  border-radius:50%;animation:spin .7s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}

/* TOOLTIP helper */
.tip{position:relative;display:inline-flex;align-items:center;cursor:help}
.tip::after{content:attr(data-tip);position:absolute;bottom:calc(100% + 6px);left:50%;
  transform:translateX(-50%);background:#0c0f1a;border:1px solid var(--bdr);
  color:var(--txt);font-size:10px;font-family:var(--mono);padding:5px 9px;
  border-radius:6px;white-space:nowrap;opacity:0;pointer-events:none;transition:opacity .2s;z-index:50}
.tip:hover::after{opacity:1}


/* FUND OVERVIEW */
.fo-table-wrap{background:var(--s1);border:1px solid var(--bdr);border-radius:12px;overflow:hidden;margin-bottom:22px}
.fo-scroll{overflow-x:auto}
.fo-table{width:100%;border-collapse:collapse;font-size:12px;min-width:900px}
.fo-table thead th{padding:8px 12px;font-family:var(--mono);font-size:9px;letter-spacing:1.2px;
  color:var(--sub);text-transform:uppercase;background:var(--s2);border-bottom:1px solid var(--bdr);
  text-align:right;white-space:nowrap}
.fo-table thead th:first-child{text-align:left;position:sticky;left:0;background:var(--s2);z-index:2}
.fo-table tbody td{padding:7px 12px;border-bottom:1px solid rgba(28,36,56,.5);text-align:right;
  font-family:var(--mono);font-size:11px;white-space:nowrap}
.fo-table tbody td:first-child{text-align:left;font-family:var(--sans);position:sticky;left:0;
  background:var(--s1);z-index:1}
.fo-table tbody tr:hover td{background:rgba(79,142,255,.04)}
.fo-table tbody tr:hover td:first-child{background:rgba(79,142,255,.06)}
.fo-row-section > td:first-child{font-weight:700;font-size:12px;color:var(--txt)}
.fo-row-section td{background:var(--s2)!important;border-bottom:1px solid var(--bdr)!important}
.fo-row-sub > td:first-child{color:var(--sub);padding-left:28px!important;font-size:11px}
.fo-row-total > td{background:rgba(79,142,255,.07)!important;font-weight:700;
  border-top:1px solid var(--acc)!important;border-bottom:1px solid var(--acc)!important}
.fo-row-total > td:first-child{color:var(--acc);font-size:13px}
.fo-row-profit > td{background:rgba(45,212,160,.06)!important;font-weight:600}
.fo-row-profit > td:first-child{color:var(--grn)}
.fo-row-loan > td:first-child{color:var(--rose)}
.fo-row-fee > td:first-child{color:var(--gold)}
.fo-row-equity > td:first-child{color:var(--sky)}
.fo-cell-pos{color:var(--grn)!important}
.fo-cell-neg{color:var(--red)!important}
.fo-cell-highlight{color:var(--acc)!important;font-weight:600}
.fo-th-change{background:rgba(79,142,255,.08)!important;color:var(--acc)!important}
.fo-td-change{background:rgba(79,142,255,.04)!important}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:var(--bg)}
::-webkit-scrollbar-thumb{background:var(--bdr2);border-radius:3px}
@media(max-width:900px){.g2,.g3,.g4{grid-template-columns:1fr}.span2,.span3{grid-column:span 1}.kpi-row{grid-template-columns:repeat(2,1fr)}.risk-metric-grid{grid-template-columns:1fr}}
</style>
</head>
<body>

<div id="loading"><div class="spin"></div>
  <p style="font-family:var(--mono);font-size:11px;color:var(--sub)">Đang phân tích file...</p></div>

<!-- UPLOAD -->
<div id="upload-screen">
  <div class="brand">
    <div class="brand-eye">Fund Analytics Dashboard</div>
    <div class="brand-name">COPIN IF</div>
    <div class="brand-desc">Trading · NAV · Risk Management · Cashflow</div>
  </div>
  <div class="drop-zone" id="dz">
    <span class="drop-icon">📊</span>
    <h3>Upload file báo cáo tháng</h3>
    <p style="margin-top:6px">Monthly_Copin_IF_Report_2026.xlsx</p>
    <p style="margin-top:10px;font-size:11px">Kéo thả hoặc click để chọn</p>
    <input type="file" id="fi" accept=".xlsx,.xls">
  </div>
  <div style="display:flex;flex-direction:column;align-items:center;gap:12px;width:100%;max-width:480px">
    <button onclick="loadEmbeddedData()" style="width:100%;padding:16px;border-radius:14px;
      background:linear-gradient(135deg,rgba(79,142,255,.2),rgba(0,212,170,.15));
      border:1.5px solid rgba(79,142,255,.5);color:#dde4f0;font-family:var(--sans);
      font-size:16px;font-weight:600;cursor:pointer;transition:all .25s;letter-spacing:-.3px"
      onmouseover="this.style.background='linear-gradient(135deg,rgba(79,142,255,.35),rgba(0,212,170,.25))'"
      onmouseout="this.style.background='linear-gradient(135deg,rgba(79,142,255,.2),rgba(0,212,170,.15))'">
      ▶ Xem báo cáo Feb 2026 ngay
    </button>
    <div style="display:flex;align-items:center;gap:10px;width:100%;color:var(--dim);font-size:11px">
      <div style="flex:1;height:1px;background:var(--bdr)"></div>
      <span style="font-family:var(--mono)">hoặc upload file mới</span>
      <div style="flex:1;height:1px;background:var(--bdr)"></div>
    </div>
  </div>
  <div class="upload-pills">
    <div class="pill"><div class="pill-dot" style="background:var(--acc)"></div>Xử lý 100% trên trình duyệt</div>
    <div class="pill"><div class="pill-dot" style="background:var(--grn)"></div>Không upload lên server</div>
    <div class="pill"><div class="pill-dot" style="background:var(--gold)"></div>Hỗ trợ cả file Draft &amp; Production</div>
  </div>
</div>

<!-- DASHBOARD -->
<div id="dash" style="display:none">
  <div class="header">
    <div class="h-logo">COPIN <em>IF</em></div>
    <div class="h-sep"></div>
    <div class="h-file" id="fname">report.xlsx</div>
    <div class="h-right">
      <select class="month-sel" id="msel" onchange="onM()"></select>
      <button class="btn-sm btn-exp" onclick="exportCSV()">↓ CSV</button>
      <button class="btn-sm btn-excel" onclick="exportExcel()">↓ Excel</button>
      <button class="btn-sm btn-pdf" onclick="exportPDF()">↓ PDF</button>
      <button class="btn-sm" onclick="resetDash()">↩ Đổi file</button>
    </div>
  </div>
  <div class="tabs">
    <div class="tab active" data-tab="overview">Overview</div>
    <div class="tab" data-tab="performance">Performance</div>
    <div class="tab" data-tab="risk">Risk</div>
    <div class="tab" data-tab="portfolio">Portfolio</div>
    <div class="tab" data-tab="volume">Volume</div>
    <div class="tab" data-tab="live">🔴 Live Data</div>
  </div>
  <div class="content">
    <div class="panel active" id="p-overview"></div>

    <div class="panel" id="p-performance">
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">NAV History</div><div class="card-sub">Total fund value since inception</div></div></div><div class="cw h260"><canvas id="c-pf-nav"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Monthly Returns — Fund vs BTC</div><div class="card-sub">% return per month</div></div></div><div class="cw h260"><canvas id="c-pf-ret"></canvas></div></div>
      </div>
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Drawdown</div><div class="card-sub">Fund vs BTC from peak</div></div></div><div class="cw h240"><canvas id="c-pf-dd"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Cumulative Return</div><div class="card-sub">Indexed to 1.0 at inception</div></div></div><div class="cw h240"><canvas id="c-pf-cum"></canvas></div></div>
      </div>
      <div class="g2">
        <div class="card"><div class="card-hd"><div><div class="card-title">Strategy Allocation</div><div class="card-sub">Hedge vs Algo vs Asset over time</div></div></div><div class="cw h240"><canvas id="c-pf-strat"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Trader Allocation</div><div class="card-sub" id="pf-trader-sub">Balance by trader</div></div></div><div class="cw h240"><canvas id="c-pf-trader"></canvas></div></div>
      </div>
      <!-- Trader Performance Charts -->
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Trader Balance Over Time</div><div class="card-sub">AUM per trader per month</div></div></div><div class="cw h280"><canvas id="c-tr-bal"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Trader Monthly Return %</div><div class="card-sub">MoM % change per trader</div></div></div><div class="cw h280"><canvas id="c-tr-ret"></canvas></div></div>
      </div>
      <div class="card" style="margin-bottom:18px">
        <div class="card-hd"><div><div class="card-title">Trader Cumulative Return</div><div class="card-sub">Indexed to 1.0 at first month</div></div></div>
        <div class="cw h260"><canvas id="c-tr-cum"></canvas></div>
      </div>
    </div>

    <div class="panel" id="p-risk">
      <div class="kpi-row" id="risk-kpis"></div>
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Risk Metrics — Fund vs BTC</div></div></div><div id="risk-table-wrap" style="padding:0 18px 18px"></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Comparison Bars</div><div class="card-sub">Fund (blue) vs BTC (gold)</div></div></div><div id="risk-bars-wrap" style="padding:0 18px 18px"></div></div>
      </div>
      <div class="g2">
        <div class="card"><div class="card-hd"><div><div class="card-title">Monthly Returns Distribution</div><div class="card-sub">Fund vs BTC</div></div></div><div class="cw h240"><canvas id="c-risk-dist"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">BTC Price Reference</div></div></div><div class="cw h240"><canvas id="c-risk-btc"></canvas></div></div>
      </div>
      <div class="g2" style="margin-top:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Rolling Correlation — Fund vs BTC</div><div class="card-sub">3-month rolling · Beta context</div></div></div><div class="cw h240"><canvas id="c-risk-corr"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Drawdown Recovery</div><div class="card-sub">Drawdown depth & High-Water Mark recovery</div></div></div><div class="cw h240"><canvas id="c-risk-recovery"></canvas></div></div>
      </div>
    </div>

    <div class="panel" id="p-portfolio">
      <div class="kpi-row" id="port-kpis"></div>
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Capital Structure</div><div class="card-sub" id="port-cap-sub">Asset, Loan, Fee breakdown</div></div></div><div id="port-capital" style="padding:0 18px 18px;max-height:420px;overflow-y:auto"></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Monthly Cashflow</div><div class="card-sub">Deposits, Withdrawals, PnL</div></div></div><div class="cw h320"><canvas id="c-port-cf"></canvas></div></div>
      </div>
      <div class="g2" style="margin-bottom:18px">
        <div class="card"><div class="card-hd"><div><div class="card-title">Trader Allocation % Over Time</div><div class="card-sub">Capital share per trader per month</div></div></div><div class="cw h240"><canvas id="c-port-tr"></canvas></div></div>
        <div class="card"><div class="card-hd"><div><div class="card-title">Fee Breakdown</div><div class="card-sub">By category over time</div></div></div><div class="cw h240"><canvas id="c-port-fee"></canvas></div></div>
      </div>
      <div class="card" style="margin-bottom:18px">
        <div class="card-hd">
          <div><div class="card-title">Average Purchase Price</div><div class="card-sub">Asset holdings — BTC · ETH · BNB</div></div>
          <div id="app-total" style="font-family:var(--mono);font-size:11px;color:var(--sub)"></div>
        </div>
        <div id="app-summary" style="margin-bottom:20px"></div>
        <div style="font-family:var(--mono);font-size:9px;letter-spacing:1.5px;color:var(--sub);margin-bottom:10px;padding-bottom:8px;border-bottom:1px solid var(--bdr)">TRANSACTION LOG</div>
        <div id="app-txns" style="max-height:320px;overflow-y:auto"></div>
      </div>
    </div>
  </div>
    <div class="panel" id="p-volume">
      <!-- KPI strip -->
      <div id="vol-kpis" style="display:flex;gap:12px;flex-wrap:wrap;margin-bottom:18px"></div>

      <!-- Row 1: Volume per trader + Trades per trader -->
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px">
        <div class="card">
          <div class="card-hd"><div>
            <div class="card-title">Trading Volume — Từng Trader</div>
            <div class="card-sub">Volume giao dịch ($) theo trader mỗi tháng</div>
          </div>
          <div id="vol-no-data-badge" style="display:flex;align-items:center;gap:6px;
            background:rgba(255,184,77,.12);border:1px solid rgba(255,184,77,.3);
            border-radius:6px;padding:5px 12px;font-size:9px;font-family:var(--mono);color:var(--gold)">
            ⏳ Chờ API
          </div></div>
          <div class="cw h280"><canvas id="c-vol-trader-vol"></canvas></div>
        </div>
        <div class="card">
          <div class="card-hd"><div>
            <div class="card-title">Số Lệnh — Từng Trader</div>
            <div class="card-sub">Số lệnh giao dịch theo trader mỗi tháng</div>
          </div></div>
          <div class="cw h280"><canvas id="c-vol-trader-trades"></canvas></div>
        </div>
      </div>

      <!-- Row 2: Volume by exchange -->
      <div class="card" style="margin-bottom:18px">
        <div class="card-hd"><div>
          <div class="card-title">Trading Volume — Theo Sàn</div>
          <div class="card-sub">Phân bổ volume giữa 6 sàn theo tháng</div>
        </div></div>
        <div class="cw h280" style="padding:0 18px 18px"><canvas id="c-vol-exchange-vol"></canvas></div>
      </div>
    </div>

    <div class="panel" id="p-live"></div>
</div>
<script>

// ═══════════════════════════════════════════════════════════════
// EMBEDDED DATA BUNDLE (pre-extracted from Excel)
// ═══════════════════════════════════════════════════════════════
const EMBEDDED_DATA = {"trading": {"rows": [{"loc": "OKX", "pic": "Việt", "monthly": {"May 2025": 2888.45, "Feb 2026": 5000}, "dep": {"Feb 2026": 5000}, "wd": {"May 2025": 2000}}, {"loc": "Bybit", "pic": "Việt", "monthly": {"Jul 2025": 931, "Aug 2025": 536.82, "Nov 2025": 14815.51, "Dec 2025": 4150.82}, "dep": {"Nov 2025": 13000}, "wd": {"Dec 2025": 9735.42}}, {"loc": "Binance", "pic": "Việt", "monthly": {"Oct 2025": 5195.01, "Nov 2025": 13856.96}, "dep": {"Oct 2025": 3000}, "wd": {}}, {"loc": "Bybit", "pic": "Nam", "monthly": {"Nov 2025": 21571.75, "Dec 2025": 20576.24, "Jan 2026": 21899.75, "Feb 2026": 21899.75}, "dep": {"Nov 2025": 10000}, "wd": {"Dec 2025": 16870.58, "Jan 2026": 28199, "Feb 2026": 15000}}, {"loc": "AARK 1", "pic": "Việt", "monthly": {"May 2025": 1880.81, "Jun 2025": 1921.9, "Jul 2025": 1922.7, "Aug 2025": 2109.55}, "dep": {}, "wd": {"Jul 2025": 5000, "Aug 2025": 3050}}, {"loc": "AARK 2", "pic": "Việt", "monthly": {"May 2025": 1400.48, "Jun 2025": 223.71, "Jul 2025": 1713.4, "Aug 2025": 1618.5}, "dep": {}, "wd": {"Aug 2025": 1500}}, {"loc": "Moonlander + Fullcrom", "pic": "Việt", "monthly": {"Aug 2025": 1535.1399999999999}, "dep": {}, "wd": {}}, {"loc": "Test", "pic": "An", "monthly": {"Sep 2025": 487.6, "Oct 2025": 494.46, "Nov 2025": 428.86, "Dec 2025": 426.37, "Jan 2026": 426.37, "Feb 2026": 426.37}, "dep": {"Sep 2025": 500}, "wd": {}}, {"loc": "Test", "pic": "Vũ - BE", "monthly": {"Sep 2025": 469.97, "Oct 2025": 469.97, "Nov 2025": 86, "Dec 2025": 86, "Jan 2026": 86, "Feb 2026": 86}, "dep": {"Sep 2025": 500}, "wd": {"Nov 2025": 200}}, {"loc": "Test", "pic": "Trân", "monthly": {"Sep 2025": 541.96}, "dep": {"Sep 2025": 500}, "wd": {}}, {"loc": "Test", "pic": "Hiệp", "monthly": {"Sep 2025": 309.68, "Oct 2025": 308.6, "Nov 2025": 308.98, "Dec 2025": 308.95, "Jan 2026": 308.95, "Feb 2026": 308.95}, "dep": {"Sep 2025": 300}, "wd": {}}, {"loc": "EdgeX", "pic": "Việt", "monthly": {"Sep 2025": 1014.23}, "dep": {"Sep 2025": 1000}, "wd": {}}, {"loc": "Lighter 1", "pic": "Việt", "monthly": {"Aug 2025": 2026.34, "Sep 2025": 20050.67}, "dep": {"Sep 2025": 36300}, "wd": {"Sep 2025": 23300}}, {"loc": "Bybit", "pic": "Vũ - FM", "monthly": {"Nov 2025": 1913.92, "Dec 2025": 1417.92, "Jan 2026": 1007.97, "Feb 2026": 1007.97}, "dep": {"Nov 2025": 2000, "Dec 2025": 1494.56, "Jan 2026": 840}, "wd": {"Dec 2025": 2109.0699999999997, "Feb 2026": 841.36}}, {"loc": "Polymarket", "pic": "Vũ - FM", "monthly": {"Feb 2026": 400}, "dep": {"Feb 2026": 21935.89}, "wd": {"Feb 2026": 23687}}, {"loc": "Lighter", "pic": "Vũ - FM", "monthly": {"Dec 2025": 4596.45, "Jan 2026": 13523.48}, "dep": {"Dec 2025": 5700, "Jan 2026": 11000}, "wd": {"Dec 2025": 1211.73, "Jan 2026": 1440}}, {"loc": "Decibel", "pic": "Vũ - FM", "monthly": {"Feb 2026": 110}, "dep": {"Feb 2026": 110}, "wd": {}}, {"loc": "Hyperliquid", "pic": "Vũ - FM", "monthly": {"Nov 2025": 1064.32, "Dec 2025": 9220.52, "Jan 2026": 1913.61, "Feb 2026": 1913.61}, "dep": {"Nov 2025": 1000, "Dec 2025": 7106.02, "Jan 2026": 300, "Feb 2026": 20277}, "wd": {"Dec 2025": 200, "Jan 2026": 12000, "Feb 2026": 1910}}, {"loc": "MEXC", "pic": "Vũ - FM", "monthly": {"Jan 2026": 371.38, "Feb 2026": 371.38}, "dep": {"Jan 2026": 669.0899999999999}, "wd": {"Jan 2026": 369.09}}, {"loc": "Binance", "pic": "Vũ - FM", "monthly": {"Dec 2025": 1014.78, "Jan 2026": 1441.61, "Feb 2026": 1441.61}, "dep": {"Dec 2025": 1203.05, "Jan 2026": 1633.47, "Feb 2026": 6113}, "wd": {"Dec 2025": 195.14, "Jan 2026": 150, "Feb 2026": 3632.89}}, {"loc": "Variational", "pic": "Vũ - FM", "monthly": {"Dec 2025": 218.46}, "dep": {"Dec 2025": 211.73}, "wd": {}}, {"loc": "Polymarket", "pic": "Việt", "monthly": {"Feb 2026": 250}, "dep": {"Feb 2026": 250}, "wd": {}}, {"loc": "Variational", "pic": "Việt", "monthly": {"Nov 2025": 10196.8, "Jan 2026": 502.9, "Feb 2026": 252.89999999999998}, "dep": {"Nov 2025": 10200, "Jan 2026": 500}, "wd": {"Feb 2026": 500}}, {"loc": "Lighter 2", "pic": "Việt", "monthly": {"Oct 2025": 7028.21, "Nov 2025": 10396.75, "Dec 2025": 9667.11}, "dep": {"Oct 2025": 10000}, "wd": {"Oct 2025": 30000}}, {"loc": "Decibel", "pic": "Việt", "monthly": {"Feb 2026": 100}, "dep": {"Feb 2026": 250}, "wd": {}}, {"loc": "Lighter 3", "pic": "Việt", "monthly": {"Sep 2025": 2399.17, "Oct 2025": 39771.45, "Nov 2025": 55435.17, "Dec 2025": 88378.20999999999, "Jan 2026": 92845, "Feb 2026": 92845}, "dep": {"Sep 2025": 21816.32, "Oct 2025": 40000, "Dec 2025": 35815.130000000005, "Jan 2026": 20858.239999999998, "Feb 2026": 50000}, "wd": {"Sep 2025": 12000, "Nov 2025": 74100, "Dec 2025": 30000, "Jan 2026": 57289.2, "Feb 2026": 55000}}, {"loc": "Lighter 4", "pic": "Việt", "monthly": {"Sep 2025": 15124.640000000001}, "dep": {"Sep 2025": 12943.47}, "wd": {}}, {"loc": "Lighter 5", "pic": "Việt", "monthly": {"Sep 2025": 1205.28}, "dep": {"Sep 2025": 1000}, "wd": {}}, {"loc": "Lighter 6", "pic": "Việt", "monthly": {"Sep 2025": 1302.51}, "dep": {"Sep 2025": 1000}, "wd": {}}, {"loc": "Lighter 7", "pic": "Việt", "monthly": {"Sep 2025": 12777.83}, "dep": {"Sep 2025": 8000}, "wd": {}}, {"loc": "Lighter 8", "pic": "Việt", "monthly": {"Sep 2025": 4997.96}, "dep": {"Sep 2025": 5000}, "wd": {}}, {"loc": "Lighter 4", "pic": "Việt", "monthly": {"Nov 2025": 10704.08, "Dec 2025": 11902.6}, "dep": {"Nov 2025": 10000}, "wd": {}}, {"loc": "Ethereal", "pic": "Việt", "monthly": {"Jan 2026": 10000}, "dep": {"Jan 2026": 10000}, "wd": {}}, {"loc": "Hyperliquid", "pic": "Việt", "monthly": {"May 2025": 240914.08, "Jun 2025": 217079.22, "Jul 2025": 241810.18, "Aug 2025": 209270.32, "Sep 2025": 413098.75, "Oct 2025": 2088.62, "Nov 2025": 1456.57}, "dep": {"May 2025": 100000, "Jul 2025": 193970, "Sep 2025": 160000, "Oct 2025": 50000, "Nov 2025": 200}, "wd": {"Jun 2025": 102000, "Jul 2025": 135000, "Sep 2025": 35000, "Oct 2025": 261547.6, "Nov 2025": 200}}, {"loc": "Hyperliquid (Sub)", "pic": "Việt", "monthly": {"May 2025": 2000, "Jun 2025": 8431.59, "Jul 2025": 62360.48, "Aug 2025": 90770.16, "Sep 2025": 20000}, "dep": {"Jun 2025": 23946.97, "Jul 2025": 16630.66}, "wd": {"Jun 2025": 19460.6, "Jul 2025": 1000}}, {"loc": "Hyperliquid", "pic": "Việt", "monthly": {"Jun 2025": 10901.3}, "dep": {"Jun 2025": 10000}, "wd": {}}, {"loc": "Hyperliquid", "pic": "Việt", "monthly": {"Jun 2025": 10523.79}, "dep": {"Jun 2025": 10000}, "wd": {}}, {"loc": "HMX", "pic": "Việt", "monthly": {"Jun 2025": 970}, "dep": {"Jun 2025": 1000}, "wd": {}}, {"loc": "Hyperliquid", "pic": "Tùng", "monthly": {"May 2025": 77027.52, "Jun 2025": 80410.61, "Jul 2025": 123534.16, "Aug 2025": 78433.02, "Sep 2025": 67667.74, "Oct 2025": 101109.35, "Nov 2025": 49799.64, "Dec 2025": 98318.33, "Jan 2026": 124934.81, "Feb 2026": 124934.81}, "dep": {"Jun 2025": 70000, "Jul 2025": 100000, "Aug 2025": 100000, "Sep 2025": 100000, "Oct 2025": 100000, "Nov 2025": 10000}, "wd": {"May 2025": 115293.6, "Jun 2025": 70000, "Jul 2025": 100000, "Aug 2025": 50000, "Sep 2025": 30000, "Oct 2025": 66000, "Nov 2025": 30500, "Jan 2026": 22000, "Feb 2026": 30100}}, {"loc": "Hyperliquid (Sub)", "pic": "Tùng", "monthly": {"Jun 2025": 58389.62, "Jul 2025": 40189.54, "Aug 2025": 70163.38, "Sep 2025": 116766.71}, "dep": {}, "wd": {}}, {"loc": "Lighter 1", "pic": "Tùng", "monthly": {"Sep 2025": 33677.26, "Oct 2025": 27872.641, "Nov 2025": 36810.776, "Dec 2025": 39015.240000000005, "Jan 2026": 100755.31, "Feb 2026": 100755.31}, "dep": {"Sep 2025": 30000, "Oct 2025": 34000, "Jan 2026": 15000}, "wd": {"Oct 2025": 31632, "Nov 2025": 52500, "Dec 2025": 25000, "Feb 2026": 20000}}, {"loc": "Decibel", "pic": "Tùng", "monthly": {"Feb 2026": 100}, "dep": {"Feb 2026": 100}, "wd": {}}, {"loc": "Decibel", "pic": "Nam", "monthly": {"Feb 2026": 100}, "dep": {"Feb 2026": 100}, "wd": {}}, {"loc": "Polymarket", "pic": "Nam", "monthly": {"Feb 2026": 1000}, "dep": {"Feb 2026": 2000}, "wd": {}}, {"loc": "Hyperliquid", "pic": "Nam", "monthly": {"May 2025": 223646.14, "Jun 2025": 237321.63, "Jul 2025": 314061.34, "Aug 2025": 279832.07, "Sep 2025": 148751.91, "Oct 2025": 49473.61, "Nov 2025": 152583.9, "Dec 2025": 142166.67, "Jan 2026": 211843.98, "Feb 2026": 211843.98}, "dep": {"May 2025": 100000, "Jun 2025": 50000, "Jul 2025": 50000, "Sep 2025": 35000, "Oct 2025": 132094, "Nov 2025": 20000, "Dec 2025": 1015.35, "Jan 2026": 40000}, "wd": {"Jun 2025": 60000, "Sep 2025": 136815.7, "Oct 2025": 149291.87, "Nov 2025": 26000, "Dec 2025": 59772, "Jan 2026": 66000, "Feb 2026": 30100}}, {"loc": "Hyperliquid", "pic": "Nam", "monthly": {"Aug 2025": 30655.92, "Sep 2025": 54578.39}, "dep": {"Aug 2025": 50000, "Sep 2025": 96815.7}, "wd": {"Aug 2025": 20000, "Sep 2025": 75000}}, {"loc": "AARK", "pic": "Nam", "monthly": {"Jul 2025": 2290.31, "Aug 2025": 3032.7}, "dep": {}, "wd": {}}, {"loc": "MEXC", "pic": "Nam", "monthly": {"Sep 2025": 479.38}, "dep": {"Sep 2025": 500}, "wd": {}}, {"loc": "Hyperliquid 2", "pic": "Nam", "monthly": {"Nov 2025": 8125.89}, "dep": {"Nov 2025": 5000}, "wd": {}}, {"loc": "Variational", "pic": "Nam", "monthly": {"Nov 2025": 1004.24}, "dep": {"Nov 2025": 1000}, "wd": {}}, {"loc": "Ethereal", "pic": "Nam", "monthly": {"Jan 2026": 10000}, "dep": {"Jan 2026": 10000}, "wd": {}}, {"loc": "Lighter 2", "pic": "Nam", "monthly": {"Nov 2025": 36499.45, "Dec 2025": 30774.39, "Jan 2026": 35993.75, "Feb 2026": 35993.75}, "dep": {"Nov 2025": 15000, "Jan 2026": 28199, "Feb 2026": 10000}, "wd": {"Dec 2025": 22762, "Jan 2026": 50000, "Feb 2026": 15000}}, {"loc": "Lighter 1", "pic": "Nam", "monthly": {"Aug 2025": 20052.8, "Sep 2025": 10205.62, "Oct 2025": 17613.697, "Nov 2025": 9781.94, "Dec 2025": 15599.09, "Jan 2026": 17756.98, "Feb 2026": 17756.98}, "dep": {"Oct 2025": 105000, "Nov 2025": 10000}, "wd": {"Sep 2025": 10000, "Oct 2025": 94608, "Nov 2025": 15000, "Feb 2026": 7000}}, {"loc": "Lighter 2", "pic": "Nam", "monthly": {"Sep 2025": 4748.69}, "dep": {"Sep 2025": 80000}, "wd": {"Sep 2025": 67000}}], "months": ["May 2025", "Jun 2025", "Jul 2025", "Aug 2025", "Sep 2025", "Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026"]}, "hf": {"months": ["Apr 2025", "May 2025", "Jun 2025", "Jul 2025", "Aug 2025", "Sep 2025", "Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026"], "rows": [{"label": "Asset", "data": {"Apr 2025": 319400, "May 2025": 480972.67261684244, "Jun 2025": 478677.57367460395, "Jul 2025": 682425.490279, "Aug 2025": 735347.40935, "Sep 2025": 647928.7283329999, "Oct 2025": 481880.7669635583, "Nov 2025": 432686.5504972319, "Dec 2025": 473864.6727875356, "Jan 2026": 492765.94237834594, "Feb 2026": 624595.599106346}}, {"label": "None-custodial wallet", "data": {"Apr 2025": 60588, "May 2025": 125578.45261684243, "Jun 2025": 124977.24367460392, "Jul 2025": 255760.45027900004, "Aug 2025": 317288.87934999994, "Sep 2025": 323426.51703299995, "Oct 2025": 479606.71666355827, "Nov 2025": 431955.3001972319, "Dec 2025": 452658.7754875356, "Jan 2026": 491406.4323783459, "Feb 2026": 623236.089106346}}, {"label": "USDC+USDT", "data": {"Apr 2025": 0, "May 2025": 4302.33, "Jun 2025": 4302.33, "Jul 2025": 4302.33, "Aug 2025": 4302.33, "Sep 2025": 37134.7513, "Oct 2025": 2274.0503000000035, "Nov 2025": 731.2502999999997, "Dec 2025": 21205.8973, "Jan 2026": 1359.51, "Feb 2026": 1359.51}}, {"label": "CEX Asset", "data": {"Apr 2025": 258812, "May 2025": 351091.89, "Jun 2025": 349398, "Jul 2025": 422362.70999999996, "Aug 2025": 413756.2, "Sep 2025": 287367.45999999996, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "HYPE", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 45650, "Nov 2025": 34210, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Hedge Trading", "data": {"Apr 2025": 152600, "May 2025": 543587.74, "Jun 2025": 624027.76, "Jul 2025": 781955.7, "Aug 2025": 759124.87, "Sep 2025": 820863.5, "Oct 2025": 152671.58000000002, "Nov 2025": 213030.32, "Dec 2025": 249705.52000000002, "Jan 2026": 338692.4, "Feb 2026": 338692.4}}, {"label": "Copin IF (Hyperliquid) - Tùng", "data": {"Apr 2025": 50000, "May 2025": 77027.52, "Jun 2025": 138800.23, "Jul 2025": 163723.7, "Aug 2025": 148596.40000000002, "Sep 2025": 184434.45, "Oct 2025": 101109.35, "Nov 2025": 49799.64, "Dec 2025": 98318.33, "Jan 2026": 124934.81, "Feb 2026": 124934.81}}, {"label": "Copin IF (Hyperliquid) - Việt", "data": {"Apr 2025": 42600, "May 2025": 242914.08, "Jun 2025": 246935.9, "Jul 2025": 304170.66, "Aug 2025": 300040.48, "Sep 2025": 433098.75, "Oct 2025": 2088.62, "Nov 2025": 1456.57, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Copin IF (HMX) - Việt", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 970, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Copin IF (Hyperliquid) - Vũ FM", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 1064.32, "Dec 2025": 9220.52, "Jan 2026": 1913.61, "Feb 2026": 1913.61}}, {"label": "Copin IF (Hyperliquid) - Nam", "data": {"Apr 2025": 60000, "May 2025": 223646.14, "Jun 2025": 237321.63, "Jul 2025": 314061.34, "Aug 2025": 310487.99, "Sep 2025": 203330.3, "Oct 2025": 49473.61, "Nov 2025": 160709.79, "Dec 2025": 142166.67, "Jan 2026": 211843.98, "Feb 2026": 211843.98}}, {"label": "Algo Trading", "data": {"Apr 2025": 28000, "May 2025": 6169.74, "Jun 2025": 2145.61, "Jul 2025": 6857.41, "Aug 2025": 30911.85, "Sep 2025": 109313.07, "Oct 2025": 98754.03799999999, "Nov 2025": 223811.186, "Dec 2025": 228132.63000000003, "Jan 2026": 306919.45, "Feb 2026": 278555.97000000003}}, {"label": "CEX Copy MM", "data": {"Apr 2025": 20000, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 931, "Aug 2025": 536.82, "Sep 2025": 0, "Oct 2025": 5195.01, "Nov 2025": 52158.14, "Dec 2025": 27159.760000000002, "Jan 2026": 24720.710000000003, "Feb 2026": 24720.710000000003}}, {"label": "DEX Copy MM", "data": {"Apr 2025": 7000, "May 2025": 6169.74, "Jun 2025": 2145.61, "Jul 2025": 5926.41, "Aug 2025": 30375.03, "Sep 2025": 109313.07, "Oct 2025": 93559.02799999999, "Nov 2025": 171653.046, "Dec 2025": 200972.87000000002, "Jan 2026": 282198.74, "Feb 2026": 253835.26}}, {"label": "- Copin IF (Lighter)- Việt", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 37807.39, "Oct 2025": 46799.659999999996, "Nov 2025": 76536, "Dec 2025": 109947.92, "Jan 2026": 92845, "Feb 2026": 92945}}, {"label": "- Copin IF (Lighter) - Nam", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 14954.310000000001, "Oct 2025": 17613.697, "Nov 2025": 46281.39, "Dec 2025": 46373.479999999996, "Jan 2026": 53750.729999999996, "Feb 2026": 53750.729999999996}}, {"label": "- Copin IF (Lighter) - Tùng", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 33677.26, "Oct 2025": 27872.641, "Nov 2025": 36810.776, "Dec 2025": 39015.240000000005, "Jan 2026": 100755.31, "Feb 2026": 100755.31}}, {"label": "- Copin IF (Lighter) - Vũ FM", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 4596.45, "Jan 2026": 13523.48, "Feb 2026": 0}}, {"label": "Lite Copin", "data": {"Apr 2025": 1000, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Prediction Market", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 1650}}, {"label": "Poly Flash - Việt", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 250}}, {"label": "Poly Market - Nam", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 1000}}, {"label": "Poly Market - Vũ FM", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 400}}, {"label": "Equity", "data": {"Apr 2025": 500000, "May 2025": 500000, "Jun 2025": 500000, "Jul 2025": 500000, "Aug 2025": 500000, "Sep 2025": 500000, "Oct 2025": 500000, "Nov 2025": 500000, "Dec 2025": 500000, "Jan 2026": 500000, "Feb 2026": 500000}}, {"label": "Loan", "data": {"Apr 2025": 0, "May 2025": 115653, "Jun 2025": 115653, "Jul 2025": 311998.288109589, "Aug 2025": 413413.85309589043, "Sep 2025": 465315.74520547944, "Oct 2025": 82110.92546575343, "Nov 2025": 30674.609191780826, "Dec 2025": 0.0018356164383897065, "Jan 2026": 0.0018356164383897065, "Feb 2026": 0.0018356164383897065}}, {"label": "Binance Loan", "data": {"Apr 2025": 0, "May 2025": 115653, "Jun 2025": 115653, "Jul 2025": 310653, "Aug 2025": 410653, "Sep 2025": 460016.82, "Oct 2025": 0.3800000000046566, "Nov 2025": 0.3800000000046566, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "- Loan (Tùng)", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 20000, "Jul 2025": 100000, "Aug 2025": 150000, "Sep 2025": 99363.82, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "- Loan (Việt)", "data": {"Apr 2025": 0, "May 2025": 65653, "Jun 2025": 55653, "Jul 2025": 120653, "Aug 2025": 120653, "Sep 2025": 170653, "Oct 2025": 0.3800000000046566, "Nov 2025": 0.3800000000046566, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "- Loan (Nam)", "data": {"Apr 2025": 0, "May 2025": 50000, "Jun 2025": 40000, "Jul 2025": 90000, "Aug 2025": 140000, "Sep 2025": 190000, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Tung Loan", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 50000, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "AAVE Loan", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 32026.299, "Nov 2025": 30383.459, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Accrued Interest ($)", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 1345.2881095890411, "Aug 2025": 2760.853095890411, "Sep 2025": 5298.925205479452, "Oct 2025": 84.24646575342466, "Nov 2025": 290.77019178082196, "Dec 2025": 0.0018356164383897065, "Jan 2026": 0.0018356164383897065, "Feb 2026": 0.0018356164383897065}}, {"label": "HYPE Loan", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 45650, "Nov 2025": 34210, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Fee", "data": {"Apr 2025": 0, "May 2025": 19999, "Jun 2025": 3300, "Jul 2025": 122299.48591865104, "Aug 2025": 107299.48591865104, "Sep 2025": 107299.48591865104, "Oct 2025": 91593, "Nov 2025": 89372, "Dec 2025": 60372, "Jan 2026": 39789.2, "Feb 2026": 0}}, {"label": "Buy subcription (Copin)", "data": {"Apr 2025": 0, "May 2025": 4999, "Jun 2025": 3000, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 6000, "Nov 2025": 0, "Dec 2025": 1000, "Jan 2026": 14789.2, "Feb 2026": 0}}, {"label": "Management Fee", "data": {"Apr 2025": 0, "May 2025": 15000, "Jun 2025": 0, "Jul 2025": 15000, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 10000, "Dec 2025": 25000, "Jan 2026": 25000, "Feb 2026": 0}}, {"label": "Performance Fee", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 107299.48591865104, "Aug 2025": 107299.48591865104, "Sep 2025": 107299.48591865104, "Oct 2025": 79372, "Nov 2025": 79372, "Dec 2025": 34372, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Other Fee", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 300, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 0, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Accrued Interest", "data": {"Apr 2025": 0, "May 2025": 0, "Jun 2025": 0, "Jul 2025": 0, "Aug 2025": 0, "Sep 2025": 0, "Oct 2025": 6221, "Nov 2025": 0, "Dec 2025": 0, "Jan 2026": 0, "Feb 2026": 0}}, {"label": "Net Profit", "data": {"Apr 2025": 0, "May 2025": 415077.1526168424, "Jun 2025": 489197.9436746042, "Jul 2025": 551940.8262507599, "Aug 2025": 504670.7903354585, "Sep 2025": 505490.06720886944, "Oct 2025": 71823.45949780487, "Nov 2025": 259481.44730545097, "Dec 2025": 417330.8209519192, "Jan 2026": 638377.7905427295, "Feb 2026": 743493.9672707297}}, {"label": "Total (NAV)", "data": {"Apr 2025": 500000, "May 2025": 915077.1526168424, "Jun 2025": 989197.9436746042, "Jul 2025": 1051940.8262507597, "Aug 2025": 1004670.7903354585, "Sep 2025": 1005490.0672088694, "Oct 2025": 571823.4594978049, "Nov 2025": 759481.447305451, "Dec 2025": 917330.8209519192, "Jan 2026": 1138377.7905427294, "Feb 2026": 1243493.9672707296}}], "MONTH_COLS": {"Apr 2025": 3, "May 2025": 4, "Jun 2025": 5, "Jul 2025": 6, "Aug 2025": 7, "Sep 2025": 8, "Oct 2025": 9, "Nov 2025": 10, "Dec 2025": 11, "Jan 2026": 12, "Feb 2026": 13}}, "risk": {"months": ["Apr 2025", "May 2025", "Jun 2025", "Jul 2025", "Aug 2025", "Sep 2025", "Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026"], "nav": [500000, 915077.1526168424, 989197.9436746042, 1051940.8262507597, 1004670.7903354585, 1005490.0672088694, 571823.4594978049, 759481.447305451, 917330.8209519192, 1138377.7905427294, 1243493.9672707296], "fundRet": [0, 0.8301543052336848, 0.08099949916332064, 0.06342803579138322, -0.04493602181386678, 0.0008154679933885174, -0.4312987485941812, 0.32817469218988304, 0.20783835366419914, 0.24096755994901448, 0.09233856949887023], "fundDD": [0, 0, 0, 0, -0.04493602181386678, -0.04415719770801768, -0.456411002189305, -0.2780188501549732, -0.12796347659459734, 0, 0], "fundPeak": [500000, 915077.1526168424, 989197.9436746042, 1051940.8262507597, 1051940.8262507597, 1051940.8262507597, 1051940.8262507597, 1051940.8262507597, 1051940.8262507597, 1138377.7905427294, 1243493.9672707296], "btcPrice": [94981, 105370.4, 107220, 118229.57, 110147.95, 114509.37, 109322.8, 90876.88, 88730.83, 82203.44, 65587], "btcRet": [0, 0.10938398205956974, 0.017553316680965488, 0.10268205558664435, -0.06835531923189782, 0.03959601608563753, -0.045293848005626025, -0.16872893851968662, -0.023614917237475615, -0.07356394615039664, -0.2021380127157696], "btcDD": [0, 0, 0, 0, -0.06835531923189782, -0.0314659014661054, -0.07533453771336565, -0.23135235965080478, -0.24950391006243194, -0.30471336400868243, -0.44525722287580005], "btcPeak": [94981, 105370.4, 107220, 118229.57, 118229.57, 118229.57, 118229.57, 118229.57, 118229.57, 118229.57, 118229.57], "metrics": {"CAGR": {"fund": 1.9840659284394553, "btc": -0.3587649773246001}, "Vol": {"fund": 1.1063403291819895, "btc": 0.3589663019141775}, "Sharpe": {"fund": 1.4843335385821428, "btc": -1.0445981467845198}, "Sortino": {"fund": 4.225462536564784, "btc": -1.4154088841601136}, "MaxDD": {"fund": -0.456411002189305, "btc": -0.44525722287580005}, "WorstM": {"fund": -0.4312987485941812, "btc": -0.2021380127157696}, "Calmar": {"fund": 5.191583184316355, "btc": -0.8751255328112745}, "Beta": {"fund": 0.6981325805754381, "btc": null}, "Alpha": {"fund": 2.234531447879167, "btc": null}}}, "meta": {"filename": "Monthly_Copin_IF_Report_2026_-_Draft__Risk_metric_.xlsx", "exported": "2026-03-05"}, "app": {"summary": [{"asset": "BTC", "avgPrice": 91537.51208216448, "quantity": 5.492274992301845, "value": 502749.18846639997}, {"asset": "ETH", "avgPrice": 2501.0018035864136, "quantity": 43.25769999999999, "value": 108187.58571899998}, {"asset": "BNB", "avgPrice": 488.98728633510393, "quantity": 62.129999999999995, "value": 30380.780100000004}], "txns": [{"asset": "ETH", "date": "01/04/2025", "qty": 33.12, "price": 1829.3478260869567, "value": 60588}, {"asset": "BNB", "date": "01/04/2025", "qty": 531, "price": 487.4048964218456, "value": 258812}, {"asset": "BTC", "date": "04/05/2025", "qty": 0.105, "price": 95238.09523809524, "value": 10000}, {"asset": "BTC", "date": "14/05/2025", "qty": 0.1945, "price": 102827.76349614396, "value": 20000}, {"asset": "BTC", "date": "19/05/2025", "qty": 0.09744499230184561, "price": 102622, "value": 10000}, {"asset": "ETH", "date": "03/07/2025", "qty": 8, "price": 2500, "value": 20000}, {"asset": "BTC", "date": "29/07/2025", "qty": 0.29736, "price": 117703.98999999999, "value": 35000.4584664}, {"asset": "ETH", "date": "29/07/2025", "qty": 3.9687, "price": 3779.37, "value": 14999.185719}, {"asset": "BNB", "date": "21/08/2025", "qty": -50, "price": 487, "value": -24350}, {"asset": "BTC", "date": "21/08/2025", "qty": 0.3822, "price": 112491.44427001571, "value": 42994.23}, {"asset": "BNB", "date": "31/08/2025", "qty": -50, "price": 487, "value": -24350}, {"asset": "BTC", "date": "31/08/2025", "qty": 0.3946, "price": 109231.32285859098, "value": 43102.68}, {"asset": "BNB", "date": "18/09/2025", "qty": -50, "price": 487, "value": -24350}, {"asset": "BTC", "date": "18/09/2025", "qty": 0.4255, "price": 111793.32549941246, "value": 47568.06}, {"asset": "BNB", "date": "24/09/2025", "qty": -100, "price": 487, "value": -48700}, {"asset": "ETH", "date": "24/09/2025", "qty": -20, "price": 2119.98, "value": -42399.6}, {"asset": "BNB", "date": "13/10/2025", "qty": -118.87, "price": 487.77, "value": -57981.219900000004}, {"asset": "ETH", "date": "14/11/2025", "qty": 1.5776, "price": 3169.371196754564, "value": 5000}, {"asset": "BTC", "date": "14/11/2025", "qty": 0.1547, "price": 96961.86166774401, "value": 15000}, {"asset": "BTC", "date": "25/11/2025", "qty": 0.11277, "price": 88673.38, "value": 10000}, {"asset": "ETH", "date": "25/11/2025", "qty": 3.3594, "price": 2976.66, "value": 10000}, {"asset": "ETH", "date": "23/12/2025", "qty": 6.79, "price": 2976.66, "value": 20000}, {"asset": "ETH", "date": "29/12/2025", "qty": 3.367, "price": 2970.00297000297, "value": 10000}, {"asset": "BTC", "date": "14/01/2026", "qty": 0.0415, "price": 95382.1686746988, "value": 3958.36}, {"asset": "ETH", "date": "14/01/2026", "qty": 1.507, "price": 3317.850033178501, "value": 5000}, {"asset": "ETH", "date": "20/01/2026", "qty": 1.568, "price": 3188.7755102040815, "value": 5000}, {"asset": "BTC", "date": "21/01/2026", "qty": 0.1118, "price": 89445.43828264759, "value": 10000}, {"asset": "BTC", "date": "21/01/2026", "qty": 0.1123, "price": 89047.19501335708, "value": 10000}, {"asset": "BTC", "date": "21/01/2026", "qty": 0.0403, "price": 89435.73200992556, "value": 3604.26}, {"asset": "BTC", "date": "21/01/2026", "qty": 0.1892, "price": 89356.1310782241, "value": 16906.18}, {"asset": "BTC", "date": "27/01/2026", "qty": 0.113, "price": 88495.57522123893, "value": 10000}, {"asset": "BNB", "date": "28/01/2026", "qty": -100, "price": 487, "value": -48700}, {"asset": "BTC", "date": "28/01/2026", "qty": 1.0027, "price": 89373.65114191684, "value": 89614.96}, {"asset": "BTC", "date": "29/01/2026", "qty": 0.1137, "price": 87950.74758135444, "value": 10000}, {"asset": "BTC", "date": "30/01/2026", "qty": 0.2428, "price": 82372.32289950577, "value": 20000}, {"asset": "BTC", "date": "02/02/2026", "qty": 0.2643, "price": 75671.58531971245, "value": 20000}, {"asset": "BTC", "date": "03/02/2026", "qty": 0.128, "price": 78125, "value": 10000}, {"asset": "BTC", "date": "06/02/2026", "qty": 0.1526, "price": 65530.7994757536, "value": 10000}, {"asset": "BTC", "date": "06/02/2026", "qty": 0.153, "price": 65359.47712418301, "value": 10000}, {"asset": "BTC", "date": "08/02/2026", "qty": 0.1444, "price": 69252.07756232687, "value": 10000}, {"asset": "BTC", "date": "09/02/2026", "qty": 0.0709, "price": 70521.86177715092, "value": 5000}, {"asset": "BTC", "date": "10/02/2026", "qty": 0.0711, "price": 70323.48804500703, "value": 5000}, {"asset": "BTC", "date": "11/02/2026", "qty": 0.0724, "price": 69060.77348066297, "value": 5000}, {"asset": "BTC", "date": "17/02/2026", "qty": 0.1478, "price": 67658.99864682004, "value": 10000}, {"asset": "BTC", "date": "23/02/2026", "qty": 0.077, "price": 64935.06493506493, "value": 5000}, {"asset": "BTC", "date": "24/02/2026", "qty": 0.0794, "price": 62972.29219143577, "value": 5000}], "total": 641317.5542854}, "vol": {"months": ["May 2025", "Jun 2025", "Jul 2025", "Aug 2025", "Sep 2025", "Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026"], "totalFee": [19999, 3300, 122299.48591865104, 107299.48591865104, 107299.48591865104, 91593, 89372, 60372, 39789.2, 0], "subFee": [4999, 3000, 0, 0, 0, 6000, 0, 1000, 14789.2, 0], "mgmtFee": [15000, 0, 15000, 0, 0, 0, 10000, 25000, 25000, 0], "perfFee": [0, 0, 107299.48591865104, 107299.48591865104, 107299.48591865104, 79372, 79372, 34372, 0, 0], "otherFee": [0, 300, 0, 0, 0, 0, 0, 0, 0, 0], "pnlByMonth": [-82706.4, 162929.52, 43039.08, -74226.39, -61440.56, -520244.86, 276515.89, 156306.74, 266221.19, 59921.88], "navByMonth": [549757.48, 626173.37, 788813.11, 790036.72, 930655.95, 251425.62, 436841.51, 477838.15, 645611.85, 618898.37], "feeRatio": [3.64, 0.53, 15.5, 13.58, 11.53, 36.43, 20.46, 12.63, 6.16, 0.0], "tradingVol": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "numTrades": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "traderVol": {"Việt": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Nam": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Tùng": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Vũ - FM": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Hiệp": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]}, "traderTrades": {"Việt": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Nam": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Tùng": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Vũ - FM": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Hiệp": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]}, "exchangeVol": {"Hyperliquid": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Lighter": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Binance": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Bybit": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "OKX": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "Decibel": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]}}, "live": {"lastSync": null, "syncStatus": "idle", "exchanges": {"hyperliquid": {"name": "Hyperliquid", "icon": "HL", "color": "#4f8eff", "status": "pending", "traders": {"Việt": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Tùng": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Vũ-FM": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "lighter": {"name": "Lighter", "icon": "LT", "color": "#2dd4a0", "status": "pending", "traders": {"Việt": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Tùng": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Vũ-FM": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Hiệp": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "binance": {"name": "Binance", "icon": "BN", "color": "#f0b90b", "status": "pending", "traders": {"Tùng": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "bybit": {"name": "Bybit", "icon": "BY", "color": "#f7a600", "status": "pending", "traders": {"Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Việt": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "okx": {"name": "OKX", "icon": "OX", "color": "#9d7eff", "status": "pending", "traders": {"Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Việt": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "decibel": {"name": "Decibel", "icon": "DB", "color": "#fb923c", "status": "pending", "traders": {"Vũ-FM": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}, "polymarket": {"name": "Polymarket", "icon": "PM", "color": "#38bdf8", "status": "pending", "traders": {"Việt": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Nam": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}, "Vũ-FM": {"balance": 0, "pnl24h": 0, "pnlPct": 0, "positions": [], "volume30d": 0, "trades30d": 0}}}}, "api_notes": {"hyperliquid": "Public REST — chỉ cần wallet address", "lighter": "Public REST — chỉ cần wallet address + account_index", "binance": "Cần API Key + Secret (read-only)", "bybit": "Cần API Key + Secret (read-only)", "okx": "Cần API Key + Secret + Passphrase", "decibel": "Cần kiểm tra API documentation", "polymarket": "Public API — dùng wallet address, không cần key"}}};

function loadEmbeddedData() {
  try {
    const b = EMBEDDED_DATA;
    // Reconstruct D in same format as parse()
    D = {
      rows:   b.trading.rows,
      months: b.trading.months,
      risk:   b.risk,
      hf:     b.hf,
      app:    b.app,
      vol:    b.vol||null,
      live:   b.live||null,
    };
    document.getElementById('fname').textContent = b.meta.filename;
    init();
  } catch(e) {
    console.error('Failed to load embedded data:', e);
  }
}

// ═══════════════════════════════════════════════════════════════
// CONFIG & STATE
// ═══════════════════════════════════════════════════════════════
let TCOLS={}, MONTHS=[], D=null, SELM=null, CH={}, ASORT={col:'bal',dir:-1}, AFLT='';

const PC={
  'Tùng' :{c:'#ffb84d',bg:'rgba(255,184,77,.65)'},
  'Việt' :{c:'#4f8eff',bg:'rgba(79,142,255,.65)'},
  'Nam'  :{c:'#2dd4a0',bg:'rgba(45,212,160,.65)'},
  'Vũ - FM':{c:'#9d7eff',bg:'rgba(157,126,255,.65)'},
};
const PLC={Hyperliquid:'#9d7eff',Binance:'#ffb84d',Bybit:'#ff5f87',OKX:'#b0bcd4',
  Lighter:'#38bdf8',Decibel:'#2dd4a0',MEXC:'#4f8eff',Polymarket:'#f472b6',
  AARK:'#fb923c',Test:'#4a5568',Other:'#3d4f6e'};
const RISK_LABELS={
  CAGR:'CAGR (Annualized Return)',
  Vol:'Annualized Volatility',
  Sharpe:'Sharpe Ratio',
  Sortino:'Sortino Ratio',
  MaxDD:'Max Drawdown',
  WorstM:'Worst Month',
  Calmar:'Calmar Ratio',
  Beta:'Beta (vs BTC)',
  Alpha:'Alpha (vs BTC)',
};

// ═══════════════════════════════════════════════════════════════
// FORMAT
// ═══════════════════════════════════════════════════════════════
const fm=n=>{if(n==null||isNaN(n))return'—';const a=Math.abs(n);
  if(a>=1e6)return(n<0?'-':'')+'$'+(a/1e6).toFixed(2)+'M';
  if(a>=1e3)return(n<0?'-$':'$')+a.toLocaleString('en',{maximumFractionDigits:0});
  return(n<0?'-$':'$')+a.toFixed(0)};
const fp=n=>n==null||isNaN(n)?'—':(n>=0?'+':'')+((n)*100).toFixed(2)+'%';
const fa=n=>n==null||isNaN(n)?'—':(n>=0?'+':'')+fm(n);
const f2=n=>n==null||isNaN(n)?'—':n.toFixed(3);
const fpc=n=>n==null||isNaN(n)?'—':(n>=0?'+':'')+((n)*100).toFixed(1)+'%';

// ═══════════════════════════════════════════════════════════════
// UPLOAD
// ═══════════════════════════════════════════════════════════════
document.getElementById('fi').addEventListener('change',e=>{if(e.target.files[0])readFile(e.target.files[0])});
const dz=document.getElementById('dz');
dz.addEventListener('dragover',e=>{e.preventDefault();dz.classList.add('over')});
dz.addEventListener('dragleave',()=>dz.classList.remove('over'));
dz.addEventListener('drop',e=>{e.preventDefault();dz.classList.remove('over');
  if(e.dataTransfer.files[0])readFile(e.dataTransfer.files[0])});

function readFile(f){
  document.getElementById('loading').style.display='flex';
  const rd=new FileReader();
  rd.onload=e=>{
    try{
      const wb=XLSX.read(e.target.result,{type:'array',cellFormula:true,cellNF:false,cellText:false,dense:false});
      D=parse(wb);
      document.getElementById('fname').textContent=f.name;
      init();
    }catch(err){alert('Lỗi đọc file: '+err.message);console.error(err);}
    document.getElementById('loading').style.display='none';
  };
  rd.readAsArrayBuffer(f);
}

// ═══════════════════════════════════════════════════════════════
// AUTO-DETECT COLUMNS
// ═══════════════════════════════════════════════════════════════
function autoDetect(ws){
  const detected={};
  const mRe=/^(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\s*(20\d\d)$/i;
  for(let c=0;c<300;c++){
    const r1=ws[XLSX.utils.encode_cell({r:0,c})];
    if(!r1||typeof r1.v!=='string')continue;
    const label=r1.v.trim();
    if(!mRe.test(label))continue;
    // Find Balance sub-header within +-3 cols in row 2
    for(let off=0;off<=3;off++){
      const r2=ws[XLSX.utils.encode_cell({r:1,c:c+off})];
      if(r2&&typeof r2.v==='string'&&r2.v.trim().startsWith('Balance')){
        detected[label]=c+off+1; // 1-indexed
        break;
      }
    }
    if(!detected[label]) detected[label]=c+1;
  }
  const ORD=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  const sorted=Object.entries(detected).sort((a,b)=>{
    const[am,ay]=a[0].split(' ');const[bm,by]=b[0].split(' ');
    return ay!==by?+ay- +by:ORD.indexOf(am)-ORD.indexOf(bm);
  });
  TCOLS=Object.fromEntries(sorted);
  MONTHS=Object.keys(TCOLS);
}

// ═══════════════════════════════════════════════════════════════
// PARSE
// ═══════════════════════════════════════════════════════════════
function parse(wb){
  const ws=wb.Sheets['Trading'];
  if(!ws)throw new Error('Không tìm thấy sheet "Trading"');
  autoDetect(ws);

  function cv(r,c){
    const x=ws[XLSX.utils.encode_cell({r,c})];
    if(!x)return null;
    if(typeof x.v==='number')return x.v;
    // Eval simple formula like =3000+10000
    const src=x.f||((typeof x.v==='string'&&x.v.startsWith('='))?x.v.slice(1):null);
    if(src){try{const s=src.replace(/[^0-9+\-*\/().]/g,'');if(s)return Function('"use strict";return('+s+')')();}catch(e){}}
    return x.v??null;
  }

  const avail=MONTHS.filter(m=>{
    const c=TCOLS[m]-1;
    for(let r=2;r<90;r++){const v=cv(r,c);if(typeof v==='number'&&v>0)return true;}
    return false;
  });

  const rows=[];
  for(let r=2;r<90;r++){
    const loc=String(cv(r,2)||'').trim();
    const pic=String(cv(r,3)||'').trim();
    if(!loc&&!pic)continue;
    const monthly={},dep={},wd={};
    for(const m of avail){
      const bc=TCOLS[m]-1;
      const bal=cv(r,bc);
      if(typeof bal==='number'&&bal>0){
        monthly[m]=bal;
        const wv=cv(r,bc+2);
        if(typeof wv==='number'&&wv>0)wd[m]=wv;
        const dv=cv(r,bc+3);
        if(typeof dv==='number'&&dv>0)dep[m]=dv;
      }
    }
    if(Object.keys(monthly).length>0)
      rows.push({loc,pic,monthly,dep,wd});
  }

  // Parse Risk sheet
  const risk=parseRisk(wb);
  const hf=parseHF(wb);
  return{rows,months:avail,risk,hf};
}

function parseRisk(wb){
  const RSHEET='Performance & Risk Management R';
  if(!wb.Sheets[RSHEET])return null;
  const ws=wb.Sheets[RSHEET];
  function v(r,c){const x=ws[XLSX.utils.encode_cell({r:r-1,c:c-1})];
    if(!x)return null;
    if(typeof x.v==='number')return x.v;
    return null;}

  // Row 2 = months (B2:L2), Row 3 = Fund NAV, Row 4 = Fund Return
  // Row 7 = Drawdown Fund, Row 11 = BTC price, Row 12 = BTC return
  // Row 14 = BTC running peak, Row 15 = BTC drawdown
  // Row 6 = Fund running peak
  // Rows 17-26 = Risk Metrics
  const months=[], nav=[], fundRet=[], fundDD=[], fundPeak=[];
  const btcPrice=[], btcRet=[], btcDD=[], btcPeak=[];

  for(let c=2;c<=13;c++){
    const mval=ws[XLSX.utils.encode_cell({r:1,c:c-1})];
    if(!mval)continue;
    const dt=mval.v;
    let label='';
    if(dt instanceof Date){
      const mo=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
      label=mo[dt.getMonth()]+' '+dt.getFullYear();
    } else if(typeof dt==='string'){
      label=dt;
    } else if(typeof dt==='number'){
      // Excel date serial
      const d=new Date(Math.round((dt-25569)*86400*1000));
      const mo=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
      label=mo[d.getMonth()]+' '+d.getFullYear();
    }
    months.push(label);
    nav.push(v(3,c));
    fundRet.push(v(4,c));
    fundDD.push(v(7,c));
    fundPeak.push(v(6,c));
    btcPrice.push(v(11,c));
    btcRet.push(v(12,c));
    btcDD.push(v(15,c));
    btcPeak.push(v(14,c));
  }

  const metrics={
    CAGR:   {fund:v(18,2), btc:v(18,3)},
    Vol:    {fund:v(19,2), btc:v(19,3)},
    Sharpe: {fund:v(20,2), btc:v(20,3)},
    Sortino:{fund:v(21,2), btc:v(21,3)},
    MaxDD:  {fund:v(22,2), btc:v(22,3)},
    WorstM: {fund:v(23,2), btc:v(23,3)},
    Calmar: {fund:v(24,2), btc:v(24,3)},
    Beta:   {fund:v(25,2), btc:null},
    Alpha:  {fund:v(26,2), btc:null},
  };

  return{months,nav,fundRet,fundDD,fundPeak,btcPrice,btcRet,btcDD,btcPeak,metrics};
}

// ═══════════════════════════════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════════════════════════════
function gpk(loc){if(!loc)return'Other';const l=loc.toLowerCase();
  if(l.includes('hyperliquid'))return'Hyperliquid';
  if(l.includes('binance'))return'Binance';
  if(l.includes('bybit'))return'Bybit';
  if(l.includes('okx'))return'OKX';
  if(l.includes('lighter'))return'Lighter';
  if(l.includes('decibel'))return'Decibel';
  if(l.includes('mexc'))return'MEXC';
  if(l.includes('polymarket'))return'Polymarket';
  if(l.includes('aark'))return'AARK';
  if(l.includes('test'))return'Test';
  return loc.split(' ')[0]||'Other';}

function platBadge(loc){const k=gpk(loc);
  const cls={Hyperliquid:'b-hl',Binance:'b-bin',Bybit:'b-bybit',OKX:'b-okx',
    Lighter:'b-ltr',Decibel:'b-dcbl',MEXC:'b-mex',Polymarket:'b-poly'}[k]||'b-oth';
  return`<span class="badge ${cls}">${k==='Hyperliquid'?'HL':k}</span>`;}

function picChip(pic){if(!pic)return`<span class="chip c-oth">—</span>`;
  const p=pic.toLowerCase();let cls='c-oth',dc='#8fa3c2';
  if(p.includes('tùng')||p.includes('tung')){cls='c-tung';dc='#ffb84d';}
  else if(p.includes('việt')||p.includes('viet')){cls='c-viet';dc='#4f8eff';}
  else if(p==='nam'){cls='c-nam';dc='#2dd4a0';}
  else if(p.includes('vũ')||p.includes('vu')){cls='c-vufm';dc='#9d7eff';}
  return`<span class="chip ${cls}"><span class="chip-dot" style="background:${dc}"></span>${pic}</span>`;}

function tots(m){let t=0;const bp={},bl={};
  for(const r of D.rows){const v=r.monthly[m]||0;t+=v;
    bp[r.pic]=(bp[r.pic]||0)+v;
    const k=gpk(r.loc);bl[k]=(bl[k]||0)+v;}
  return{total:t,byPic:bp,byPlat:bl};}

function cft(m){let d=0,w=0,p=0;const pm=prev(m);
  for(const r of D.rows){d+=(r.dep[m]||0);w+=(r.wd[m]||0);
    const b=r.monthly[m]||0,pb=pm?r.monthly[pm]||0:0;
    p+=(b-pb+(r.wd[m]||0)-(r.dep[m]||0));}
  return{dep:d,wd:w,pnl:p};}

function prev(m){const i=D.months.indexOf(m);return i>0?D.months[i-1]:null;}
function dc(id){if(CH[id]){CH[id].destroy();delete CH[id];}}
const mPics=()=>Object.keys(PC).filter(p=>D.months.some(m=>(tots(m).byPic[p]||0)>0));

const co=()=>({responsive:true,maintainAspectRatio:false,animation:{duration:300},
  plugins:{legend:{display:false},tooltip:{backgroundColor:'#0c0f1a',borderColor:'#1c2438',
    borderWidth:1,titleColor:'#6b7fa3',bodyColor:'#dde4f0',padding:10,
    titleFont:{family:"'JetBrains Mono'",size:9},bodyFont:{family:"'JetBrains Mono'",size:11},
    callbacks:{label:ctx=>` ${fm(ctx.raw)}`}}},
  scales:{x:{ticks:{color:'#3d4f6e',font:{family:"'JetBrains Mono'",size:8},maxRotation:45},
    grid:{color:'rgba(28,36,56,.5)'},border:{color:'#1c2438'}},
  y:{ticks:{color:'#3d4f6e',font:{family:"'JetBrains Mono'",size:8},callback:v=>fm(v)},
    grid:{color:'rgba(28,36,56,.5)'},border:{color:'#1c2438'}}}});

const doo=()=>({responsive:true,maintainAspectRatio:false,animation:{duration:300},cutout:'65%',
  plugins:{legend:{position:'right',labels:{color:'#6b7fa3',font:{family:"'JetBrains Mono'",size:10},padding:10,boxWidth:10}},
  tooltip:{backgroundColor:'#0c0f1a',borderColor:'#1c2438',borderWidth:1,
    titleColor:'#6b7fa3',bodyColor:'#dde4f0',
    titleFont:{family:"'JetBrains Mono'",size:9},bodyFont:{family:"'JetBrains Mono'",size:11}}}});


// ═══════════════════════════════════════════════════════════════
// PARSE COPIN HF SHEET
// ═══════════════════════════════════════════════════════════════
function parseHF(wb) {
  const ws = wb.Sheets['Copin HF'];
  if (!ws) return null;

  // Read numeric value from cell (handles cached formula values)
  function cv(r, c) {
    const x = ws[XLSX.utils.encode_cell({r: r-1, c: c-1})];
    if (!x) return null;
    if (typeof x.v === 'number') return x.v;
    return null;
  }
  // Read cell as trimmed string
  function cs(r, c) {
    const x = ws[XLSX.utils.encode_cell({r: r-1, c: c-1})];
    return x ? String(x.v ?? '').trim() : '';
  }

  // ── Step 1: Find month columns by scanning row 3 for 3-letter month names ──
  // Year context from row 2 integer cells (2025/2026)
  const MONS = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  const MONTH_COLS = {}; // label → 1-indexed col
  const months = [];
  let curYear = 2025;

  for (let c = 1; c <= 30; c++) {
    // Detect year from row 2 — integer cells 2025 or 2026
    const yr = cv(2, c);
    if (yr !== null && yr >= 2020 && yr <= 2035) curYear = Math.round(yr);

    // Check row 3 for month name
    const raw = cs(3, c);
    const mon = raw.slice(0,3);
    if (MONS.some(m => m.toLowerCase() === mon.toLowerCase())) {
      const label = mon.charAt(0).toUpperCase() + mon.slice(1).toLowerCase() + ' ' + curYear;
      if (!MONTH_COLS[label]) { MONTH_COLS[label] = c; months.push(label); }
    }
  }

  // ── Step 2: If year detection failed (XLSX.js can't read int cells), use positional fallback ──
  // Pattern: cols C(3)=Apr2025, D(4)=May2025 ... K(11)=Dec2025, L(12)=Jan2026, M(13)=Feb2026, N(14)=Mar2026
  // Detect by checking if months were assigned correct years
  const hasWrongYear = months.some(m => {
    const [mn, yr] = m.split(' ');
    const idx = MONS.indexOf(mn); // Apr=3, May=4 ... Dec=11
    const col = MONTH_COLS[m];
    // Apr-Dec should be in cols 3-11 (2025), Jan-Mar in cols 12-14 (2026)
    const expectedYr = (col >= 12 && idx <= 2) ? '2026' : '2025';
    return yr !== expectedYr;
  });

  if (months.length < 2 || hasWrongYear) {
    // Rebuild with hardcoded year assignment based on col position
    months.length = 0;
    Object.keys(MONTH_COLS).forEach(k => delete MONTH_COLS[k]);
    for (let c = 1; c <= 30; c++) {
      const raw = cs(3, c);
      const mon = raw.slice(0,3);
      if (!MONS.some(m => m.toLowerCase() === mon.toLowerCase())) continue;
      const normalMon = MONS.find(m => m.toLowerCase() === mon.toLowerCase());
      // Positional year: cols where Jan/Feb/Mar appear AFTER Oct/Nov/Dec = 2026
      // Simple rule: if this mon index < previous mon index, year incremented
      const prevLabel = months[months.length - 1];
      const prevMon = prevLabel ? MONS.indexOf(prevLabel.split(' ')[0]) : -1;
      const curMon  = MONS.indexOf(normalMon);
      if (prevMon >= 0 && curMon < prevMon) curYear++;
      const label = normalMon + ' ' + curYear;
      if (!MONTH_COLS[label]) { MONTH_COLS[label] = c; months.push(label); }
    }
  }



  // ── Step 3: Find Change columns (MoM$, MoM%, YoY$, YoY%) ──
  // Search row 3 for header text after the last month column
  let momBalCol=null, momPctCol=null, yoyBalCol=null, yoyPctCol=null;
  const lastMonCol = Math.max(...Object.values(MONTH_COLS));
  for (let c = lastMonCol+1; c <= lastMonCol+10; c++) {
    const h = cs(3, c).toLowerCase();
    if      (h.includes('mom') && h.includes('bal')) momBalCol = c;
    else if (h.includes('mom') && (h.includes('%') || h.includes('pct') || h === 'mom (%)')) momPctCol = c;
    else if (h.includes('yoy') && h.includes('bal')) yoyBalCol = c;
    else if (h.includes('yoy') && (h.includes('%') || h.includes('pct') || h === 'yoy (%)')) yoyPctCol = c;
    // Also catch by position: first 4 non-empty cols after months
    if (momBalCol && momPctCol && yoyBalCol && yoyPctCol) break;
  }
  // Positional fallback: +1,+2,+3,+4 after last month col (skip empty cols)
  if (!momBalCol) {
    let found = 0;
    for (let c = lastMonCol+1; c <= lastMonCol+8; c++) {
      const val = cv(45, c); // check if row 45 has a value here
      if (val !== null) {
        found++;
        if (found===1) momBalCol=c;
        else if (found===2) momPctCol=c;
        else if (found===3) yoyBalCol=c;
        else if (found===4) { yoyPctCol=c; break; }
      }
    }
  }


  // ── Step 4: Build row helper ──
  function row(rowNum, label, type='normal', parent=null) {
    const data={};
    for (const [m,c] of Object.entries(MONTH_COLS)) {
      const val = cv(rowNum, c);
      data[m] = val !== null ? val : 0;
    }
    return { label, type, parent, data,
      mom:    momBalCol ? cv(rowNum, momBalCol) : null,
      momPct: momPctCol ? cv(rowNum, momPctCol) : null,
      yoy:    yoyBalCol ? cv(rowNum, yoyBalCol) : null,
      yoyPct: yoyPctCol ? cv(rowNum, yoyPctCol) : null,
    };
  }

  // ── Step 5: Build all rows ──
  const rows = [
    row(5,  'Asset',                     'section'),
    row(6,  'Non-custodial Wallet',      'sub',      'Asset'),
    row(7,  'USDC+USDT',                 'sub',      'Asset'),
    row(8,  'CEX Asset',                 'sub',      'Asset'),
    row(9,  'HYPE',                      'sub',      'Asset'),
    row(10, 'Hedge Trading',             'section'),
    row(11, 'Copin IF (HL) - Tùng',     'sub',      'Hedge Trading'),
    row(12, 'Copin IF (HL) - Việt',     'sub',      'Hedge Trading'),
    row(13, 'Copin IF (HMX) - Việt',    'sub',      'Hedge Trading'),
    row(14, 'Copin IF (HL) - Vũ FM',    'sub',      'Hedge Trading'),
    row(15, 'Copin IF (HL) - Nam',      'sub',      'Hedge Trading'),
    row(16, 'Algo Trading',              'section'),
    row(17, 'CEX Copy MM',               'sub',      'Algo Trading'),
    row(18, 'DEX Copy MM',               'sub',      'Algo Trading'),
    row(19, 'Copin IF (Lighter) - Việt','sub',       'Algo Trading'),
    row(20, 'Copin IF (Lighter) - Nam', 'sub',       'Algo Trading'),
    row(21, 'Copin IF (Lighter) - Tùng','sub',       'Algo Trading'),
    row(22, 'Copin IF (Lighter) - Vũ FM','sub',      'Algo Trading'),
    row(23, 'Lite Copin',                'sub',      'Algo Trading'),
    row(24, 'Prediction Market',         'section'),
    row(25, 'Poly Flash - Việt',         'sub',      'Prediction Market'),
    row(26, 'Poly Market - Nam',         'sub',      'Prediction Market'),
    row(27, 'Poly Market - Vũ FM',       'sub',      'Prediction Market'),
    row(28, 'Equity',                    'equity'),
    row(29, 'Loan',                      'loan'),
    row(30, 'Binance Loan',              'sub-loan', 'Loan'),
    row(31, '- Loan (Tùng)',             'sub-loan', 'Loan'),
    row(32, '- Loan (Việt)',             'sub-loan', 'Loan'),
    row(33, '- Loan (Nam)',              'sub-loan', 'Loan'),
    row(34, 'Tung Loan',                 'sub-loan', 'Loan'),
    row(35, 'AAVE Loan',                 'sub-loan', 'Loan'),
    row(36, 'Accrued Interest ($)',      'sub-loan', 'Loan'),
    row(37, 'HYPE Loan',                 'sub-loan', 'Loan'),
    row(38, 'Fee',                       'fee'),
    row(39, 'Buy Subscription (Copin)',  'sub-fee',  'Fee'),
    row(40, 'Management Fee',            'sub-fee',  'Fee'),
    row(41, 'Performance Fee',           'sub-fee',  'Fee'),
    row(42, 'Other Fee',                 'sub-fee',  'Fee'),
    row(43, 'Accrued Interest',          'sub-fee',  'Fee'),
    row(44, 'Net Profit',                'profit'),
    row(45, 'Total (NAV)',               'total'),
  ];

  return { months, rows, MONTH_COLS };
}


// ═══════════════════════════════════════════════════════════════
// FUND OVERVIEW RENDER
// ═══════════════════════════════════════════════════════════════
function rFo() {
  const HF = D.hf;
  if (!HF) {
    document.getElementById('p-fundoverview').innerHTML =
      '<div style="text-align:center;padding:60px;color:var(--sub);font-family:var(--mono)">⚠ Không tìm thấy sheet "Copin HF"</div>';
    return;
  }

  const ms = HF.months;
  const lastM = ms[ms.length - 1];
  document.getElementById('fo-last-month').textContent = SELM;

  // Helper: get value for row by label
  // Map SELM (Trading format) to HF month key
  const hfMonthKey = (m) => {
    if (!m) return m;
    // Direct check in months array (works for both file-parsed and embedded)
    const found = HF.months.find(hm => hm === m);
    return found || m;
  };
  const gv = (label, month) => {
    const r = HF.rows.find(x => x.label === label);
    if (!r) return 0;
    const key = hfMonthKey(month);
    return r.data[key] || 0;
  };

  // KPI cards from last month
  const nav    = gv('Total (NAV)', SELM);
  const profit = gv('Net Profit', SELM);
  const asset  = gv('Asset', SELM);
  const hedge  = gv('Hedge Trading', SELM);
  const algo   = gv('Algo Trading', SELM);
  const pred   = gv('Prediction Market', SELM);
  const assetTotal = asset + hedge + algo + pred;
  const loan   = gv('Loan', SELM);
  const fee    = gv('Fee', SELM);
  const equity = gv('Equity', SELM);

  // Compute MoM and YoY from HF data based on SELM
  const selHFKey = hfMonthKey(SELM);
  const selHFIdx = HF.months.indexOf(selHFKey);
  const prevHFKey = selHFIdx > 0 ? HF.months[selHFIdx - 1] : null;
  const navPrev = prevHFKey ? gv('Total (NAV)', prevHFKey) : null;
  const momAmt  = navPrev !== null ? nav - navPrev : null;
  const momPct  = navPrev && navPrev > 0 ? (nav - navPrev) / navPrev : null;
  const profPrev  = prevHFKey ? gv('Net Profit', prevHFKey) : null;
  const profMomA  = profPrev !== null ? profit - profPrev : null;
  const profMomP  = profPrev && profPrev !== 0 ? (profit - profPrev) / Math.abs(profPrev) : null;
  // YoY: fixed base = NAV of Apr 2025 (inception month = first month in HF data)
  const baseMonth = HF.months[0]; // Apr 2025
  const navBase = gv('Total (NAV)', baseMonth);
  const yoyAmt = navBase ? nav - navBase : null;
  const yoyPct = navBase && navBase > 0 ? (nav - navBase) / navBase : null;
  const navRow  = HF.rows.find(r => r.label === 'Total (NAV)');
  const profRow = HF.rows.find(r => r.label === 'Net Profit');

  document.getElementById('fo-kpi').innerHTML = `
    <div class="kpi"><div class="kpi-bar" style="background:linear-gradient(90deg,var(--acc),var(--acc2))"></div>
      <div class="kpi-lbl">Total NAV</div><div class="kpi-val">${fm(nav)}</div>
      <div class="kpi-foot">${SELM}</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:${profit>=0?'var(--grn)':'var(--red)'}"></div>
      <div class="kpi-lbl">Net Profit</div>
      <div class="kpi-val ${profit>=0?'pos':'neg'}">${fm(profit)}</div>
      <div class="kpi-foot">MoM: ${profMomA!=null?fa(profMomA):'—'} ${profMomP!=null?'('+fp(profMomP)+')':''}</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:var(--gold)"></div>
      <div class="kpi-lbl">Total Assets</div><div class="kpi-val">${fm(assetTotal)}</div>
      <div class="kpi-foot">Asset + Hedge + Algo + Predict</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:var(--sky)"></div>
      <div class="kpi-lbl">Equity</div><div class="kpi-val">${fm(equity)}</div>
      <div class="kpi-foot">Fund capital</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:var(--rose)"></div>
      <div class="kpi-lbl">Total Loan</div>
      <div class="kpi-val ${loan>0?'neg':''}">${fm(loan)}</div>
      <div class="kpi-foot">Outstanding debt</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:var(--gold)"></div>
      <div class="kpi-lbl">Total Fee Paid</div>
      <div class="kpi-val">${fm(fee)}</div>
      <div class="kpi-foot">Cumulative fees</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:${momPct!=null&&momPct>=0?'var(--grn)':'var(--red)'}"></div>
      <div class="kpi-lbl">NAV MoM</div>
      <div class="kpi-val ${momPct!=null&&momPct>=0?'pos':'neg'}">${momPct!=null?fp(momPct):'—'}</div>
      <div class="kpi-foot">${momAmt!=null?fa(momAmt):''}</div></div>
    <div class="kpi"><div class="kpi-bar" style="background:var(--pur)"></div>
      <div class="kpi-lbl">NAV YoY</div>
      <div class="kpi-val ${yoyPct!=null&&yoyPct>=0?'pos':'neg'}">${yoyPct!=null?fp(yoyPct):'—'}</div>
      <div class="kpi-foot">${yoyAmt!=null?fa(yoyAmt):''}</div></div>
  `;

  // Chart 1: NAV & Net Profit line
  dc('c-fo-nav');
  CH['c-fo-nav'] = new Chart(document.getElementById('c-fo-nav'), {
    type: 'line',
    data: {
      labels: ms,
      datasets: [
        { label: 'Total NAV', data: ms.map(m => gv('Total (NAV)', m)),
          borderColor: '#4f8eff', backgroundColor: 'rgba(79,142,255,.1)',
          borderWidth: 2.5, fill: true, tension: .4, pointRadius: 3, yAxisID: 'y' },
        { label: 'Net Profit', data: ms.map(m => gv('Net Profit', m)),
          borderColor: '#2dd4a0', backgroundColor: 'transparent',
          borderWidth: 2, fill: false, tension: .4, pointRadius: 2,
          borderDash: [4, 3], yAxisID: 'y' },
        { label: 'Loan', data: ms.map(m => gv('Loan', m)),
          borderColor: '#ff5f87', backgroundColor: 'transparent',
          borderWidth: 1.5, fill: false, tension: .4, pointRadius: 2,
          borderDash: [2, 3], yAxisID: 'y' },
      ]
    },
    options: { ...co(), plugins: { ...co().plugins, legend: { display: true,
      labels: { color: '#6b7fa3', font: { family: "'JetBrains Mono'", size: 10 }, boxWidth: 10 } } } }
  });

  // Chart 2: Breakdown donut for selected month
  dc('c-fo-break');
  const hedgeS  = gv('Hedge Trading', SELM);
  const algoS   = gv('Algo Trading', SELM);
  const predict = gv('Prediction Market', SELM);
  const assetM  = gv('Asset', SELM);
  const loanM   = gv('Loan', SELM);
  const feeM    = gv('Fee', SELM);
  CH['c-fo-break'] = new Chart(document.getElementById('c-fo-break'), {
    type: 'doughnut',
    data: {
      labels: ['Asset', 'Hedge Trading', 'Algo Trading', 'Prediction', 'Loan', 'Fee'],
      datasets: [{ data: [assetM, hedgeS, algoS, predict, loanM, feeM],
        backgroundColor: ['rgba(255,184,77,.7)','rgba(79,142,255,.7)','rgba(45,212,160,.7)',
          'rgba(157,126,255,.7)','rgba(255,95,135,.5)','rgba(255,184,77,.4)'],
        borderColor: '#07090f', borderWidth: 3, hoverOffset: 8 }]
    },
    options: { ...doo(), plugins: { ...doo().plugins, tooltip: { ...doo().plugins.tooltip,
      callbacks: { label: ctx => ` ${ctx.label}: ${fm(ctx.parsed)}` } } } }
  });

  // Chart 3: Asset stacked area
  dc('c-fo-asset');
  CH['c-fo-asset'] = new Chart(document.getElementById('c-fo-asset'), {
    type: 'bar',
    data: {
      labels: ms,
      datasets: [
        { label: 'Asset', data: ms.map(m => gv('Asset', m)),
          backgroundColor: 'rgba(255,184,77,.6)', borderColor: '#ffb84d', borderWidth: 1, borderRadius: 3 },
        { label: 'Hedge Trading', data: ms.map(m => gv('Hedge Trading', m)),
          backgroundColor: 'rgba(79,142,255,.6)', borderColor: '#4f8eff', borderWidth: 1, borderRadius: 3 },
        { label: 'Algo Trading', data: ms.map(m => gv('Algo Trading', m)),
          backgroundColor: 'rgba(45,212,160,.6)', borderColor: '#2dd4a0', borderWidth: 1, borderRadius: 3 },
        { label: 'Prediction', data: ms.map(m => gv('Prediction Market', m)),
          backgroundColor: 'rgba(157,126,255,.6)', borderColor: '#9d7eff', borderWidth: 1, borderRadius: 3 },
      ]
    },
    options: { ...co(), plugins: { ...co().plugins, legend: { display: true,
      labels: { color: '#6b7fa3', font: { family: "'JetBrains Mono'", size: 10 }, boxWidth: 10 } } } }
  });

  // Chart 4: Loan history
  dc('c-fo-loan');
  const loanTypes = [
    { label: 'Binance Loan', color: 'rgba(255,184,77,.65)' },
    { label: 'Tung Loan',    color: 'rgba(79,142,255,.65)' },
    { label: 'AAVE Loan',    color: 'rgba(45,212,160,.65)' },
    { label: 'HYPE Loan',    color: 'rgba(255,95,135,.55)' },
  ];
  CH['c-fo-loan'] = new Chart(document.getElementById('c-fo-loan'), {
    type: 'bar',
    data: {
      labels: ms,
      datasets: loanTypes.map(lt => ({
        label: lt.label,
        data: ms.map(m => gv(lt.label, m)),
        backgroundColor: lt.color, borderWidth: 1, borderRadius: 3
      }))
    },
    options: { ...co(), plugins: { ...co().plugins, legend: { display: true,
      labels: { color: '#6b7fa3', font: { family: "'JetBrains Mono'", size: 10 }, boxWidth: 10 } } } }
  });

  // Build main table
  const thead = document.getElementById('fo-thead');
  const tbody = document.getElementById('fo-tbody');

  // Header row
  thead.innerHTML = `<tr>
    <th style="min-width:220px;text-align:left">Category</th>
    ${ms.map(m => `<th>${m}</th>`).join('')}
    <th class="fo-th-change">MoM $</th>
    <th class="fo-th-change">MoM %</th>
    <th class="fo-th-change">YoY $</th>
    <th class="fo-th-change">YoY %</th>
  </tr>`;

  // Row renderer
  const TYPE_CLASS = {
    section: 'fo-row-section',
    equity:  'fo-row-equity',
    loan:    'fo-row-loan',
    'sub-loan': 'fo-row-sub fo-row-loan',
    fee:     'fo-row-fee',
    'sub-fee': 'fo-row-sub fo-row-fee',
    profit:  'fo-row-profit',
    total:   'fo-row-total',
    sub:     'fo-row-sub',
    normal:  '',
  };

  const fCell = (val, type) => {
    if (val == null || val === 0) return `<td class="dim">—</td>`;
    const cls = val > 0 ? 'fo-cell-pos' : 'fo-cell-neg';
    if (type === 'total' || type === 'profit')
      return `<td class="fo-cell-highlight">${fm(val)}</td>`;
    if (type === 'loan' || type === 'sub-loan')
      return `<td class="${val > 0 ? 'fo-cell-neg' : ''}">${fm(val)}</td>`;
    return `<td class="${val < 0 ? 'fo-cell-neg' : ''}">${fm(val)}</td>`;
  };
  const fChange = (val, isPct) => {
    if (val == null || (Math.abs(val) < 0.000001)) return `<td class="fo-td-change dim">—</td>`;
    const cls = val >= 0 ? 'fo-cell-pos' : 'fo-cell-neg';
    const str = isPct ? fp(val) : fa(val);
    return `<td class="fo-td-change ${cls}">${str}</td>`;
  };

  tbody.innerHTML = HF.rows.map(r => {
    const cls = TYPE_CLASS[r.type] || '';
    const cells = ms.map(m => fCell(r.data[m], r.type)).join('');
    return `<tr class="${cls}">
      <td>${r.label}</td>
      ${cells}
      ${fChange(r.mom, false)}
      ${fChange(r.momPct, true)}
      ${fChange(r.yoy, false)}
      ${fChange(r.yoyPct, true)}
    </tr>`;
  }).join('');
}

// ═══════════════════════════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════════════════════════

function init(){
  document.getElementById('upload-screen').style.display='none';
  document.getElementById('dash').style.display='flex';
  const ms=D.hf?D.hf.months:D.months;
  const sel=document.getElementById('msel');
  sel.innerHTML=ms.map(m=>`<option value="${m}">${m}</option>`).join('');
  sel.value=ms[ms.length-1]; SELM=sel.value;
  document.querySelectorAll('.tab').forEach(t=>{
    t.addEventListener('click',()=>{
      document.querySelectorAll('.tab').forEach(x=>x.classList.remove('active'));
      document.querySelectorAll('.panel').forEach(x=>x.classList.remove('active'));
      t.classList.add('active');
      document.getElementById('p-'+t.dataset.tab).classList.add('active');
    });
  });
  renderAll();
}
function onM(){SELM=document.getElementById('msel').value;renderAll();}
function renderAll(){rOv();rPerf();rRiskTab();rPort();rVol();if(D.live)rLive();}

function hfGet(label,month){
  if(!D.hf||!D.hf.rows)return 0;
  const r=D.hf.rows.find(x=>x.label===label);
  if(!r||!r.data)return 0;
  return r.data[month]||0;
}
function hfSel(month){
  if(!D.hf)return month;
  return D.hf.months.find(m=>m===month)||D.hf.months[D.hf.months.length-1];
}

// ═══════════════════════════════════════════════════════
// OVERVIEW
// ═══════════════════════════════════════════════════════
function rOv(){
  const HF=D.hf, R=D.risk;
  const sel=hfSel(SELM);
  const hfMs=HF?HF.months:[];
  const hfNav=hfMs.map(m=>hfGet('Total (NAV)',m)||0);
  const base=HF?HF.months[0]:null;
  const prevM=HF?HF.months[HF.months.indexOf(sel)-1]:null;
  const nav=hfGet('Total (NAV)',sel);
  const navBase=base?hfGet('Total (NAV)',base):null;
  const navPrev=prevM?hfGet('Total (NAV)',prevM):null;
  const profit=hfGet('Net Profit',sel);
  const mom=navPrev&&navPrev>0?(nav-navPrev)/navPrev:null;
  const yoy=navBase&&navBase>0?(nav-navBase)/navBase:null;
  const curDD=R?(R.fundDD[R.fundDD.length-1]||0):null;
  const sharpe=R?.metrics?.Sharpe?.fund??null;
  const maxDD=R?.metrics?.MaxDD?.fund??null;
  const cagr=R?.metrics?.CAGR?.fund??null;
  const alpha=R?.metrics?.Alpha?.fund??null;
  const calmar=R?.metrics?.Calmar?.fund??null;
  const sortino=R?.metrics?.Sortino?.fund??null;
  const beta=R?.metrics?.Beta?.fund??null;
  const cur=tots(SELM);
  const byPic=cur.byPic, total=cur.total;

  document.getElementById('p-overview').innerHTML=`
  <div style="display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:24px;flex-wrap:wrap;gap:12px">
    <div>
      <div style="font-family:var(--mono);font-size:9px;letter-spacing:3px;color:var(--sub);margin-bottom:6px">MONTHLY REPORT · ${sel.toUpperCase()}</div>
      <div style="font-size:24px;font-weight:700;letter-spacing:-.6px">Copin IF — Fund Performance Summary</div>
    </div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      ${[
        [sharpe, `Sharpe ${sharpe!=null?sharpe.toFixed(2):'—'}`, sharpe>=1?'grn':sharpe>0?'gold':'red'],
        [curDD,  `DD ${fp(curDD)}`, curDD<-0.2?'red':curDD<-0.05?'gold':'grn'],
        [cagr,   `CAGR ${fp(cagr)}`, cagr>0?'acc':'red'],
      ].map(([v,txt,col])=>v!=null?`<span style="padding:5px 14px;border-radius:20px;font-family:var(--mono);font-size:11px;font-weight:600;
        background:${col==='grn'?'rgba(45,212,160,.12)':col==='gold'?'rgba(255,184,77,.1)':col==='acc'?'rgba(79,142,255,.12)':'rgba(255,77,109,.12)'};
        color:var(--${col==='acc'?'acc':col});border:1px solid ${col==='grn'?'rgba(45,212,160,.25)':col==='gold'?'rgba(255,184,77,.2)':col==='acc'?'rgba(79,142,255,.25)':'rgba(255,77,109,.2)'}">${txt}</span>`:'').join('')}
    </div>
  </div>

  <!-- KPI STRIP -->
  <div style="display:grid;grid-template-columns:2fr 1fr 1fr 1fr 1fr;gap:10px;margin-bottom:16px">
    ${[
      {l:'Total NAV',     display:fm(nav),      sub:`MoM ${mom!=null?fp(mom):'—'}`,   sub2:`Since Inception ${yoy!=null?fp(yoy):'—'}`, c:'var(--acc)', big:true},
      {l:'Net Profit',    display:fm(profit),    sub:`vs Base ${navBase&&navBase>0?fp((nav-navBase)/navBase):'—'}`,                      c:profit>=0?'var(--grn)':'var(--red)'},
      {l:'CAGR',          display:fp(cagr),      sub:'Annualized',                                                                       c:'var(--acc)'},
      {l:'Alpha vs BTC',  display:fp(alpha),     sub:'Excess return',                                                                    c:alpha!=null&&alpha>=0?'var(--grn)':'var(--red)'},
      {l:'Max Drawdown',  display:fp(maxDD),     sub:'From peak',                                                                        c:maxDD!=null&&maxDD<-0.3?'var(--red)':maxDD!=null&&maxDD<-0.15?'var(--gold)':'var(--txt)'},
    ].map(k=>`
      <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px;position:relative;overflow:hidden">
        <div style="position:absolute;top:0;left:0;right:0;height:2.5px;background:${k.c}"></div>
        <div style="font-family:var(--mono);font-size:8px;letter-spacing:1.5px;color:var(--sub);text-transform:uppercase;margin-bottom:8px">${k.l}</div>
        <div style="font-size:${k.big?'28px':'20px'};font-weight:700;letter-spacing:-1px;line-height:1;color:${k.c}">${k.display}</div>
        <div style="font-family:var(--mono);font-size:9px;color:var(--sub);margin-top:8px">${k.sub}</div>
        ${k.sub2?`<div style="font-family:var(--mono);font-size:9px;color:var(--sub);margin-top:2px">${k.sub2}</div>`:''}
      </div>`).join('')}
  </div>

  <!-- MIDDLE ROW: Chart + Risk snapshot -->
  <div style="display:grid;grid-template-columns:3fr 1fr;gap:12px;margin-bottom:14px">
    <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px">
      <div style="display:flex;justify-content:space-between;align-items:baseline;margin-bottom:10px">
        <div><div style="font-size:12px;font-weight:600">NAV History</div><div style="font-family:var(--mono);font-size:9px;color:var(--sub);margin-top:2px">${base} → ${hfMs[hfMs.length-1]}</div></div>
        <div style="font-family:var(--mono);font-size:10px;color:var(--sub)">Peak <strong style="color:var(--txt)">${fm(Math.max(...hfNav.filter(v=>v>0))||0)}</strong></div>
      </div>
      <div style="position:relative;height:140px"><canvas id="c-ov-nav"></canvas></div>
    </div>
    <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px">
      <div style="font-size:12px;font-weight:600;margin-bottom:14px">Risk Snapshot</div>
      <div style="display:flex;flex-direction:column;gap:9px">
        ${[
          ['Sharpe', sharpe!=null?sharpe.toFixed(2):'—', sharpe!=null&&sharpe>=1?'var(--grn)':sharpe!=null&&sharpe>0?'var(--gold)':'var(--red)'],
          ['Sortino', sortino!=null?sortino.toFixed(2):'—', 'var(--acc)'],
          ['Calmar', calmar!=null?calmar.toFixed(2):'—', 'var(--acc)'],
          ['Max DD', fp(maxDD), maxDD!=null&&maxDD<-0.3?'var(--red)':maxDD!=null&&maxDD<-0.15?'var(--gold)':'var(--txt)'],
          ['Beta', beta!=null?beta.toFixed(2):'—', 'var(--sub)'],
        ].map(([l,v,c])=>`<div style="display:flex;justify-content:space-between;align-items:center">
          <span style="font-size:11px;color:var(--sub)">${l}</span>
          <span style="font-family:var(--mono);font-size:12px;font-weight:600;color:${c}">${v}</span>
        </div>`).join('')}
      </div>
    </div>
  </div>

  <!-- BOTTOM ROW: Trader bars + Monthly heatmap -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
    <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px">
      <div style="display:flex;justify-content:space-between;align-items:baseline;margin-bottom:14px">
        <div style="font-size:12px;font-weight:600">Trader Allocation</div>
        <div style="font-family:var(--mono);font-size:9px;color:var(--sub)">${SELM} · ${fm(total)}</div>
      </div>
      <div style="display:flex;flex-direction:column;gap:10px">
        ${Object.entries(byPic).filter(([,v])=>v>0).sort((a,b)=>b[1]-a[1]).map(([p,v])=>{
          const pct=total>0?v/total:0;
          const c=(PC[p]||{c:'#8fa3c2'}).c;
          return`<div>
            <div style="display:flex;justify-content:space-between;margin-bottom:5px">
              <span style="font-size:11px;font-weight:500">${p}</span>
              <span style="font-family:var(--mono);font-size:10px"><span style="color:var(--sub)">${fm(v)}</span> <span style="color:${c}">${(pct*100).toFixed(1)}%</span></span>
            </div>
            <div style="height:3px;background:var(--s3);border-radius:2px">
              <div style="width:${(pct*100).toFixed(1)}%;height:100%;border-radius:2px;background:${c}"></div>
            </div>
          </div>`;
        }).join('')}
      </div>
    </div>
    <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px">
      <div style="font-size:12px;font-weight:600;margin-bottom:4px">Monthly Returns</div>
      <div style="font-family:var(--mono);font-size:9px;color:var(--sub);margin-bottom:12px">Fund return heatmap · ${base} → ${hfMs[hfMs.length-1]}</div>
      <div style="display:grid;grid-template-columns:repeat(6,1fr);gap:5px">
        ${hfMs.slice(1).map((m,i)=>{
          const ret=hfNav[i]>0?(hfNav[i+1]-hfNav[i])/hfNav[i]:0;
          const intensity=Math.min(Math.abs(ret)/0.45,1);
          const bg=ret>=0?`rgba(45,212,160,${0.1+intensity*0.6})`:`rgba(255,77,109,${0.1+intensity*0.6})`;
          const clr=ret>=0?'#2dd4a0':'#ff4d6d';
          return`<div style="padding:7px 3px;border-radius:6px;background:${bg};text-align:center;
            border:1px solid ${ret>=0?'rgba(45,212,160,.15)':'rgba(255,77,109,.15)'}" title="${m}: ${fp(ret)}">
            <div style="font-family:var(--mono);font-size:7px;color:var(--sub);margin-bottom:3px">${m.slice(0,3)} ${m.slice(-2)}</div>
            <div style="font-family:var(--mono);font-size:10px;font-weight:700;color:${clr}">${ret>=0?'+':''}${(ret*100).toFixed(1)}%</div>
          </div>`;
        }).join('')}
      </div>
    </div>
  </div>`;

  dc('c-ov-nav');
  CH['c-ov-nav']=new Chart(document.getElementById('c-ov-nav'),{type:'line',
    data:{labels:hfMs,datasets:[{data:hfNav,borderColor:'#4f8eff',
      backgroundColor:(ctx)=>{const g=ctx.chart.ctx.createLinearGradient(0,0,0,140);
        g.addColorStop(0,'rgba(79,142,255,.2)');g.addColorStop(1,'rgba(79,142,255,0)');return g;},
      borderWidth:2,fill:true,tension:.4,pointRadius:2,pointHoverRadius:5}]},
    options:{responsive:true,maintainAspectRatio:false,animation:{duration:300},
      plugins:{legend:{display:false},tooltip:{backgroundColor:'#0c0f1a',borderColor:'#1c2438',
        borderWidth:1,bodyColor:'#dde4f0',bodyFont:{family:"'JetBrains Mono'",size:11},
        callbacks:{label:ctx=>` ${fm(ctx.raw)}`}}},
      scales:{x:{ticks:{color:'#3d4f6e',font:{size:8,family:"'JetBrains Mono'"},maxRotation:0},
          grid:{color:'rgba(28,36,56,.4)'},border:{color:'#1c2438'}},
        y:{ticks:{color:'#3d4f6e',font:{size:8,family:"'JetBrains Mono'"},callback:v=>fm(v)},
          grid:{color:'rgba(28,36,56,.4)'},border:{color:'#1c2438'}}}}});
}

// ═══════════════════════════════════════════════════════
// PERFORMANCE TAB
// ═══════════════════════════════════════════════════════
function rPerf(){
  const HF=D.hf,R=D.risk;
  if(!R){document.getElementById('p-performance').innerHTML='<div style="padding:60px;text-align:center;color:var(--sub)">Risk data not available</div>';return;}
  const ms=R.months, hfMs=HF?HF.months:[];
  const base=R.nav[0]||1, btcBase=R.btcPrice[0]||1;

  dc('c-pf-nav');
  CH['c-pf-nav']=new Chart(document.getElementById('c-pf-nav'),{type:'line',data:{labels:ms,
    datasets:[
      {label:'Fund NAV',data:R.nav,borderColor:'#4f8eff',
        backgroundColor:(ctx)=>{const g=ctx.chart.ctx.createLinearGradient(0,0,0,260);
          g.addColorStop(0,'rgba(79,142,255,.18)');g.addColorStop(1,'rgba(79,142,255,0)');return g;},
        borderWidth:2.5,fill:true,tension:.4,pointRadius:2,pointHoverRadius:5},
      {label:'High-Water Mark',data:R.fundPeak,borderColor:'rgba(45,212,160,.7)',
        backgroundColor:'transparent',borderWidth:1.5,borderDash:[5,4],
        fill:false,tension:.4,pointRadius:0,pointHoverRadius:4},
    ]},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${fm(ctx.raw)}`}}}}});

  dc('c-pf-ret');
  CH['c-pf-ret']=new Chart(document.getElementById('c-pf-ret'),{type:'bar',data:{labels:ms,
    datasets:[
      {label:'Fund',data:R.fundRet.map(v=>v!=null?+(v*100).toFixed(2):null),
        backgroundColor:R.fundRet.map(v=>v!=null&&v>=0?'rgba(79,142,255,.75)':'rgba(255,77,109,.7)'),borderRadius:3},
      {label:'BTC', data:R.btcRet.map(v=>v!=null?+(v*100).toFixed(2):null),
        backgroundColor:'rgba(255,184,77,.45)',borderRadius:3},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'%':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'%'}}}}});

  dc('c-pf-dd');
  CH['c-pf-dd']=new Chart(document.getElementById('c-pf-dd'),{type:'line',data:{labels:ms,
    datasets:[
      {label:'Fund',data:R.fundDD.map(v=>v!=null?+(v*100).toFixed(2):null),
        borderColor:'#ff4d6d',backgroundColor:'rgba(255,77,109,.1)',fill:true,tension:.3,borderWidth:2,pointRadius:2},
      {label:'BTC', data:R.btcDD.map(v=>v!=null?+(v*100).toFixed(2):null),
        borderColor:'rgba(255,184,77,.7)',backgroundColor:'transparent',fill:false,tension:.3,borderWidth:1.5,pointRadius:1,borderDash:[4,3]},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'%':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'%'}}}}});

  dc('c-pf-cum');
  CH['c-pf-cum']=new Chart(document.getElementById('c-pf-cum'),{type:'line',data:{labels:ms,
    datasets:[
      {label:'Fund',data:R.nav.map(v=>v!=null?+(v/base).toFixed(3):null),
        borderColor:'#4f8eff',backgroundColor:'transparent',fill:false,tension:.4,borderWidth:2.5,pointRadius:2},
      {label:'BTC', data:R.btcPrice.map(v=>v!=null?+(v/btcBase).toFixed(3):null),
        borderColor:'rgba(255,184,77,.7)',backgroundColor:'transparent',fill:false,tension:.4,borderWidth:1.5,pointRadius:1,borderDash:[4,3]},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'x':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'x'}}}}});

  dc('c-pf-strat');
  CH['c-pf-strat']=new Chart(document.getElementById('c-pf-strat'),{type:'bar',data:{labels:hfMs,
    datasets:[
      {label:'Hedge',      data:hfMs.map(m=>hfGet('Hedge Trading',m)),      backgroundColor:'rgba(79,142,255,.65)',borderRadius:2},
      {label:'Algo',       data:hfMs.map(m=>hfGet('Algo Trading',m)),       backgroundColor:'rgba(45,212,160,.6)', borderRadius:2},
      {label:'Asset',      data:hfMs.map(m=>hfGet('Asset',m)),              backgroundColor:'rgba(255,184,77,.55)',borderRadius:2},
      {label:'Prediction', data:hfMs.map(m=>hfGet('Prediction Market',m)), backgroundColor:'rgba(157,126,255,.5)',borderRadius:2},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}}},
      scales:{...co().scales,x:{...co().scales.x,stacked:true},y:{...co().scales.y,stacked:true}}}});

  if(document.getElementById('pf-trader-sub'))
    document.getElementById('pf-trader-sub').textContent='Balance by trader · '+SELM;
  const cur=tots(SELM);
  const pics=Object.entries(cur.byPic).filter(([,v])=>v>0).sort((a,b)=>b[1]-a[1]);
  dc('c-pf-trader');
  CH['c-pf-trader']=new Chart(document.getElementById('c-pf-trader'),{type:'doughnut',
    data:{labels:pics.map(([p])=>p),datasets:[{
      data:pics.map(([,v])=>v),
      backgroundColor:pics.map(([p])=>(PC[p]||{bg:'rgba(107,127,163,.5)'}).bg),
      borderColor:'#07090f',borderWidth:3,hoverOffset:6}]},
    options:{...doo(),plugins:{...doo().plugins,
      tooltip:{...doo().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.label}: ${fm(ctx.parsed)}`}}}}});

  // ── TRADER PERFORMANCE CHARTS ──────────────────────────────
  const MONTHS_ALL = (D&&D.months)||[]; // trading months
  const TR_COLORS = {
    'Nam'   : {border:'#2dd4a0', bg:'rgba(45,212,160,.15)'},
    'Tùng'  : {border:'#ffb84d', bg:'rgba(255,184,77,.15)'},
    'Việt'  : {border:'#4f8eff', bg:'rgba(79,142,255,.15)'},
    'Vũ - FM':{border:'#9d7eff', bg:'rgba(157,126,255,.15)'},
  };

  // Aggregate balance per trader per month from trading rows
  function traderMonthly(pic){
    return MONTHS_ALL.map(m=>{
      let t=0;
      D.rows.forEach(r=>{if(r.pic===pic)t+=(r.monthly[m]||0);});
      return t||null;
    });
  }

  const mainTraders = ['Nam','Tùng','Việt','Vũ - FM'];

  // Chart 1: Balance over time
  dc('c-tr-bal');
  CH['c-tr-bal']=new Chart(document.getElementById('c-tr-bal'),{type:'line',
    data:{labels:MONTHS_ALL, datasets:mainTraders.map(p=>({
      label:p,
      data:traderMonthly(p),
      borderColor:TR_COLORS[p].border,
      backgroundColor:TR_COLORS[p].bg,
      fill:false, tension:.4, borderWidth:2.5, pointRadius:3, pointHoverRadius:6,
    }))},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${fm(ctx.raw)}`}}}}});


  // Chart 2: Monthly return % per trader
  function traderMoM(pic){
    const bal = traderMonthly(pic);
    return bal.map((v,i)=>{
      if(i===0||v==null) return null;
      const prev = bal[i-1];
      return prev&&prev>0 ? +((v-prev)/prev*100).toFixed(2) : null;
    });
  }

  dc('c-tr-ret');
  CH['c-tr-ret']=new Chart(document.getElementById('c-tr-ret'),{type:'bar',
    data:{labels:MONTHS_ALL, datasets:mainTraders.map(p=>({
      label:p,
      data:traderMoM(p),
      backgroundColor:TR_COLORS[p].border.replace(')',', .65)').replace('rgb','rgba'),
      borderRadius:3,
    }))},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'%':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'%'}}}}});

  // Chart 3: Cumulative return per trader (indexed to 1.0)
  function traderCumRet(pic){
    const bal = traderMonthly(pic);
    const base = bal.find(v=>v!=null)||1;
    return bal.map(v=>v!=null ? +(v/base).toFixed(3) : null);
  }

  dc('c-tr-cum');
  CH['c-tr-cum']=new Chart(document.getElementById('c-tr-cum'),{type:'line',
    data:{labels:MONTHS_ALL, datasets:mainTraders.map(p=>({
      label:p,
      data:traderCumRet(p),
      borderColor:TR_COLORS[p].border,
      backgroundColor:'transparent',
      fill:false, tension:.4, borderWidth:2.5, pointRadius:3, pointHoverRadius:6,
    }))},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'x':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'x'}}}}});
}

// ═══════════════════════════════════════════════════════
// RISK TAB
// ═══════════════════════════════════════════════════════



function rRiskTab(){
  const R=D.risk;
  if(!R){document.getElementById('p-risk').innerHTML='<div style="padding:60px;text-align:center;color:var(--sub);font-family:var(--mono)">⚠ Risk sheet not found</div>';return;}
  const M=R.metrics;
  const ROWS=[['CAGR',M.CAGR,'%'],['Volatility',M.Vol,'%'],['Sharpe',M.Sharpe,'x'],
    ['Sortino',M.Sortino,'x'],['Max DD',M.MaxDD,'%'],['Worst Month',M.WorstM,'%'],
    ['Calmar',M.Calmar,'x'],['Beta',M.Beta,'x'],['Alpha',M.Alpha,'%']];

  // KPI strip
  const PCT_METRICS=new Set(['CAGR','Volatility','Max DD','Worst Month','Alpha']);
  document.getElementById('risk-kpis').innerHTML=ROWS.map(([lbl,v])=>{
    const fv=v.fund, bv=v.btc;
    const fmt=x=>x==null?'—':PCT_METRICS.has(lbl)?fp(x):x.toFixed(2);
    const c=fv==null?'var(--sub)':fv>1?'var(--grn)':fv>0?'var(--acc)':fv>-0.15?'var(--gold)':'var(--red)';
    return`<div class="kpi"><div class="kpi-bar" style="background:${c}"></div>
      <div class="kpi-lbl">${lbl}</div>
      <div class="kpi-val" style="color:${c}">${fmt(fv)}</div>
      <div class="kpi-foot">BTC: ${fmt(bv)}</div></div>`;
  }).join('');

  // Comparison table
  document.getElementById('risk-table-wrap').innerHTML=`
    <table style="width:100%;border-collapse:collapse;font-family:var(--mono);font-size:12px">
      <thead><tr>
        <th style="text-align:left;padding:8px 10px;color:var(--sub);font-size:9px;letter-spacing:1.5px;border-bottom:1px solid var(--bdr)">METRIC</th>
        <th style="text-align:right;padding:8px 10px;color:var(--acc);font-size:9px;letter-spacing:1.5px;border-bottom:1px solid var(--bdr)">FUND</th>
        <th style="text-align:right;padding:8px 10px;color:var(--gold);font-size:9px;letter-spacing:1.5px;border-bottom:1px solid var(--bdr)">BTC</th>
        <th style="text-align:right;padding:8px 10px;color:var(--sub);font-size:9px;letter-spacing:1.5px;border-bottom:1px solid var(--bdr)">EDGE</th>
      </tr></thead>
      <tbody>
        ${ROWS.map(([l,v,u])=>{
          const fmt=x=>x==null?'—':u==='%'?fp(x):x.toFixed(2);
          const edge=v.fund!=null&&v.btc!=null?v.fund-v.btc:null;
          const fc=v.fund!=null&&v.fund>0?'var(--grn)':v.fund!=null&&v.fund<-0.2?'var(--red)':'var(--txt)';
          const bc=v.btc!=null&&v.btc>0?'var(--grn)':v.btc!=null&&v.btc<-0.2?'var(--red)':'var(--sub)';
          const ec=edge!=null&&edge>0?'var(--grn)':edge!=null&&edge<0?'var(--red)':'var(--sub)';
          return`<tr style="border-bottom:1px solid rgba(28,36,56,.5)">
            <td style="padding:9px 10px;color:var(--sub)">${l}</td>
            <td style="padding:9px 10px;text-align:right;font-weight:600;color:${fc}">${fmt(v.fund)}</td>
            <td style="padding:9px 10px;text-align:right;color:${bc}">${fmt(v.btc)}</td>
            <td style="padding:9px 10px;text-align:right;font-size:10px;color:${ec}">${edge!=null?(edge>0?'+':'')+fmt(edge):'—'}</td>
          </tr>`;
        }).join('')}
      </tbody>
    </table>`;

  // Comparison bars
  const BAR_ROWS=[['CAGR',M.CAGR,'%'],['Sharpe',M.Sharpe,'x'],['Calmar',M.Calmar,'x'],
    ['Max DD',M.MaxDD,'%'],['Alpha',M.Alpha,'%'],['Volatility',M.Vol,'%']];
  const maxA=Math.max(...BAR_ROWS.flatMap(([,v])=>[Math.abs(v.fund||0),Math.abs(v.btc||0)]));
  document.getElementById('risk-bars-wrap').innerHTML=BAR_ROWS.map(([l,v,u])=>{
    const fw=maxA>0?Math.abs(v.fund||0)/maxA*100:0;
    const bw=v.btc!=null&&maxA>0?Math.abs(v.btc)/maxA*100:0;
    const fmt=x=>x==null?'—':u==='%'?fp(x):x.toFixed(2);
    return`<div style="margin-bottom:14px">
      <div style="display:flex;justify-content:space-between;margin-bottom:5px">
        <span style="font-size:11px;color:var(--sub)">${l}</span>
        <span style="font-family:var(--mono);font-size:11px">
          <span style="color:var(--acc)">${fmt(v.fund)}</span>
          <span style="color:var(--dim)"> / </span>
          <span style="color:var(--gold)">${fmt(v.btc)}</span>
        </span>
      </div>
      <div style="height:5px;background:var(--s3);border-radius:3px;margin-bottom:3px">
        <div style="width:${fw.toFixed(1)}%;height:100%;border-radius:3px;background:var(--acc)"></div>
      </div>
      <div style="height:4px;background:var(--s3);border-radius:3px;opacity:.6">
        <div style="width:${bw.toFixed(1)}%;height:100%;border-radius:3px;background:var(--gold)"></div>
      </div>
    </div>`;
  }).join('');

  dc('c-risk-dist');
  CH['c-risk-dist']=new Chart(document.getElementById('c-risk-dist'),{type:'bar',data:{labels:R.months,
    datasets:[
      {label:'Fund',data:R.fundRet.map(v=>v!=null?+(v*100).toFixed(2):null),
        backgroundColor:R.fundRet.map(v=>v!=null&&v>=0?'rgba(79,142,255,.75)':'rgba(255,77,109,.7)'),
        borderRadius:3,barPercentage:.6},
      {label:'BTC',data:R.btcRet.map(v=>v!=null?+(v*100).toFixed(2):null),
        backgroundColor:'rgba(255,184,77,.45)',borderRadius:3,barPercentage:.6},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw.toFixed(2)+'%':'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>v+'%'}}}}});

  dc('c-risk-btc');
  CH['c-risk-btc']=new Chart(document.getElementById('c-risk-btc'),{type:'line',data:{labels:R.months,
    datasets:[{label:'BTC Price',data:R.btcPrice,borderColor:'#ffb84d',
      backgroundColor:(ctx)=>{const g=ctx.chart.ctx.createLinearGradient(0,0,0,240);
        g.addColorStop(0,'rgba(255,184,77,.18)');g.addColorStop(1,'rgba(255,184,77,0)');return g;},
      fill:true,tension:.4,borderWidth:2,pointRadius:2}]},
    options:{...co(),plugins:{...co().plugins,
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` BTC: $${ctx.raw!=null?(+ctx.raw).toLocaleString():'—'}`}}},
      scales:{...co().scales,y:{...co().scales.y,ticks:{...co().scales.y.ticks,callback:v=>'$'+(v/1000).toFixed(0)+'k'}}}}});

  // ── ROLLING CORRELATION ──────────────────────────────────
  // Compute rolling 3-month Pearson correlation between fundRet and btcRet
  function rollCorr(a, b, win){
    return a.map((_, i)=>{
      if(i < win-1) return null;
      const fa=a.slice(i-win+1,i+1), fb=b.slice(i-win+1,i+1);
      const ma=fa.reduce((s,v)=>s+v,0)/win, mb=fb.reduce((s,v)=>s+v,0)/win;
      const num=fa.reduce((s,v,j)=>s+(v-ma)*(fb[j]-mb),0);
      const da=Math.sqrt(fa.reduce((s,v)=>s+(v-ma)**2,0));
      const db=Math.sqrt(fb.reduce((s,v)=>s+(v-mb)**2,0));
      return da&&db ? +((num/(da*db)).toFixed(3)) : null;
    });
  }
  const corrData = rollCorr(R.fundRet, R.btcRet, 3);

  dc('c-risk-corr');
  CH['c-risk-corr']=new Chart(document.getElementById('c-risk-corr'),{type:'line',
    data:{labels:R.months, datasets:[
      {label:'Rolling Corr (3M)',data:corrData,
        borderColor:'#9d7eff',backgroundColor:'rgba(157,126,255,.1)',
        fill:true,tension:.4,borderWidth:2.5,pointRadius:3,pointHoverRadius:6,
        pointBackgroundColor:corrData.map(v=>v==null?'#9d7eff':v>0?'#9d7eff':'#ff4d6d')},
      {label:'Zero',data:R.months.map(()=>0),
        borderColor:'rgba(255,255,255,.15)',borderWidth:1,borderDash:[3,3],
        pointRadius:0,fill:false},
    ]},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10,filter:i=>i.text!=='Zero'}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>ctx.dataset.label==='Zero'?'':` Corr: ${ctx.raw!=null?ctx.raw:'—'}`}}},
      scales:{...co().scales,
        y:{...co().scales.y,min:-1,max:1,ticks:{...co().scales.y.ticks,callback:v=>v.toFixed(1)}}}}});

  // ── DRAWDOWN RECOVERY ──────────────────────────────────
  // Show drawdown as filled area + mark recovery months
  const ddPct = R.fundDD.map(v=>v!=null?+(v*100).toFixed(2):0);
  const navNorm = R.nav.map(v=>v/R.nav[0]); // normalized to 1.0
  const hwmNorm = R.fundPeak.map(v=>v/R.nav[0]);

  dc('c-risk-recovery');
  CH['c-risk-recovery']=new Chart(document.getElementById('c-risk-recovery'),{type:'bar',
    data:{labels:R.months, datasets:[
      {label:'Drawdown %',data:ddPct,
        backgroundColor:ddPct.map(v=>v<-20?'rgba(255,77,109,.75)':v<0?'rgba(255,164,77,.6)':'rgba(45,212,160,.5)'),
        borderRadius:2,yAxisID:'y',order:2},
      {label:'NAV (normalized)',data:navNorm.map(v=>+(v.toFixed(3))),
        type:'line',borderColor:'#4f8eff',backgroundColor:'transparent',
        borderWidth:2,pointRadius:2,fill:false,tension:.4,yAxisID:'y2',order:1},
      {label:'High-Water Mark',data:hwmNorm.map(v=>+(v.toFixed(3))),
        type:'line',borderColor:'rgba(45,212,160,.7)',backgroundColor:'transparent',
        borderWidth:1.5,borderDash:[5,4],pointRadius:0,fill:false,tension:.4,yAxisID:'y2',order:0},
    ]},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip}},
      scales:{
        x:{...co().scales.x},
        y:{...co().scales.y,position:'left',ticks:{...co().scales.y.ticks,callback:v=>v+'%'},title:{display:true,text:'DD%',color:'#6b7fa3',font:{size:9}}},
        y2:{...co().scales.y,position:'right',grid:{display:false},ticks:{...co().scales.y.ticks,callback:v=>v+'x'},title:{display:true,text:'NAV (x)',color:'#6b7fa3',font:{size:9}}}}}});

}

// ═══════════════════════════════════════════════════════
// PORTFOLIO TAB
// ═══════════════════════════════════════════════════════
function rPort(){
  rApp();
  const HF=D.hf, sel=hfSel(SELM), hfMs=HF?HF.months:[];
  const nav=hfGet('Total (NAV)',sel), equity=hfGet('Equity',sel);
  const loan=hfGet('Loan',sel), fee=hfGet('Fee',sel), profit=hfGet('Net Profit',sel);
  const hedge=hfGet('Hedge Trading',sel), algo=hfGet('Algo Trading',sel);
  const asset=hfGet('Asset',sel), pred=hfGet('Prediction Market',sel);

  // KPIs
  document.getElementById('port-kpis').innerHTML=[
    ['Total NAV',   'var(--acc)',                        nav],
    ['Net Profit',  profit>=0?'var(--grn)':'var(--red)', profit],
    ['Equity (LP)', 'var(--sky)',                        equity],
    ['Hedge',       'var(--acc)',                        hedge],
    ['Algo',        'var(--acc2)',                       algo],
    ['Loan',        loan>0?'var(--rose)':'var(--dim)',   loan],
    ['Fees Paid',   'var(--gold)',                       fee],
  ].map(([l,c,v])=>`<div class="kpi"><div class="kpi-bar" style="background:${c}"></div>
    <div class="kpi-lbl">${l}</div>
    <div class="kpi-val" style="color:${c}">${fm(v)}</div>
    <div class="kpi-foot">${sel}</div></div>`).join('');

  if(document.getElementById('port-cap-sub'))
    document.getElementById('port-cap-sub').textContent='Capital structure · '+sel;

  // Capital table
  const CAP=[
    ['Asset (Wallet)', asset,  'var(--gold)',  true, false],
    ['— Non-custodial', hfGet('None-custodial wallet',sel), 'var(--sub)', false, false],
    ['— USDC+USDT',    hfGet('USDC+USDT',sel),             'var(--sub)', false, false],
    ['Hedge Trading',  hedge,  'var(--acc)',   true, false],
    ['— HL Tùng',      hfGet('Copin IF (Hyperliquid) - Tùng',sel),  'var(--sub)', false, false],
    ['— HL Nam',       hfGet('Copin IF (Hyperliquid) - Nam',sel),   'var(--sub)', false, false],
    ['— HL Vũ FM',     hfGet('Copin IF (Hyperliquid) - Vũ FM',sel), 'var(--sub)', false, false],
    ['Algo Trading',   algo,   'var(--acc2)',  true, false],
    ['— DEX Copy MM',  hfGet('DEX Copy MM',sel),            'var(--sub)', false, false],
    ['— Lighter Việt', hfGet('- Copin IF (Lighter)- Việt',sel),'var(--sub)',false,false],
    ['— Lighter Tùng', hfGet('- Copin IF (Lighter) - Tùng',sel),'var(--sub)',false,false],
    ['— Lighter Nam',  hfGet('- Copin IF (Lighter) - Nam',sel), 'var(--sub)',false,false],
    ['Prediction',     pred,   'var(--pur)',   true, false],
    ['— Poly Việt',    hfGet('Poly Flash - Việt',sel),     'var(--sub)', false, false],
    ['— Poly Nam',     hfGet('Poly Market - Nam',sel),     'var(--sub)', false, false],
    ['— Poly Vũ FM',   hfGet('Poly Market - Vũ FM',sel),  'var(--sub)', false, false],
    ['Loan',           loan,   'var(--rose)',  true, false],
    ['Fee',            fee,    'var(--gold)',  true, false],
    ['Net Profit',     profit, profit>=0?'var(--grn)':'var(--red)', true, false],
    ['Total NAV',      nav,    'var(--acc)',   true, true],
  ];
  document.getElementById('port-capital').innerHTML=`
    <table style="width:100%;border-collapse:collapse;font-family:var(--mono);font-size:11px">
      ${CAP.map(([l,v,c,sec,tot])=>`
        <tr style="border-bottom:1px solid rgba(28,36,56,${sec?.6:.3})${tot?';border-top:2px solid rgba(79,142,255,.4)':''}">
          <td style="padding:${sec?'8':'5'}px 8px;color:${sec?'var(--txt)':'var(--sub)'};
            font-weight:${sec?600:400}${!sec&&!tot?';padding-left:20px':''}">${l}</td>
          <td style="padding:${sec?'8':'5'}px 8px;text-align:right;
            font-weight:${tot?700:sec?600:400};color:${v===0?'var(--dim)':c}">${v!==0?fm(v):'—'}</td>
        </tr>`).join('')}
    </table>`;

  // Cashflow
  dc('c-port-cf');
  CH['c-port-cf']=new Chart(document.getElementById('c-port-cf'),{type:'bar',data:{labels:D.months,
    datasets:[
      {label:'Deposit', data:D.months.map(m=>{let d=0;D.rows.forEach(r=>{d+=(r.dep[m]||0)});return d;}),
        backgroundColor:'rgba(45,212,160,.65)',borderRadius:3},
      {label:'Withdraw',data:D.months.map(m=>{let w=0;D.rows.forEach(r=>{w+=(r.wd[m]||0)});return -w;}),
        backgroundColor:'rgba(255,77,109,.55)',borderRadius:3},
      {label:'PnL',     data:D.months.map(m=>cft(m).pnl),
        type:'line',borderColor:'#ffb84d',backgroundColor:'transparent',
        fill:false,tension:.4,borderWidth:2,pointRadius:3,yAxisID:'y'},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}}}}});

  // Trader history
  dc('c-port-tr');
  const mainTr=['Nam','Tùng','Việt','Vũ - FM'];
  const TR_AC={'Nam':{border:'#2dd4a0',bg:'rgba(45,212,160,.55)'},'Tùng':{border:'#ffb84d',bg:'rgba(255,184,77,.55)'},'Việt':{border:'#4f8eff',bg:'rgba(79,142,255,.55)'},'Vũ - FM':{border:'#9d7eff',bg:'rgba(157,126,255,.55)'}};
  function trPct(pic,months){return months.map(m=>{const t=tots(m).byPic,tot=Object.values(t).reduce((a,b)=>a+b,0);return tot?+((t[pic]||0)/tot*100).toFixed(1):null;});}
  CH['c-port-tr']=new Chart(document.getElementById('c-port-tr'),{type:'line',
    data:{labels:D.months,datasets:mainTr.map(p=>({label:p,data:trPct(p,D.months),
      borderColor:TR_AC[p].border,backgroundColor:TR_AC[p].bg,
      fill:true,tension:.4,borderWidth:1.5,pointRadius:2,pointHoverRadius:5}))},
    options:{...co(),plugins:{...co().plugins,
      legend:{display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}},
      tooltip:{...co().plugins.tooltip,callbacks:{label:ctx=>` ${ctx.dataset.label}: ${ctx.raw!=null?ctx.raw+'%':'—'}`}}},
      scales:{...co().scales,x:{...co().scales.x},
        y:{...co().scales.y,stacked:true,ticks:{...co().scales.y.ticks,callback:v=>v+'%'},max:100}}}});

  // Fee breakdown
  dc('c-port-fee');
  CH['c-port-fee']=new Chart(document.getElementById('c-port-fee'),{type:'bar',data:{labels:hfMs,
    datasets:[
      {label:'Buy Subscription', data:hfMs.map(m=>hfGet('Buy subcription (Copin)',m)), backgroundColor:'rgba(79,142,255,.65)',borderRadius:2},
      {label:'Management Fee',   data:hfMs.map(m=>hfGet('Management Fee',m)),           backgroundColor:'rgba(45,212,160,.6)',borderRadius:2},
      {label:'Performance Fee',  data:hfMs.map(m=>hfGet('Performance Fee',m)),          backgroundColor:'rgba(255,184,77,.6)',borderRadius:2},
      {label:'Other Fee',        data:hfMs.map(m=>hfGet('Other Fee',m)),               backgroundColor:'rgba(157,126,255,.5)',borderRadius:2},
    ]},options:{...co(),plugins:{...co().plugins,
      legend:{display:true,labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10}}},
      scales:{...co().scales,x:{...co().scales.x,stacked:true},y:{...co().scales.y,stacked:true}}}});
}

// ── AVERAGE PURCHASE PRICE ────────────────────────────────
function rApp(){
  const A = D.app;
  if(!A){['app-summary','app-txns','app-total'].forEach(id=>{const el=document.getElementById(id);if(el)el.innerHTML='';});return;}

  const ASSET_COLOR = {BTC:'#ffb84d', ETH:'#4f8eff', BNB:'#f0b90b'};
  const fNum = n => n==null?'—':n.toLocaleString('en',{maximumFractionDigits:2});
  const fUSD = n => n==null?'—':'$'+Math.round(n).toLocaleString('en');

  // Total
  const total = document.getElementById('app-total');
  if(total) total.textContent = 'Total Value: '+fm(A.total);

  // Summary cards
  const sumEl = document.getElementById('app-summary');
  if(sumEl) sumEl.innerHTML = `
    <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:12px">
      ${A.summary.map(s=>{
        const c = ASSET_COLOR[s.asset]||'var(--acc)';
        return `<div style="background:var(--s2);border:1px solid var(--bdr);border-radius:10px;padding:14px 16px;position:relative;overflow:hidden">
          <div style="position:absolute;top:0;left:0;right:0;height:2px;background:${c}"></div>
          <div style="font-family:var(--mono);font-size:11px;font-weight:700;color:${c};margin-bottom:10px">${s.asset}</div>
          <div style="display:flex;flex-direction:column;gap:6px">
            <div style="display:flex;justify-content:space-between">
              <span style="font-size:10px;color:var(--sub)">Avg Price</span>
              <span style="font-family:var(--mono);font-size:11px;font-weight:600;color:var(--txt)">${fm(s.avgPrice)}</span>
            </div>
            <div style="display:flex;justify-content:space-between">
              <span style="font-size:10px;color:var(--sub)">Quantity</span>
              <span style="font-family:var(--mono);font-size:11px;color:var(--txt)">${fNum(s.quantity)}</span>
            </div>
            <div style="display:flex;justify-content:space-between">
              <span style="font-size:10px;color:var(--sub)">Value</span>
              <span style="font-family:var(--mono);font-size:11px;font-weight:600;color:${c}">${fm(s.value)}</span>
            </div>
          </div>
        </div>`;
      }).join('')}
    </div>`;

  // Transaction log
  const txEl = document.getElementById('app-txns');
  if(txEl) txEl.innerHTML = `
    <table style="width:100%;border-collapse:collapse;font-family:var(--mono);font-size:11px">
      <thead><tr style="border-bottom:1px solid var(--bdr)">
        <th style="text-align:left;padding:7px 10px;color:var(--sub);font-size:9px;letter-spacing:1px">ASSET</th>
        <th style="text-align:left;padding:7px 10px;color:var(--sub);font-size:9px;letter-spacing:1px">DATE</th>
        <th style="text-align:right;padding:7px 10px;color:var(--sub);font-size:9px;letter-spacing:1px">QTY</th>
        <th style="text-align:right;padding:7px 10px;color:var(--sub);font-size:9px;letter-spacing:1px">PRICE</th>
        <th style="text-align:right;padding:7px 10px;color:var(--sub);font-size:9px;letter-spacing:1px">VALUE</th>
      </tr></thead>
      <tbody>
        ${A.txns.map(t=>{
          const c = ASSET_COLOR[t.asset]||'var(--acc)';
          const isSell = t.qty < 0;
          return `<tr style="border-bottom:1px solid rgba(28,36,56,.4)">
            <td style="padding:7px 10px"><span style="color:${c};font-weight:600">${t.asset}</span></td>
            <td style="padding:7px 10px;color:var(--sub)">${t.date}</td>
            <td style="padding:7px 10px;text-align:right;color:${isSell?'var(--red)':'var(--grn)'}">${isSell?'':'+'}${fNum(t.qty)}</td>
            <td style="padding:7px 10px;text-align:right;color:var(--txt)">${fm(t.price)}</td>
            <td style="padding:7px 10px;text-align:right;color:${isSell?'var(--red)':'var(--txt)'}">${isSell?'-':''}${fm(Math.abs(t.value))}</td>
          </tr>`;
        }).join('')}
      </tbody>
    </table>`;
}


// ═══════════════════════════════════════════════════════════════
// VOLUME & FEE TAB
// ═══════════════════════════════════════════════════════════════
function rVol(){
  const V = D.vol;
  const el = document.getElementById('p-volume');
  if(!V){
    el.innerHTML='<div style="padding:60px;text-align:center;color:var(--sub)">Volume data not available</div>';
    return;
  }
  const ms = V.months;
  const base = co();
  const usdTick = {callback:v=>fm(v)};
  const leg = {display:true,position:'top',labels:{color:'#6b7fa3',font:{size:10,family:"'JetBrains Mono'"},boxWidth:10,padding:12}};

  // ── Volume KPI strip ────────────────────────────────────────
  const tVol    = V.traderVol    || {};
  const tTrades = V.traderTrades || {};
  const exVol   = V.exchangeVol  || {};
  const traders = Object.keys(tVol).length
    ? Object.keys(tVol)
    : ['Việt','Nam','Tùng','Vũ - FM','Hiệp'];

  const hasTraderVol    = traders.some(t=>(tVol[t]||[]).some(v=>v>0));
  const hasTraderTrades = traders.some(t=>(tTrades[t]||[]).some(v=>v>0));

  const totalVol    = (V.tradingVol||[]).reduce((a,b)=>a+b,0);
  const totalTrades = (V.numTrades ||[]).reduce((a,b)=>a+b,0);
  const avgVol      = ms.length ? totalVol/ms.length : 0;
  const avgTrades   = ms.length ? Math.round(totalTrades/ms.length) : 0;
  const lastNAV     = (V.navByMonth||[])[V.navByMonth.length-1]||0;
  const volToNav    = lastNAV > 0 ? (totalVol/lastNAV*100).toFixed(1)+'%' : '—';

  document.getElementById('vol-kpis').innerHTML = [
    {l:'Total Volume (YTD)',  v: hasTraderVol    ? fm(totalVol)              : '—', c:'var(--gold)', s:'Tổng volume giao dịch'},
    {l:'Avg Volume / tháng', v: hasTraderVol    ? fm(avgVol)                : '—', c:'var(--acc)',  s:'Volume trung bình/tháng'},
    {l:'Total Trades (YTD)', v: hasTraderTrades ? totalTrades.toLocaleString(): '—', c:'var(--pur)', s:'Tổng số lệnh'},
    {l:'Avg Trades / tháng', v: hasTraderTrades ? avgTrades.toLocaleString() : '—', c:'var(--sub)', s:'Số lệnh trung bình'},
    {l:'Vol / NAV',          v: hasTraderVol    ? volToNav                  : '—', c:'var(--grn)', s:'Mức độ active'},
  ].map(k=>`<div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;
    padding:16px 18px;flex:1;min-width:130px">
    <div style="font-size:9px;color:var(--sub);font-family:var(--mono);
      text-transform:uppercase;letter-spacing:.06em;margin-bottom:6px">${k.l}</div>
    <div style="font-size:20px;font-weight:700;color:${k.c};font-family:var(--mono)">${k.v}</div>
    <div style="font-size:9px;color:var(--dim);margin-top:3px">${k.s}</div>
  </div>`).join('');

  // Show/hide pending badge
  const badge = document.getElementById('vol-no-data-badge');
  if(badge) badge.style.display = (hasTraderVol||hasTraderTrades) ? 'none' : 'flex';

  const TRADER_COLORS = {
    'Việt':'#4f8eff','Nam':'#2dd4a0','Tùng':'#ffb84d',
    'Vũ - FM':'#ff4d6d','Hiệp':'#9d7eff'
  };
  const EX_COLORS = {
    'Hyperliquid':'#4f8eff','Lighter':'#2dd4a0','Binance':'#f0b90b',
    'Bybit':'#f7a600','OKX':'#9d7eff','Decibel':'#fb923c'
  };

  // ── Chart 1: Volume ($) per trader stacked bar ─────────────
  dc('c-vol-trader-vol');
  CH['c-vol-trader-vol'] = new Chart(document.getElementById('c-vol-trader-vol'),{
    type:'bar',
    data:{labels:ms, datasets: traders.map(t=>({
      label: t,
      data: tVol[t] || ms.map(()=>0),
      backgroundColor: (TRADER_COLORS[t]||'#888')+'cc',
      borderRadius:3, stack:'v'
    }))},
    options:{...base,
      plugins:{...base.plugins, legend:leg,
        title:{display:!hasTraderVol,
          text:'Chưa có data — dev điền vol.traderVol',
          color:'#6b7fa3', font:{size:10,family:"'JetBrains Mono'"}}},
      scales:{
        x:{...base.scales.x, stacked:true},
        y:{...base.scales.y, stacked:true, ticks:{...base.scales.y.ticks,...usdTick}}}}});

  // ── Chart 2: Trades (#) per trader stacked bar ─────────────
  dc('c-vol-trader-trades');
  CH['c-vol-trader-trades'] = new Chart(document.getElementById('c-vol-trader-trades'),{
    type:'bar',
    data:{labels:ms, datasets: traders.map(t=>({
      label: t,
      data: tTrades[t] || ms.map(()=>0),
      backgroundColor: (TRADER_COLORS[t]||'#888')+'bb',
      borderRadius:3, stack:'t'
    }))},
    options:{...base,
      plugins:{...base.plugins, legend:leg,
        title:{display:!hasTraderTrades,
          text:'Chưa có data — dev điền vol.traderTrades',
          color:'#6b7fa3', font:{size:10,family:"'JetBrains Mono'"}}},
      scales:{
        x:{...base.scales.x, stacked:true},
        y:{...base.scales.y, stacked:true, ticks:{...base.scales.y.ticks,
          callback:v=>v>=1000?(v/1000).toFixed(1)+'k':String(v)}}}}});

  // ── Chart 3: Volume by Exchange stacked bar ─────────────────
  dc('c-vol-exchange-vol');
  const exKeys = Object.keys(exVol).length
    ? Object.keys(exVol)
    : ['Hyperliquid','Lighter','Binance','Bybit','OKX','Decibel'];
  const hasExVol = exKeys.some(e=>(exVol[e]||[]).some(v=>v>0));

  CH['c-vol-exchange-vol'] = new Chart(document.getElementById('c-vol-exchange-vol'),{
    type:'bar',
    data:{labels:ms, datasets: exKeys.map(e=>({
      label: e,
      data: exVol[e] || ms.map(()=>0),
      backgroundColor: (EX_COLORS[e]||'#888')+'cc',
      borderRadius:3, stack:'e'
    }))},
    options:{...base,
      plugins:{...base.plugins, legend:leg,
        title:{display:!hasExVol,
          text:'Chưa có data — dev điền vol.exchangeVol',
          color:'#6b7fa3', font:{size:10,family:"'JetBrains Mono'"}}},
      scales:{
        x:{...base.scales.x, stacked:true},
        y:{...base.scales.y, stacked:true, ticks:{...base.scales.y.ticks,...usdTick}}}}});
}

let LIVE = null;  // mutable live state

function rLive(){
  LIVE = D.live ? JSON.parse(JSON.stringify(D.live)) : null;
  const el = document.getElementById('p-live');
  if(!el) return;
  renderLivePanel(el);
}

function renderLivePanel(el){
  const exs = LIVE ? LIVE.exchanges : {};
  const lastSync = LIVE ? LIVE.lastSync : null;
  const EX_ORDER = ['hyperliquid','lighter','binance','bybit','okx','decibel','polymarket'];

  // ── compute totals across all exchanges ───────────────────
  let totalBal=0, totalPnl24h=0, totalVol30d=0, totalTrades30d=0;
  let syncedCount=0, totalEx=EX_ORDER.length;
  EX_ORDER.forEach(exKey=>{
    const ex = exs[exKey];
    if(!ex) return;
    if(ex.status==='ok') syncedCount++;
    Object.values(ex.traders||{}).forEach(t=>{
      totalBal    += t.balance||0;
      totalPnl24h += t.pnl24h||0;
      totalVol30d += t.volume30d||0;
      totalTrades30d += t.trades30d||0;
    });
  });

  const syncedAll = syncedCount === totalEx;
  const syncTime  = lastSync ? new Date(lastSync).toLocaleString('vi-VN') : 'Chưa sync';
  const pnlColor  = totalPnl24h >= 0 ? 'var(--grn)' : 'var(--red)';

  el.innerHTML = `
  <!-- ── HEADER BAR ── -->
  <div style="display:flex;align-items:center;justify-content:space-between;
    background:var(--s1);border:1px solid var(--bdr);border-radius:12px;
    padding:16px 22px;margin-bottom:18px;gap:16px;flex-wrap:wrap">
    <div>
      <div style="font-size:13px;font-weight:700;color:var(--txt);font-family:var(--mono)">
        LIVE EXCHANGE DATA
      </div>
      <div style="font-size:10px;color:var(--sub);margin-top:3px">
        Last sync: <span style="color:var(--acc)">${syncTime}</span>
        &nbsp;·&nbsp; ${syncedCount}/${totalEx} exchanges connected
      </div>
    </div>
    <div style="display:flex;gap:10px;align-items:center">
      <div style="font-size:9px;color:var(--sub);font-family:var(--mono);
        background:var(--s2);padding:6px 12px;border-radius:6px;border:1px solid var(--bdr)">
        ℹ️ &nbsp;Dev: implement <code style="color:var(--acc)">window.syncExchanges()</code>
        then call <code style="color:var(--acc)">refreshLive()</code>
      </div>
      <button onclick="triggerSync()" id="sync-btn"
        style="background:var(--acc);color:#fff;border:none;border-radius:8px;
        padding:10px 20px;font-size:12px;font-weight:700;font-family:var(--mono);
        cursor:pointer;display:flex;align-items:center;gap:8px;transition:opacity .2s">
        <span id="sync-icon">⟳</span> Sync Now
      </button>
    </div>
  </div>

  <!-- ── TOTAL KPI STRIP ── -->
  <div style="display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:18px">
    ${[
      {l:'Total NAV (Live)',v:fm(totalBal),      c:'var(--acc)',   s:'Tất cả sàn + trader'},
      {l:'PnL 24h',        v:fm(totalPnl24h),    c:pnlColor,      s:totalPnl24h>=0?'▲ Có lãi':'▼ Lỗ'},
      {l:'Volume 30d',     v:fm(totalVol30d),    c:'var(--gold)',  s:'Tổng khối lượng giao dịch'},
      {l:'Trades 30d',     v:(totalTrades30d||0).toLocaleString(), c:'var(--pur)', s:'Tổng số lệnh'},
    ].map(k=>`
      <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;padding:16px 18px">
        <div style="font-size:9px;color:var(--sub);font-family:var(--mono);
          text-transform:uppercase;letter-spacing:.06em;margin-bottom:6px">${k.l}</div>
        <div style="font-size:20px;font-weight:700;color:${k.c};font-family:var(--mono)">${k.v}</div>
        <div style="font-size:9px;color:var(--dim);margin-top:3px">${k.s}</div>
      </div>`).join('')}
  </div>

  <!-- ── EXCHANGE CARDS ── -->
  <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:14px;margin-bottom:18px">
    ${EX_ORDER.map(exKey=>{
      const ex = exs[exKey];
      if(!ex) return '';
      const traders = Object.entries(ex.traders||{});
      const exBal  = traders.reduce((s,[,t])=>s+(t.balance||0),0);
      const exPnl  = traders.reduce((s,[,t])=>s+(t.pnl24h||0),0);
      const exVol  = traders.reduce((s,[,t])=>s+(t.volume30d||0),0);
      const statusColor = {ok:'var(--grn)',pending:'var(--gold)',error:'var(--red)',idle:'var(--dim)'}[ex.status]||'var(--dim)';
      const statusLabel = {ok:'✓ Connected',pending:'⏳ Pending',error:'✗ Error',idle:'○ Not synced'}[ex.status]||'—';
      const apiNote = (LIVE&&LIVE.api_notes&&LIVE.api_notes[exKey])||'';

      return `
      <div style="background:var(--s1);border:1px solid var(--bdr);border-radius:12px;overflow:hidden">
        <!-- Card Header -->
        <div style="background:var(--s2);padding:14px 18px;
          border-bottom:1px solid var(--bdr);display:flex;align-items:center;gap:12px">
          <div style="background:${ex.color}22;border:1px solid ${ex.color}44;
            border-radius:8px;width:36px;height:36px;display:flex;align-items:center;
            justify-content:center;font-size:11px;font-weight:700;color:${ex.color};
            font-family:var(--mono)">${ex.icon}</div>
          <div style="flex:1">
            <div style="font-size:13px;font-weight:700;color:var(--txt);font-family:var(--mono)">${ex.name}</div>
            <div style="font-size:10px;color:${statusColor};margin-top:1px">${statusLabel}</div>
          </div>
          <div style="text-align:right">
            <div style="font-size:16px;font-weight:700;color:var(--acc);font-family:var(--mono)">${fm(exBal)}</div>
            <div style="font-size:10px;color:${exPnl>=0?'var(--grn)':'var(--red)'}">${exPnl>=0?'▲':'▼'} ${fm(Math.abs(exPnl))} 24h</div>
          </div>
        </div>

        <!-- API Note (dev hint) -->
        <div style="padding:8px 18px;background:${ex.color}08;border-bottom:1px solid var(--bdr)">
          <span style="font-size:9px;color:var(--sub);font-family:var(--mono)">
            🔌 ${apiNote}
          </span>
        </div>

        <!-- Trader Rows -->
        <div style="padding:10px 0">
          ${traders.map(([name,t])=>{
            const pct = t.pnlPct||0;
            const pctColor = pct>=0?'var(--grn)':'var(--red)';
            const positions = t.positions||[];
            return `
            <div style="padding:8px 18px;border-bottom:1px solid ${ex.color}11;
              display:flex;align-items:center;gap:10px">
              <div style="width:28px;height:28px;border-radius:50%;
                background:var(--s3);display:flex;align-items:center;justify-content:center;
                font-size:10px;font-weight:700;color:var(--sub)">${name.charAt(0)}</div>
              <div style="flex:1">
                <div style="font-size:11px;font-weight:600;color:var(--txt)">${name}</div>
                <div style="font-size:9px;color:var(--sub);margin-top:1px">
                  Vol 30d: ${fm(t.volume30d||0)} &nbsp;·&nbsp; ${(t.trades30d||0)} trades
                </div>
              </div>
              <div style="text-align:right">
                <div style="font-size:13px;font-weight:700;color:var(--txt);font-family:var(--mono)">${fm(t.balance||0)}</div>
                <div style="font-size:10px;color:${pctColor};font-family:var(--mono)">${pct>=0?'+':''}${pct.toFixed(2)}%</div>
              </div>
              ${positions.length>0?`
              <div style="margin-left:8px">
                ${positions.slice(0,3).map(p=>`
                <div style="font-size:8px;font-family:var(--mono);
                  background:${p.side==='long'?'rgba(45,212,160,.15)':'rgba(255,77,109,.15)'};
                  color:${p.side==='long'?'var(--grn)':'var(--red)'};
                  border-radius:4px;padding:2px 6px;margin-bottom:2px;white-space:nowrap">
                  ${p.side==='long'?'L':'S'} ${p.symbol||''} ${fm(p.size||0)}
                </div>`).join('')}
              </div>`:''}
            </div>`;
          }).join('')}
        </div>

        <!-- Exchange footer: Vol summary -->
        <div style="padding:10px 18px;background:var(--s2);
          border-top:1px solid var(--bdr);display:flex;justify-content:space-between">
          <span style="font-size:9px;color:var(--sub);font-family:var(--mono)">
            VOL 30D: <span style="color:var(--gold)">${fm(exVol)}</span>
          </span>
          <span style="font-size:9px;color:var(--sub);font-family:var(--mono)">
            ${traders.length} traders active
          </span>
        </div>
      </div>`;
    }).join('')}
  </div>

  <!-- ── OPEN POSITIONS TABLE ── -->
  <div class="card" style="margin-bottom:14px">
    <div class="card-hd">
      <div><div class="card-title">Open Positions</div>
      <div class="card-sub">Tất cả vị thế đang mở — tự động lấy từ các sàn</div></div>
    </div>
    <div style="overflow-x:auto">
      <table style="width:100%;border-collapse:collapse;font-family:var(--mono);font-size:11px">
        <thead>
          <tr style="border-bottom:1px solid var(--bdr)">
            ${['TRADER','SÀN','SYMBOL','SIDE','SIZE','ENTRY','MARK','PNL','ROE %'].map(h=>
              `<th style="padding:10px 14px;color:var(--sub);font-size:9px;
                text-transform:uppercase;letter-spacing:.05em;font-weight:600;text-align:left">${h}</th>`
            ).join('')}
          </tr>
        </thead>
        <tbody id="live-positions-tbody">
          <tr>
            <td colspan="9" style="padding:32px;text-align:center;color:var(--dim);font-size:11px">
              Chưa có dữ liệu — nhấn <strong style="color:var(--acc)">Sync Now</strong> sau khi dev tích hợp API
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <!-- ── RECENT TRADES TABLE ── -->
  <div class="card">
    <div class="card-hd">
      <div><div class="card-title">Recent Trades</div>
      <div class="card-sub">50 giao dịch gần nhất — tất cả sàn</div></div>
    </div>
    <div style="overflow-x:auto">
      <table style="width:100%;border-collapse:collapse;font-family:var(--mono);font-size:11px">
        <thead>
          <tr style="border-bottom:1px solid var(--bdr)">
            ${['TIME','TRADER','SÀN','SYMBOL','SIDE','SIZE ($)','PRICE','FEE','PNL'].map(h=>
              `<th style="padding:10px 14px;color:var(--sub);font-size:9px;
                text-transform:uppercase;letter-spacing:.05em;font-weight:600;text-align:left">${h}</th>`
            ).join('')}
          </tr>
        </thead>
        <tbody id="live-trades-tbody">
          <tr>
            <td colspan="9" style="padding:32px;text-align:center;color:var(--dim);font-size:11px">
              Chưa có dữ liệu — nhấn <strong style="color:var(--acc)">Sync Now</strong> sau khi dev tích hợp API
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>`;
}

// ── Sync trigger (dev implements window.syncExchanges) ─────────
async function triggerSync(){
  const btn   = document.getElementById('sync-btn');
  const icon  = document.getElementById('sync-icon');
  if(!btn) return;

  // Spin animation
  btn.disabled = true;
  btn.style.opacity = '0.7';
  let deg = 0;
  const spin = setInterval(()=>{ deg+=15; icon.style.transform=`rotate(${deg}deg)`; }, 50);

  try {
    // ── DEV INTEGRATION POINT ────────────────────────────────
    // Implement window.syncExchanges() to return data in this shape:
    // {
    //   lastSync: new Date().toISOString(),
    //   exchanges: {
    //     hyperliquid: { status: 'ok', traders: { 'Việt': { balance, pnl24h, pnlPct, positions, volume30d, trades30d } } },
    //     lighter:  { ... },
    //     binance:  { ... },
    //     bybit:    { ... },
    //     okx:      { ... },
    //     decibel:  { ... },
    //   },
    //   positions: [{ trader, exchange, symbol, side, size, entry, mark, pnl, roe }],
    //   trades:    [{ time, trader, exchange, symbol, side, size, price, fee, pnl }],
    // }
    // ─────────────────────────────────────────────────────────

    if(typeof window.syncExchanges === 'function'){
      const data = await window.syncExchanges();
      if(data){
        if(LIVE) Object.assign(LIVE, data);
        else LIVE = data;
        // Update positions table
        if(data.positions) renderPositions(data.positions);
        // Update trades table
        if(data.trades) renderTrades(data.trades);
        // Re-render exchange cards
        const el = document.getElementById('p-live');
        if(el) renderLivePanel(el);
      }
    } else {
      // Dev hasn't implemented yet — show toast
      showToast('⚠️ window.syncExchanges() chưa được implement. Dev cần tích hợp API.');
    }
  } catch(e) {
    showToast('❌ Sync error: ' + e.message);
    console.error('Sync error:', e);
  } finally {
    clearInterval(spin);
    if(icon) icon.style.transform = '';
    if(btn){ btn.disabled=false; btn.style.opacity='1'; }
  }
}

function renderPositions(positions){
  const tbody = document.getElementById('live-positions-tbody');
  if(!tbody||!positions||!positions.length) return;
  tbody.innerHTML = positions.map(p=>{
    const pnlColor = (p.pnl||0)>=0?'var(--grn)':'var(--red)';
    const sideColor = p.side==='long'?'var(--grn)':'var(--red)';
    return `<tr style="border-bottom:1px solid var(--bdr);transition:background .15s"
      onmouseover="this.style.background='var(--s2)'" onmouseout="this.style.background=''">
      <td style="padding:10px 14px;color:var(--txt)">${p.trader||'—'}</td>
      <td style="padding:10px 14px;color:var(--sub)">${p.exchange||'—'}</td>
      <td style="padding:10px 14px;color:var(--acc);font-weight:700">${p.symbol||'—'}</td>
      <td style="padding:10px 14px;color:${sideColor};font-weight:700">${(p.side||'').toUpperCase()}</td>
      <td style="padding:10px 14px;color:var(--txt)">${fm(p.size||0)}</td>
      <td style="padding:10px 14px;color:var(--sub)">${fm(p.entry||0)}</td>
      <td style="padding:10px 14px;color:var(--txt)">${fm(p.mark||0)}</td>
      <td style="padding:10px 14px;color:${pnlColor};font-weight:600">${fm(p.pnl||0)}</td>
      <td style="padding:10px 14px;color:${pnlColor}">${((p.roe||0)*100).toFixed(2)}%</td>
    </tr>`;
  }).join('');
}

function renderTrades(trades){
  const tbody = document.getElementById('live-trades-tbody');
  if(!tbody||!trades||!trades.length) return;
  tbody.innerHTML = trades.slice(0,50).map(t=>{
    const sideColor = t.side==='buy'||t.side==='long'?'var(--grn)':'var(--red)';
    const pnlColor = (t.pnl||0)>=0?'var(--grn)':'var(--red)';
    return `<tr style="border-bottom:1px solid var(--bdr);transition:background .15s"
      onmouseover="this.style.background='var(--s2)'" onmouseout="this.style.background=''">
      <td style="padding:9px 14px;color:var(--sub);white-space:nowrap">${t.time||'—'}</td>
      <td style="padding:9px 14px;color:var(--txt)">${t.trader||'—'}</td>
      <td style="padding:9px 14px;color:var(--sub)">${t.exchange||'—'}</td>
      <td style="padding:9px 14px;color:var(--acc);font-weight:700">${t.symbol||'—'}</td>
      <td style="padding:9px 14px;color:${sideColor};font-weight:700">${(t.side||'').toUpperCase()}</td>
      <td style="padding:9px 14px;color:var(--txt)">${fm(t.size||0)}</td>
      <td style="padding:9px 14px;color:var(--sub)">${fm(t.price||0)}</td>
      <td style="padding:9px 14px;color:var(--red)">${fm(t.fee||0)}</td>
      <td style="padding:9px 14px;color:${pnlColor};font-weight:600">${fm(t.pnl||0)}</td>
    </tr>`;
  }).join('');
}

function showToast(msg){
  let t = document.getElementById('live-toast');
  if(!t){
    t = document.createElement('div');
    t.id = 'live-toast';
    t.style.cssText = `position:fixed;bottom:28px;right:28px;background:var(--s2);
      border:1px solid var(--bdr);border-radius:10px;padding:12px 20px;
      font-family:var(--mono);font-size:11px;color:var(--txt);z-index:9999;
      box-shadow:0 8px 32px rgba(0,0,0,.4);transition:opacity .3s;max-width:400px`;
    document.body.appendChild(t);
  }
  t.textContent = msg;
  t.style.opacity = '1';
  setTimeout(()=>{ t.style.opacity='0'; }, 4000);
}

// Public helper for dev to push data without sync button
function refreshLive(data){
  if(data){
    if(LIVE) Object.assign(LIVE, data);
    else LIVE = data;
    if(data.positions) renderPositions(data.positions);
    if(data.trades) renderTrades(data.trades);
  }
  const el = document.getElementById('p-live');
  if(el) renderLivePanel(el);
}


// ═══════════════════════════════════════════════════════════════
// EXPORT EXCEL — full multi-sheet workbook
// ═══════════════════════════════════════════════════════════════
function exportExcel(){
  if(!D){ alert('Chưa có data'); return; }
  const wb = XLSX.utils.book_new();
  const month = SELM || D.months[D.months.length-1];

  // ── Helper: styled header row ────────────────────────────
  function makeSheet(headers, rows){
    const data = [headers, ...rows];
    const ws = XLSX.utils.aoa_to_sheet(data);
    // Column widths
    ws['!cols'] = headers.map(h=>({wch: Math.max(h.length+2, 14)}));
    return ws;
  }

  // ── Sheet 1: NAV Summary ─────────────────────────────────
  const navRows = D.months.map(m=>{
    const bal  = D.rows.reduce((s,r)=>s+(r.monthly[m]||0),0);
    const dep  = D.rows.reduce((s,r)=>s+((r.dep||{})[m]||0),0);
    const wd   = D.rows.reduce((s,r)=>s+((r.wd||{})[m]||0),0);
    const prev = D.months[D.months.indexOf(m)-1];
    const prevBal = prev ? D.rows.reduce((s,r)=>s+(r.monthly[prev]||0),0) : bal;
    const pnl  = bal - prevBal - dep + wd;
    const ret  = prevBal > 0 ? (pnl/prevBal*100).toFixed(2)+'%' : '—';
    return [m, bal.toFixed(2), dep.toFixed(2), wd.toFixed(2), pnl.toFixed(2), ret];
  });
  XLSX.utils.book_append_sheet(wb,
    makeSheet(['Month','NAV ($)','Deposit ($)','Withdrawal ($)','PnL ($)','Return %'], navRows),
    'NAV Summary');

  // ── Sheet 2: Trader Balance (all months) ─────────────────
  const traderHeaders = ['Trader','Exchange', ...D.months];
  const traderRows = D.rows.map(r=>[
    r.pic, r.loc,
    ...D.months.map(m => r.monthly[m] != null ? r.monthly[m] : '')
  ]);
  XLSX.utils.book_append_sheet(wb,
    makeSheet(traderHeaders, traderRows),
    'Trader Balance');

  // ── Sheet 3: Tháng hiện tại — chi tiết ──────────────────
  const mRows = D.rows
    .filter(r => r.monthly[month] != null)
    .map(r => {
      const months_arr = D.months;
      const idx = months_arr.indexOf(month);
      const prev = idx > 0 ? months_arr[idx-1] : null;
      const bal  = r.monthly[month] || 0;
      const prevBal = prev ? (r.monthly[prev]||0) : 0;
      const dep  = (r.dep||{})[month]||0;
      const wd   = (r.wd||{})[month]||0;
      const pnl  = bal - prevBal - dep + wd;
      const pct  = prevBal > 0 ? (pnl/prevBal*100).toFixed(2)+'%' : '—';
      return [r.pic, r.loc, bal.toFixed(2), prevBal.toFixed(2),
              dep.toFixed(2), wd.toFixed(2), pnl.toFixed(2), pct];
    });
  XLSX.utils.book_append_sheet(wb,
    makeSheet(['Trader','Exchange','Balance','Prev Balance','Deposit','Withdrawal','PnL','PnL %'], mRows),
    month.replace(' ','_'));

  // ── Sheet 4: Fee ─────────────────────────────────────────
  if(D.hf && D.hf.rows){
    const feeLabels = ['Fee','Buy subcription (Copin)','Management Fee','Performance Fee','Other Fee'];
    const feeHeaders = ['Fee Type', ...D.hf.months];
    const feeRows = feeLabels.map(lbl=>{
      const row = D.hf.rows.find(r=>r.label===lbl);
      return [lbl, ...D.hf.months.map(m=>(row&&row.data[m]!=null)?row.data[m].toFixed(2):'0')];
    });
    XLSX.utils.book_append_sheet(wb, makeSheet(feeHeaders, feeRows), 'Fees');
  }

  // ── Sheet 5: Risk Metrics ─────────────────────────────────
  if(D.risk && D.risk.metrics){
    const metricsRows = Object.entries(D.risk.metrics)
      .filter(([,v])=>v!=null)
      .map(([k,v])=>[k, typeof v==='number'?v.toFixed(4):v]);
    XLSX.utils.book_append_sheet(wb,
      makeSheet(['Metric','Value'], metricsRows),
      'Risk Metrics');
  }

  // ── Sheet 6: Volume (if available) ───────────────────────
  if(D.vol && (D.vol.tradingVol||[]).some(v=>v>0)){
    const volHeaders = ['Month','Total Volume ($)','Num Trades','NAV ($)','Fee ($)','PnL ($)'];
    const volRows = D.vol.months.map((m,i)=>[
      m,
      (D.vol.tradingVol[i]||0).toFixed(2),
      D.vol.numTrades[i]||0,
      (D.vol.navByMonth[i]||0).toFixed(2),
      (D.vol.totalFee[i]||0).toFixed(2),
      (D.vol.pnlByMonth[i]||0).toFixed(2),
    ]);
    XLSX.utils.book_append_sheet(wb, makeSheet(volHeaders, volRows), 'Volume');
  }

  // ── Download ─────────────────────────────────────────────
  const fname = `Copin_IF_Report_${month.replace(' ','_')}.xlsx`;
  XLSX.writeFile(wb, fname);
  showExportToast(`✓ Đã xuất ${fname}`, 'var(--grn)');
}

// ═══════════════════════════════════════════════════════════════
// EXPORT PDF — capture current tab as PDF
// ═══════════════════════════════════════════════════════════════
async function exportPDF(){
  if(!D){ alert('Chưa có data'); return; }

  // Find active tab
  const activeTab = document.querySelector('.tab.active');
  const tabName   = activeTab ? activeTab.dataset.tab : 'overview';
  const panelEl   = document.getElementById('p-'+tabName);

  if(!panelEl){ alert('Không tìm thấy nội dung để xuất'); return; }

  const btn = event && event.target;
  if(btn){ btn.disabled=true; btn.textContent='⏳ Đang xuất...'; }

  try {
    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF({ orientation:'landscape', unit:'mm', format:'a4' });
    const pageW = pdf.internal.pageSize.getWidth();
    const pageH = pdf.internal.pageSize.getHeight();
    const margin = 12;

    // Header
    const month = SELM || D.months[D.months.length-1];
    pdf.setFillColor(7, 9, 15);
    pdf.rect(0, 0, pageW, pageH, 'F');
    pdf.setTextColor(221, 228, 240);
    pdf.setFontSize(18);
    pdf.setFont('helvetica','bold');
    pdf.text('COPIN IF', margin, margin+8);
    pdf.setFontSize(10);
    pdf.setTextColor(107, 127, 163);
    pdf.text(`Monthly Report — ${month}`, margin, margin+15);
    pdf.text(`Tab: ${tabName.toUpperCase()}`, margin, margin+20);
    pdf.setFontSize(8);
    pdf.text(`Generated: ${new Date().toLocaleString('vi-VN')}`, pageW-margin, margin+8, {align:'right'});

    // Divider
    pdf.setDrawColor(28, 36, 56);
    pdf.setLineWidth(0.3);
    pdf.line(margin, margin+24, pageW-margin, margin+24);

    // Capture panel as image
    const canvas = await html2canvas(panelEl, {
      backgroundColor: '#07090f',
      scale: 1.5,
      useCORS: true,
      logging: false,
      width: panelEl.scrollWidth,
      height: panelEl.scrollHeight,
      windowWidth: panelEl.scrollWidth,
    });

    const imgData = canvas.toDataURL('image/jpeg', 0.92);
    const imgW    = pageW - margin*2;
    const imgH    = (canvas.height * imgW) / canvas.width;
    const startY  = margin + 28;
    const availH  = pageH - startY - margin;

    if(imgH <= availH){
      // Fits on one page
      pdf.addImage(imgData, 'JPEG', margin, startY, imgW, imgH);
    } else {
      // Multi-page
      let yOffset = 0;
      while(yOffset < imgH){
        if(yOffset > 0) pdf.addPage();
        const sliceH = Math.min(availH, imgH - yOffset);
        const ratio  = sliceH / imgH;

        // Crop canvas slice
        const sliceCanvas = document.createElement('canvas');
        sliceCanvas.width  = canvas.width;
        sliceCanvas.height = Math.round(canvas.height * ratio);
        const ctx = sliceCanvas.getContext('2d');
        ctx.drawImage(canvas,
          0, Math.round((yOffset/imgH)*canvas.height),
          canvas.width, sliceCanvas.height,
          0, 0, sliceCanvas.width, sliceCanvas.height);
        const sliceData = sliceCanvas.toDataURL('image/jpeg', 0.92);

        const drawStartY = yOffset === 0 ? startY : margin;
        pdf.addImage(sliceData, 'JPEG', margin, drawStartY, imgW, sliceH);
        yOffset += availH;
      }
    }

    // Footer on each page
    const totalPages = pdf.internal.getNumberOfPages();
    for(let i=1; i<=totalPages; i++){
      pdf.setPage(i);
      pdf.setFontSize(7);
      pdf.setTextColor(61, 79, 110);
      pdf.text(`COPIN IF — Confidential`, margin, pageH-5);
      pdf.text(`${i} / ${totalPages}`, pageW-margin, pageH-5, {align:'right'});
    }

    const fname = `Copin_IF_${tabName}_${month.replace(' ','_')}.pdf`;
    pdf.save(fname);
    showExportToast(`✓ Đã xuất ${fname}`, 'var(--acc)');

  } catch(e){
    console.error('PDF export error:', e);
    showExportToast('❌ Lỗi xuất PDF: '+e.message, 'var(--red)');
  } finally {
    if(btn){ btn.disabled=false; btn.textContent='↓ PDF'; }
  }
}

// ── Export toast notification ──────────────────────────────────
function showExportToast(msg, color='var(--grn)'){
  let t = document.getElementById('export-toast');
  if(!t){
    t = document.createElement('div');
    t.id = 'export-toast';
    t.style.cssText = `position:fixed;bottom:28px;right:28px;
      background:var(--s2);border:1px solid var(--bdr);border-radius:10px;
      padding:12px 20px;font-family:var(--mono);font-size:11px;
      z-index:9999;box-shadow:0 8px 32px rgba(0,0,0,.5);
      transition:opacity .4s;opacity:0;max-width:380px;pointer-events:none`;
    document.body.appendChild(t);
  }
  t.style.color = color;
  t.textContent = msg;
  t.style.opacity = '1';
  setTimeout(()=>t.style.opacity='0', 3500);
}

function exportCSV(){
  if(!D)return;
  const rows=[['Month','Trader','Platform','Balance','Withdrawal','Deposit']];
  D.months.forEach(m=>D.rows.forEach(r=>{if(r.monthly[m])rows.push([m,r.pic,r.loc,r.monthly[m],r.wd[m]||0,r.dep[m]||0])}));
  const csv=rows.map(r=>r.join(',')).join('\n');
  const a=document.createElement('a');a.href='data:text/csv;charset=utf-8,'+encodeURIComponent(csv);
  a.download='copin_if_'+SELM.replace(' ','')+'.csv';a.click();
}
function resetDash(){
  D=null;SELM=null;
  Object.values(CH).forEach(c=>c&&c.destroy&&c.destroy());CH={};
  document.getElementById('upload-screen').style.display='flex';
  document.getElementById('dash').style.display='none';
}</script>
<script>
  // Auto-load embedded data on page open
  window.addEventListener('DOMContentLoaded', () => {
    if (typeof EMBEDDED_DATA !== 'undefined') {
      loadEmbeddedData();
    }
  });
</script>
</body>
</html>
