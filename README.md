<!DOCTYPE html>
<html lang="sw">
<head>
<meta charset="UTF-8">

<title>MAUZOBOOK - Mfumo wa Biashara</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
:root {
  --bg: #0a0c0f;
  --bg2: #111418;
  --bg3: #181c22;
  --border: #232830;
  --border2: #2e3540;
  --accent: #00d4aa;
  --accent2: #0099ff;
  --accent3: #ff6b35;
  --danger: #ff4757;
  --warn: #ffa502;
  --success: #2ed573;
  --text: #e8edf3;
  --text2: #8a95a3;
  --text3: #555f6d;
  --card-shadow: 0 4px 24px rgba(0,0,0,0.4);
  --radius: 12px;
  --radius-sm: 8px;
  --sidebar: 240px;
  --font: 'Space Grotesk', sans-serif;
  --mono: 'JetBrains Mono', monospace;
}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:var(--font);background:var(--bg);color:var(--text);font-size:14px;line-height:1.6;overflow-x:hidden}
::-webkit-scrollbar{width:4px}
::-webkit-scrollbar-track{background:var(--bg2)}
::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px}

/* ===== AUTH ===== */
#auth-screen{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:9999;backdrop-filter:blur(10px)}
.auth-wrap{width:100%;max-width:420px;padding:20px}
.auth-logo{text-align:center;margin-bottom:32px}
.auth-logo h1{font-size:2rem;font-weight:700;letter-spacing:4px;background:linear-gradient(135deg,var(--accent),var(--accent2));-webkit-background-clip:text;-webkit-text-fill-color:transparent;font-family:var(--mono)}
.auth-logo p{color:var(--text2);font-size:0.85rem;margin-top:6px}
.auth-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:28px}
.auth-tabs{display:flex;gap:4px;margin-bottom:24px;background:var(--bg3);border-radius:var(--radius-sm);padding:4px}
.auth-tab{flex:1;padding:8px;text-align:center;border-radius:6px;cursor:pointer;font-weight:500;font-size:0.9rem;color:var(--text2);transition:all .2s}
.auth-tab.active{background:var(--accent);color:#000;font-weight:700}
.form-field{margin-bottom:16px}
.form-field label{display:block;color:var(--text2);font-size:0.8rem;font-weight:600;letter-spacing:.5px;text-transform:uppercase;margin-bottom:6px}
.form-field input,.form-field select,.form-field textarea{width:100%;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--radius-sm);padding:11px 14px;color:var(--text);font-family:var(--font);font-size:0.9rem;transition:border .2s,box-shadow .2s;outline:none}
.form-field input:focus,.form-field select:focus,.form-field textarea:focus{border-color:var(--accent);box-shadow:0 0 0 3px rgba(0,212,170,.12)}
.form-field select{appearance:none;background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='%238a95a3' viewBox='0 0 20 20'%3E%3Cpath d='M7 7l3 3 3-3'/%3E%3C/svg%3E");background-repeat:no-repeat;background-position:right 12px center;background-size:16px;cursor:pointer}
.form-field select option{background:var(--bg2)}
.forgot-link{text-align:right;margin-top:-8px;margin-bottom:16px}
.forgot-link a{color:var(--accent2);font-size:0.8rem;text-decoration:none}
.btn{display:inline-flex;align-items:center;justify-content:center;gap:8px;padding:11px 20px;border-radius:var(--radius-sm);font-family:var(--font);font-size:0.9rem;font-weight:600;cursor:pointer;border:none;transition:all .2s;text-decoration:none}
.btn-full{width:100%}
.btn-primary{background:linear-gradient(135deg,var(--accent),#00b896);color:#000}
.btn-primary:hover{opacity:.9;transform:translateY(-1px)}
.btn-secondary{background:var(--bg3);color:var(--text);border:1px solid var(--border2)}
.btn-secondary:hover{border-color:var(--accent);color:var(--accent)}
.btn-danger{background:var(--danger);color:#fff}
.btn-danger:hover{opacity:.85}
.btn-success{background:var(--success);color:#000}
.btn-warn{background:var(--warn);color:#000}
.btn-blue{background:var(--accent2);color:#fff}
.btn-blue:hover{opacity:.9}
.btn-sm{padding:7px 13px;font-size:0.8rem;border-radius:6px}
.btn-icon{padding:8px;border-radius:6px;background:var(--bg3);border:1px solid var(--border);color:var(--text2)}
.btn-icon:hover{border-color:var(--accent);color:var(--accent)}

/* ===== LAYOUT ===== */
#app{display:flex;min-height:100vh;display:none}
.sidebar{width:var(--sidebar);background:var(--bg2);border-right:1px solid var(--border);display:flex;flex-direction:column;position:fixed;height:100vh;z-index:100;transition:transform .3s}
.sidebar-logo{padding:20px;border-bottom:1px solid var(--border)}
.sidebar-logo h2{font-size:1.1rem;font-weight:700;letter-spacing:3px;color:var(--accent);font-family:var(--mono)}
.sidebar-logo p{color:var(--text3);font-size:0.75rem;margin-top:2px}
.sidebar-user{padding:14px 16px;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:10px}
.user-avatar{width:36px;height:36px;border-radius:50%;background:linear-gradient(135deg,var(--accent),var(--accent2));display:flex;align-items:center;justify-content:center;font-weight:700;font-size:.9rem;color:#000;flex-shrink:0}
.user-info small{color:var(--text3);font-size:.72rem;display:block}
.user-info strong{font-size:.85rem;display:block}
nav{flex:1;padding:10px 0;overflow-y:auto}
.nav-item{display:flex;align-items:center;gap:12px;padding:10px 16px;color:var(--text2);text-decoration:none;border-left:3px solid transparent;transition:all .2s;cursor:pointer;font-size:.88rem;font-weight:500}
.nav-item:hover{background:rgba(0,212,170,.05);color:var(--text);border-left-color:var(--border2)}
.nav-item.active{background:rgba(0,212,170,.1);color:var(--accent);border-left-color:var(--accent)}
.nav-item i{width:18px;text-align:center;font-size:.9rem}
.nav-badge{background:var(--danger);color:#fff;font-size:.65rem;padding:2px 6px;border-radius:10px;margin-left:auto;font-weight:700}
.nav-badge.warn{background:var(--warn);color:#000}
.sidebar-footer{padding:12px;border-top:1px solid var(--border)}
.sidebar-footer .btn{margin-bottom:6px;font-size:.82rem;padding:9px 14px}
.main{flex:1;margin-left:var(--sidebar);display:flex;flex-direction:column;min-height:100vh}
.topbar{background:var(--bg2);border-bottom:1px solid var(--border);padding:14px 24px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:50}
.topbar-left h2{font-size:1.1rem;font-weight:700}
.topbar-left p{color:var(--text2);font-size:.78rem}
.topbar-right{display:flex;align-items:center;gap:10px}
.notif-btn{position:relative;background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);padding:8px 10px;color:var(--text2);cursor:pointer;font-size:.9rem}
.notif-dot{position:absolute;top:4px;right:4px;width:8px;height:8px;border-radius:50%;background:var(--danger)}
.content{flex:1;padding:24px;overflow-y:auto}
.section{display:none;animation:fadeUp .25s ease}
.section.active{display:block}
@keyframes fadeUp{from{opacity:0;transform:translateY(12px)}to{opacity:1;transform:translateY(0)}}

/* ===== CARDS ===== */
.stats-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:16px;margin-bottom:24px}
.stat-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:20px;position:relative;overflow:hidden}
.stat-card::before{content:'';position:absolute;inset:0;background:linear-gradient(135deg,currentColor 0%,transparent 60%);opacity:.04}
.stat-card .icon{width:42px;height:42px;border-radius:var(--radius-sm);display:flex;align-items:center;justify-content:center;font-size:1.1rem;margin-bottom:12px}
.stat-card .val{font-size:1.5rem;font-weight:700;font-family:var(--mono);line-height:1}
.stat-card .lbl{color:var(--text2);font-size:.78rem;margin-top:4px}
.stat-card .trend{font-size:.72rem;margin-top:8px;color:var(--success)}
.stat-card .trend.down{color:var(--danger)}
.c-green{color:var(--success)}.c-blue{color:var(--accent2)}.c-orange{color:var(--accent3)}.c-red{color:var(--danger)}.c-teal{color:var(--accent)}
.bg-green{background:rgba(46,213,115,.12)}.bg-blue{background:rgba(0,153,255,.12)}.bg-orange{background:rgba(255,107,53,.12)}.bg-red{background:rgba(255,71,87,.12)}.bg-teal{background:rgba(0,212,170,.12)}

/* ===== CARDS ===== */
.card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);margin-bottom:20px}
.card-header{padding:16px 20px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between}
.card-header h3{font-size:.95rem;font-weight:700;display:flex;align-items:center;gap:8px}
.card-header h3 i{color:var(--accent)}
.card-body{padding:20px}

/* ===== TABLES ===== */
.tbl-wrap{overflow-x:auto}
table{width:100%;border-collapse:collapse}
th{text-align:left;padding:10px 14px;font-size:.75rem;font-weight:700;text-transform:uppercase;letter-spacing:.5px;color:var(--text3);background:var(--bg3);border-bottom:1px solid var(--border)}
td{padding:11px 14px;border-bottom:1px solid var(--border);font-size:.87rem;color:var(--text2)}
tr:hover td{background:rgba(255,255,255,.02);color:var(--text)}
tr:last-child td{border-bottom:none}
.td-bold{font-weight:700;color:var(--text);font-family:var(--mono)}

/* ===== BADGES ===== */
.badge{display:inline-flex;align-items:center;padding:3px 9px;border-radius:20px;font-size:.72rem;font-weight:700}
.badge-green{background:rgba(46,213,115,.15);color:var(--success)}
.badge-red{background:rgba(255,71,87,.15);color:var(--danger)}
.badge-warn{background:rgba(255,165,2,.15);color:var(--warn)}
.badge-blue{background:rgba(0,153,255,.15);color:var(--accent2)}
.badge-teal{background:rgba(0,212,170,.15);color:var(--accent)}

/* ===== FORMS ===== */
.form-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px}
.form-row{display:grid;grid-template-columns:1fr 1fr;gap:16px}
.qty-ctrl{display:flex;align-items:center;gap:0}
.qty-btn{width:36px;height:38px;background:var(--bg3);border:1px solid var(--border2);color:var(--text2);cursor:pointer;font-size:1rem;transition:all .2s}
.qty-btn:first-child{border-radius:var(--radius-sm) 0 0 var(--radius-sm)}
.qty-btn:last-child{border-radius:0 var(--radius-sm) var(--radius-sm) 0}
.qty-btn:hover{background:var(--accent);color:#000;border-color:var(--accent)}
.qty-ctrl input{width:60px;text-align:center;border-radius:0;border-left:none;border-right:none;height:38px}
.pay-opts{display:flex;gap:10px;flex-wrap:wrap}
.pay-opt input{display:none}
.pay-label{display:flex;flex-direction:column;align-items:center;padding:10px 16px;border:2px solid var(--border2);border-radius:var(--radius-sm);cursor:pointer;transition:all .2s;font-size:.8rem;gap:5px;color:var(--text2);min-width:80px}
.pay-opt input:checked+.pay-label{border-color:var(--accent);background:rgba(0,212,170,.08);color:var(--accent)}
.pay-label i{font-size:1.1rem}
.total-display{background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);padding:14px;text-align:right}
.total-display .val{font-size:1.8rem;font-weight:700;font-family:var(--mono);color:var(--accent)}
.total-display .lbl{color:var(--text3);font-size:.78rem}

/* ===== PRODUCT CARDS ===== */
.prod-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:16px}
.prod-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);overflow:hidden;transition:border-color .2s}
.prod-card:hover{border-color:var(--accent)}
.prod-card-top{padding:16px;background:var(--bg3);position:relative;display:flex;align-items:center;justify-content:center;min-height:80px}
.prod-card-top i{font-size:2.2rem;opacity:.5}
.prod-cat-badge{position:absolute;top:8px;right:8px;font-size:.65rem;padding:2px 7px;border-radius:20px;font-weight:700}
.prod-stock-badge{position:absolute;top:8px;left:8px;font-size:.65rem;padding:2px 7px;border-radius:20px;font-weight:700}
.prod-body{padding:14px}
.prod-name{font-weight:700;font-size:.92rem;margin-bottom:4px}
.prod-price{font-family:var(--mono);color:var(--accent);font-size:1rem;font-weight:600;margin-bottom:6px}
.prod-stock{color:var(--text3);font-size:.78rem}
.prod-actions{display:flex;gap:6px;margin-top:10px}

/* ===== DEBT ITEMS ===== */
.debt-item{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:16px;display:flex;align-items:center;gap:16px;margin-bottom:10px;transition:border-color .2s}
.debt-item:hover{border-color:var(--border2)}
.debt-avatar{width:40px;height:40px;border-radius:50%;background:rgba(255,71,87,.15);display:flex;align-items:center;justify-content:center;color:var(--danger);font-weight:700;flex-shrink:0}
.debt-info{flex:1}
.debt-info h4{font-size:.9rem;font-weight:700}
.debt-info p{color:var(--text3);font-size:.78rem}
.debt-amount{font-family:var(--mono);color:var(--danger);font-size:1.1rem;font-weight:700}
.debt-actions{display:flex;gap:6px}

/* ===== CHARTS ===== */
.charts-grid{display:grid;grid-template-columns:2fr 1fr;gap:20px;margin-bottom:20px}
.chart-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:20px}
.chart-card h3{font-size:.88rem;font-weight:700;margin-bottom:16px;color:var(--text2)}
canvas{max-height:250px}

/* ===== MODAL ===== */
.modal-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.7);z-index:1000;align-items:center;justify-content:center;backdrop-filter:blur(4px)}
.modal-overlay.open{display:flex}
.modal{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);width:90%;max-width:520px;max-height:90vh;overflow-y:auto;animation:fadeUp .2s ease}
.modal-header{padding:16px 20px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between}
.modal-header h3{font-size:.95rem;font-weight:700}
.modal-close{background:none;border:none;color:var(--text3);font-size:1.3rem;cursor:pointer;padding:4px}
.modal-close:hover{color:var(--text)}
.modal-body{padding:20px}
.modal-footer{padding:16px 20px;border-top:1px solid var(--border);display:flex;gap:10px;justify-content:flex-end}

/* ===== NOTIFICATIONS ===== */
.notif-panel{position:fixed;top:56px;right:16px;width:320px;background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);box-shadow:var(--card-shadow);z-index:200;display:none;animation:fadeUp .15s ease}
.notif-panel.open{display:block}
.notif-header{padding:12px 16px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;font-size:.85rem;font-weight:700}
.notif-item{padding:12px 16px;border-bottom:1px solid var(--border);display:flex;gap:10px;align-items:flex-start;cursor:pointer}
.notif-item:hover{background:var(--bg3)}
.notif-item:last-child{border-bottom:none}
.notif-icon{width:32px;height:32px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:.85rem;flex-shrink:0}
.notif-text{font-size:.8rem;line-height:1.4}
.notif-text strong{display:block;font-size:.82rem;margin-bottom:2px}
.notif-time{color:var(--text3);font-size:.7rem}

/* ===== AI CHAT ===== */
.ai-fab{position:fixed;bottom:80px;right:20px;width:52px;height:52px;border-radius:50%;background:linear-gradient(135deg,var(--accent2),var(--accent));border:none;cursor:pointer;font-size:1.3rem;color:#000;box-shadow:0 4px 20px rgba(0,212,170,.3);z-index:150;transition:all .2s}
.ai-fab:hover{transform:scale(1.1)}
.ai-panel{position:fixed;bottom:80px;right:20px;width:340px;height:480px;background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);display:none;flex-direction:column;z-index:200;box-shadow:var(--card-shadow);animation:fadeUp .2s ease}
.ai-panel.open{display:flex}
.ai-head{padding:12px 16px;background:linear-gradient(135deg,var(--accent2),var(--accent));border-radius:var(--radius) var(--radius) 0 0;display:flex;align-items:center;justify-content:space-between;color:#000}
.ai-head-left{display:flex;align-items:center;gap:10px}
.ai-dot{width:8px;height:8px;border-radius:50%;background:#000;opacity:.5}
.ai-body{flex:1;overflow-y:auto;padding:12px;background:var(--bg);display:flex;flex-direction:column;gap:8px}
.ai-msg{padding:10px 12px;border-radius:10px;max-width:90%;font-size:.83rem;line-height:1.5}
.ai-msg.bot{background:var(--bg2);border:1px solid var(--border);align-self:flex-start}
.ai-msg.user{background:var(--accent2);color:#fff;align-self:flex-end}
.ai-quick{padding:8px 12px;display:flex;gap:6px;overflow-x:auto;background:var(--bg3);border-top:1px solid var(--border)}
.ai-quick button{white-space:nowrap;padding:5px 10px;border-radius:20px;background:var(--bg);border:1px solid var(--border2);color:var(--text2);cursor:pointer;font-size:.75rem;transition:all .2s}
.ai-quick button:hover{border-color:var(--accent);color:var(--accent)}
.ai-typing{padding:6px 12px;font-size:.75rem;color:var(--text3);font-style:italic;display:none}
.ai-input{padding:10px 12px;display:flex;gap:8px;border-top:1px solid var(--border);background:var(--bg2)}
.ai-input input{flex:1;background:var(--bg3);border:1px solid var(--border);border-radius:20px;padding:8px 14px;color:var(--text);font-size:.83rem;outline:none}
.ai-input input:focus{border-color:var(--accent)}
.ai-input button{background:var(--accent);color:#000;border:none;border-radius:50%;width:34px;height:34px;cursor:pointer;font-size:.85rem;flex-shrink:0}

/* ===== MOBILE NAV ===== */
.mob-nav{display:none;position:fixed;bottom:0;left:0;right:0;background:var(--bg2);border-top:1px solid var(--border);z-index:100;padding:6px 0 8px}
.mob-nav-inner{display:flex;justify-content:space-around}
.mob-btn{display:flex;flex-direction:column;align-items:center;gap:3px;background:none;border:none;color:var(--text3);cursor:pointer;padding:4px 8px;font-size:.65rem;transition:color .2s}
.mob-btn.active{color:var(--accent)}
.mob-btn i{font-size:1.1rem}

/* ===== ALERTS ===== */
.toast{position:fixed;top:20px;right:20px;background:var(--bg2);border:1px solid var(--border);border-left:4px solid var(--accent);border-radius:var(--radius-sm);padding:12px 16px;min-width:260px;max-width:350px;z-index:9000;box-shadow:var(--card-shadow);display:none;animation:slideIn .3s ease}
.toast.show{display:flex;align-items:center;gap:10px}
.toast.success{border-left-color:var(--success)}.toast.danger{border-left-color:var(--danger)}.toast.warn{border-left-color:var(--warn)}
.toast i{font-size:1rem}.toast .toast-msg{flex:1;font-size:.85rem}.toast .toast-close{background:none;border:none;color:var(--text3);cursor:pointer;font-size:1rem}
@keyframes slideIn{from{transform:translateX(100%);opacity:0}to{transform:translateX(0);opacity:1}}

/* ===== SEARCH BAR ===== */
.search-bar{display:flex;align-items:center;gap:10px;background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);padding:8px 14px;margin-bottom:16px}
.search-bar i{color:var(--text3)}
.search-bar input{flex:1;background:none;border:none;outline:none;color:var(--text);font-size:.88rem}
.search-bar input::placeholder{color:var(--text3)}

/* ===== PROFILE ===== */
.profile-hero{background:linear-gradient(135deg,var(--accent2),var(--accent));border-radius:var(--radius);padding:24px;margin-bottom:20px;display:flex;align-items:center;gap:20px}
.profile-av{width:64px;height:64px;border-radius:50%;background:rgba(0,0,0,.2);display:flex;align-items:center;justify-content:center;font-size:1.8rem;font-weight:700;color:#fff;border:3px solid rgba(255,255,255,.3)}
.profile-info h2{font-size:1.2rem;font-weight:700;color:#fff}
.profile-info p{color:rgba(255,255,255,.7);font-size:.83rem}

/* ===== EXPENSES ===== */
.exp-item{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius-sm);padding:14px;display:flex;align-items:center;gap:12px;margin-bottom:8px}
.exp-icon{width:38px;height:38px;border-radius:var(--radius-sm);display:flex;align-items:center;justify-content:center;font-size:.95rem}
.exp-info{flex:1}
.exp-info h4{font-size:.88rem;font-weight:600}
.exp-info p{color:var(--text3);font-size:.75rem}
.exp-amount{font-family:var(--mono);color:var(--danger);font-weight:700}

/* ===== PROFIT CALC ===== */
.profit-summary{display:grid;grid-template-columns:repeat(3,1fr);gap:16px;margin-bottom:20px}
.profit-item{background:var(--bg3);border-radius:var(--radius-sm);padding:16px;text-align:center}
.profit-item .val{font-size:1.4rem;font-weight:700;font-family:var(--mono)}
.profit-item .lbl{color:var(--text3);font-size:.75rem;margin-top:4px}

/* ===== CATEGORIES ===== */
.cat-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(140px,1fr));gap:12px;margin-bottom:20px}
.cat-item{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius-sm);padding:16px;text-align:center;cursor:pointer;transition:all .2s}
.cat-item:hover,.cat-item.selected{border-color:var(--accent);background:rgba(0,212,170,.06)}
.cat-item i{font-size:1.5rem;margin-bottom:8px;display:block}
.cat-item span{font-size:.8rem;font-weight:600}

/* ===== RESPONSIVE ===== */
@media(max-width:900px){
  .sidebar{transform:translateX(-100%)}
  .sidebar.open{transform:translateX(0)}
  .main{margin-left:0}
  .mob-nav{display:block}
  .content{padding:16px;padding-bottom:80px}
  .charts-grid{grid-template-columns:1fr}
  .ai-panel,.ai-fab{bottom:72px}
  .form-row{grid-template-columns:1fr}
}
@media(max-width:600px){
  .stats-grid{grid-template-columns:1fr 1fr}
  .prod-grid{grid-template-columns:1fr 1fr}
  .profit-summary{grid-template-columns:1fr}
}
</style>
</head>
<body>

<!-- ===== AUTH SCREEN ===== -->
<div id="auth-screen">
  <div class="auth-wrap">
    <div class="auth-logo">
      <h1>MAUZOBOOK</h1>
      <p>Mfumo wa Biashara — Professional Edition</p>
    </div>
    <div class="auth-card">
      <div class="auth-tabs">
        <div class="auth-tab active" onclick="switchAuth('login')">Ingia</div>
        <div class="auth-tab" onclick="switchAuth('register')">Jiandikishe</div>
      </div>
      <!-- LOGIN -->
      <div id="auth-login">
        <div class="form-field">
          <label>Jina la Mtumiaji</label>
          <input type="text" id="l-user" placeholder="Weka jina lako">
        </div>
        <div class="form-field">
          <label>Nenosiri</label>
          <input type="password" id="l-pass" placeholder="Weka nenosiri" onkeydown="if(event.key==='Enter')doLogin()">
        </div>
        <div class="forgot-link"><a href="#" onclick="showForgot()">Umesahau nenosiri?</a></div>
        <button class="btn btn-primary btn-full" onclick="doLogin()"><i class="fas fa-sign-in-alt"></i> Ingia</button>
      </div>
      <!-- REGISTER -->
      <div id="auth-register" style="display:none">
        <div class="form-field">
          <label>Jina Kamili</label>
          <input type="text" id="r-name" placeholder="Jina lako kamili">
        </div>
        <div class="form-field">
          <label>Jina la Mtumiaji</label>
          <input type="text" id="r-user" placeholder="Chagua username">
        </div>
        <div class="form-field">
          <label>Barua Pepe</label>
          <input type="email" id="r-email" placeholder="jina@mfano.com">
        </div>
        <div class="form-field">
          <label>Nenosiri</label>
          <input type="password" id="r-pass" placeholder="Nenosiri (angalau herufi 6)">
        </div>
        <div class="form-field">
          <label>Jina la Duka/Biashara</label>
          <input type="text" id="r-biz" placeholder="Mfano: Duka la Mama Fatuma">
        </div>
        <button class="btn btn-primary btn-full" onclick="doRegister()"><i class="fas fa-user-plus"></i> Jiandikishe</button>
      </div>
      <!-- FORGOT -->
      <div id="auth-forgot" style="display:none">
        <p style="color:var(--text2);font-size:.85rem;margin-bottom:16px">Tutakutumia kiungo cha kubadili nenosiri kwenye barua pepe uliyosajili nayo.</p>
        <div class="form-field">
          <label>Jina la Mtumiaji</label>
          <input type="text" id="f-user" placeholder="Username yako">
        </div>
        <button class="btn btn-primary btn-full" onclick="doForgot()">Tuma Kiungo cha Kubadili Nenosiri</button>
        <p style="text-align:center;margin-top:12px"><a href="#" onclick="switchAuth('login')" style="color:var(--text3);font-size:.82rem">← Rudi Kuingia</a></p>
      </div>
    </div>
  </div>
</div>

<!-- ===== MAIN APP ===== -->
<div id="app">
  <!-- SIDEBAR -->
  <aside class="sidebar" id="sidebar">
    <div class="sidebar-logo">
      <h2>MAUZOBOOK</h2>
      <p id="biz-name-sidebar">Biashara Yako</p>
    </div>
    <div class="sidebar-user">
      <div class="user-avatar" id="user-av">M</div>
      <div class="user-info">
        <strong id="user-name-sidebar">Mtumiaji</strong>
        <small>Admin</small>
      </div>
    </div>
    <nav>
      <div class="nav-item active" onclick="goTo('dash')"><i class="fas fa-chart-pie"></i> Dashibodi</div>
      <div class="nav-item" onclick="goTo('sales')"><i class="fas fa-shopping-cart"></i> Mauzo <span class="nav-badge" id="nb-sales" style="display:none">0</span></div>
      <div class="nav-item" onclick="goTo('purchases')"><i class="fas fa-truck-loading"></i> Manunuzi</div>
      <div class="nav-item" onclick="goTo('products')"><i class="fas fa-boxes"></i> Bidhaa <span class="nav-badge warn" id="nb-stock" style="display:none">0</span></div>
      <div class="nav-item" onclick="goTo('categories')"><i class="fas fa-tags"></i> Makundi</div>
      <div class="nav-item" onclick="goTo('customers')"><i class="fas fa-users"></i> Wateja</div>
      <div class="nav-item" onclick="goTo('debts')"><i class="fas fa-money-check-alt"></i> Madeni <span class="nav-badge" id="nb-debts" style="display:none">0</span></div>
      <div class="nav-item" onclick="goTo('expenses')"><i class="fas fa-file-invoice-dollar"></i> Matumizi</div>
      <div class="nav-item" onclick="goTo('profit')"><i class="fas fa-chart-line"></i> Faida</div>
      <div class="nav-item" onclick="goTo('stats')"><i class="fas fa-chart-bar"></i> Takwimu</div>
      <div class="nav-item" onclick="goTo('profile')"><i class="fas fa-user-circle"></i> Akaunti</div>
    </nav>
    <div class="sidebar-footer">
      <button class="btn btn-secondary btn-full" onclick="openAI()"><i class="fas fa-robot"></i> AI Msaidizi</button>
      <button class="btn btn-secondary btn-full" onclick="doExportExcel()"><i class="fas fa-file-excel"></i> Hamisha Excel</button>
      <button class="btn btn-danger btn-full" onclick="doLogout()"><i class="fas fa-sign-out-alt"></i> Toka</button>
    </div>
  </aside>

  <!-- MAIN CONTENT -->
  <main class="main">
    <!-- TOPBAR -->
    <div class="topbar">
      <div class="topbar-left">
        <h2 id="page-title">Dashibodi</h2>
        <p id="page-sub">Habari za biashara yako leo</p>
      </div>
      <div class="topbar-right">
        <button class="btn btn-secondary btn-sm" onclick="doScan()"><i class="fas fa-barcode"></i> Scan</button>
        <button class="btn btn-primary btn-sm" onclick="goTo('sales');openNewSale()"><i class="fas fa-plus"></i> Uza</button>
        <div class="notif-btn" onclick="toggleNotif()">
          <i class="fas fa-bell"></i>
          <span class="notif-dot" id="notif-dot"></span>
        </div>
      </div>
    </div>

    <!-- NOTIFICATIONS PANEL -->
    <div class="notif-panel" id="notif-panel">
      <div class="notif-header">
        <span><i class="fas fa-bell"></i> Arifa</span>
        <button onclick="clearNotifs()" style="background:none;border:none;color:var(--text3);cursor:pointer;font-size:.75rem">Futa Zote</button>
      </div>
      <div id="notif-list"></div>
    </div>

    <div class="content">

      <!-- ===== DASHBOARD ===== -->
      <div class="section active" id="sec-dash">
        <div class="stats-grid">
          <div class="stat-card" style="color:var(--accent)">
            <div class="icon bg-teal c-teal"><i class="fas fa-shopping-cart"></i></div>
            <div class="val">TSh <span id="d-today">0</span></div>
            <div class="lbl">Mauzo ya Leo</div>
            <div class="trend" id="d-trend-sales">↑ +0 wiki iliyopita</div>
          </div>
          <div class="stat-card" style="color:var(--accent2)">
            <div class="icon bg-blue c-blue"><i class="fas fa-users"></i></div>
            <div class="val"><span id="d-cust">0</span></div>
            <div class="lbl">Wateja Wote</div>
          </div>
          <div class="stat-card" style="color:var(--success)">
            <div class="icon bg-green c-green"><i class="fas fa-chart-line"></i></div>
            <div class="val">TSh <span id="d-profit">0</span></div>
            <div class="lbl">Faida ya Mwezi</div>
          </div>
          <div class="stat-card" style="color:var(--danger)">
            <div class="icon bg-red c-red"><i class="fas fa-money-check-alt"></i></div>
            <div class="val">TSh <span id="d-debts">0</span></div>
            <div class="lbl">Madeni Yote</div>
          </div>
          <div class="stat-card" style="color:var(--warn)">
            <div class="icon bg-orange c-orange"><i class="fas fa-boxes"></i></div>
            <div class="val"><span id="d-prods">0</span></div>
            <div class="lbl">Bidhaa</div>
          </div>
          <div class="stat-card" style="color:var(--accent3)">
            <div class="icon bg-orange c-orange"><i class="fas fa-file-invoice-dollar"></i></div>
            <div class="val">TSh <span id="d-expenses">0</span></div>
            <div class="lbl">Matumizi Mwezi</div>
          </div>
        </div>
        <div class="charts-grid">
          <div class="chart-card">
            <h3>Mauzo - Wiki 7 Zilizopita</h3>
            <canvas id="weekChart"></canvas>
          </div>
          <div class="chart-card">
            <h3>Bidhaa Bora</h3>
            <canvas id="topProdChart"></canvas>
          </div>
        </div>
        <div class="card">
          <div class="card-header"><h3><i class="fas fa-history"></i> Shughuli za Hivi Karibuni</h3></div>
          <div class="card-body" id="activity-list"><p style="color:var(--text3);text-align:center;padding:10px">Bado hakuna shughuli</p></div>
        </div>
      </div>

      <!-- ===== SALES ===== -->
      <div class="section" id="sec-sales">
        <div class="card" id="sale-form-card">
          <div class="card-header">
            <h3><i class="fas fa-shopping-cart"></i> Ongeza Uuzaji Mpya</h3>
            <button class="btn btn-secondary btn-sm" onclick="clearSaleForm()"><i class="fas fa-redo"></i> Safisha</button>
          </div>
          <div class="card-body">
            <div class="form-grid">
              <div class="form-field">
                <label>Bidhaa</label>
                <select id="s-product" onchange="onProductSelect()">
                  <option value="">— Chagua bidhaa —</option>
                </select>
              </div>
              <div class="form-field">
                <label>Idadi</label>
                <div class="qty-ctrl">
                  <button class="qty-btn" onclick="adjustQty(-1)">−</button>
                  <input type="number" id="s-qty" value="1" min="1" oninput="calcTotal()">
                  <button class="qty-btn" onclick="adjustQty(1)">+</button>
                </div>
              </div>
              <div class="form-field">
                <label>Bei ya Kuuza (TSh)</label>
                <input type="number" id="s-price" placeholder="0" oninput="calcTotal()">
              </div>
              <div class="form-field">
                <label>Mteja</label>
                <select id="s-customer">
                  <option value="cash">Fedha Taslimu</option>
                </select>
              </div>
              <div class="form-field">
                <label>Tarehe</label>
                <input type="date" id="s-date">
              </div>
              <div class="form-field">
                <label>Kumbumbu (Hiari)</label>
                <input type="text" id="s-note" placeholder="Maelezo ya ziada...">
              </div>
            </div>
            <div class="form-field">
              <label>Aina ya Malipo</label>
              <div class="pay-opts">
                <label class="pay-opt"><input type="radio" name="pay" value="cash" checked><span class="pay-label"><i class="fas fa-money-bill"></i> Fedha</span></label>
                <label class="pay-opt"><input type="radio" name="pay" value="mpesa"><span class="pay-label"><i class="fas fa-mobile-alt"></i> M-Pesa</span></label>
                <label class="pay-opt"><input type="radio" name="pay" value="credit"><span class="pay-label"><i class="fas fa-clock"></i> Deni</span></label>
                <label class="pay-opt"><input type="radio" name="pay" value="bank"><span class="pay-label"><i class="fas fa-university"></i> Benki</span></label>
              </div>
            </div>
            <div class="total-display" style="margin-top:16px">
              <div class="lbl">Jumla ya Malipo</div>
              <div class="val" id="s-total">TSh 0</div>
            </div>
            <div style="display:flex;gap:10px;margin-top:16px;flex-wrap:wrap">
              <button class="btn btn-secondary" onclick="printPDFReceipt()"><i class="fas fa-file-pdf"></i> Risiti PDF</button>
              <button class="btn btn-primary" onclick="saveSale()"><i class="fas fa-save"></i> Hifadhi Uuzaji</button>
            </div>
          </div>
        </div>
        <div class="card">
          <div class="card-header">
            <h3><i class="fas fa-list"></i> Historia ya Mauzo</h3>
            <div style="display:flex;gap:8px">
              <input type="date" id="filter-date" style="background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);padding:6px 10px;color:var(--text);font-size:.8rem" onchange="renderSalesTable()">
              <button class="btn btn-secondary btn-sm" onclick="document.getElementById('filter-date').value='';renderSalesTable()">Onyesha Zote</button>
            </div>
          </div>
          <div class="card-body">
            <div class="search-bar">
              <i class="fas fa-search"></i>
              <input type="text" id="sales-search" placeholder="Tafuta uuzaji..." oninput="renderSalesTable()">
            </div>
            <div class="tbl-wrap"><table id="sales-tbl">
              <thead><tr><th>Tarehe</th><th>Bidhaa</th><th>Idadi</th><th>Bei</th><th>Jumla</th><th>Mteja</th><th>Malipo</th><th>Vitendo</th></tr></thead>
              <tbody id="sales-tbody"></tbody>
            </table></div>
          </div>
        </div>
      </div>

      <!-- ===== PURCHASES ===== -->
      <div class="section" id="sec-purchases">
        <div class="card">
          <div class="card-header">
            <h3><i class="fas fa-truck-loading"></i> Rekodi ya Ununuzi</h3>
            <button class="btn btn-primary btn-sm" onclick="openPurchaseModal()"><i class="fas fa-plus"></i> Ongeza Ununuzi</button>
          </div>
          <div class="card-body">
            <div class="tbl-wrap"><table>
              <thead><tr><th>Tarehe</th><th>Bidhaa</th><th>Idadi</th><th>Bei ya Kununua</th><th>Jumla</th><th>Muuzaji</th></tr></thead>
              <tbody id="purchases-tbody"></tbody>
            </table></div>
          </div>
        </div>
      </div>

      <!-- ===== PRODUCTS ===== -->
      <div class="section" id="sec-products">
        <div style="display:flex;gap:10px;flex-wrap:wrap;margin-bottom:16px;align-items:center">
          <div class="search-bar" style="flex:1;margin:0">
            <i class="fas fa-search"></i>
            <input type="text" id="prod-search" placeholder="Tafuta bidhaa..." oninput="renderProducts()">
          </div>
          <select id="prod-cat-filter" style="background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);padding:9px 14px;color:var(--text);font-size:.85rem" onchange="renderProducts()">
            <option value="">Makundi Yote</option>
          </select>
          <button class="btn btn-primary btn-sm" onclick="openProductModal()"><i class="fas fa-plus"></i> Ongeza Bidhaa</button>
        </div>
        <div id="prod-grid" class="prod-grid"></div>
      </div>

      <!-- ===== CATEGORIES ===== -->
      <div class="section" id="sec-categories">
        <div class="card">
          <div class="card-header">
            <h3><i class="fas fa-tags"></i> Makundi ya Bidhaa</h3>
            <button class="btn btn-primary btn-sm" onclick="openCatModal()"><i class="fas fa-plus"></i> Ongeza Kundi</button>
          </div>
          <div class="card-body">
            <div id="cat-grid" class="cat-grid"></div>
          </div>
        </div>
      </div>

      <!-- ===== CUSTOMERS ===== -->
      <div class="section" id="sec-customers">
        <div class="card">
          <div class="card-header">
            <h3><i class="fas fa-users"></i> Orodha ya Wateja</h3>
            <button class="btn btn-primary btn-sm" onclick="openCustomerModal()"><i class="fas fa-user-plus"></i> Ongeza Mteja</button>
          </div>
          <div class="card-body">
            <div class="search-bar">
              <i class="fas fa-search"></i>
              <input type="text" id="cust-search" placeholder="Tafuta kwa jina au simu..." oninput="renderCustomers()">
            </div>
            <div class="tbl-wrap"><table>
              <thead><tr><th>Jina</th><th>Simu</th><th>Anwani</th><th>Deni (TSh)</th><th>Mauzo</th><th>Vitendo</th></tr></thead>
              <tbody id="cust-tbody"></tbody>
            </table></div>
          </div>
        </div>
      </div>

      <!-- ===== DEBTS ===== -->
      <div class="section" id="sec-debts">
        <div style="display:flex;gap:10px;margin-bottom:16px;flex-wrap:wrap">
          <button class="btn btn-warn" onclick="sendWhatsApp()"><i class="fab fa-whatsapp"></i> WhatsApp Reminder</button>
          <button class="btn btn-blue" onclick="sendSMSReminder()"><i class="fas fa-sms"></i> SMS Reminder</button>
        </div>
        <div id="debts-list"></div>
      </div>

      <!-- ===== EXPENSES ===== -->
      <div class="section" id="sec-expenses">
        <div class="card">
          <div class="card-header">
            <h3><i class="fas fa-file-invoice-dollar"></i> Matumizi ya Biashara</h3>
            <button class="btn btn-primary btn-sm" onclick="openExpenseModal()"><i class="fas fa-plus"></i> Ongeza Matumizi</button>
          </div>
          <div class="card-body">
            <div id="expenses-list"></div>
          </div>
        </div>
      </div>

      <!-- ===== PROFIT ===== -->
      <div class="section" id="sec-profit">
        <div class="profit-summary">
          <div class="profit-item">
            <div class="val c-teal" id="p-revenue">TSh 0</div>
            <div class="lbl">Mapato Yote</div>
          </div>
          <div class="profit-item">
            <div class="val c-red" id="p-cost">TSh 0</div>
            <div class="lbl">Gharama Zote</div>
          </div>
          <div class="profit-item">
            <div class="val c-green" id="p-profit">TSh 0</div>
            <div class="lbl">Faida Halisi</div>
          </div>
        </div>
        <div class="card">
          <div class="card-header"><h3><i class="fas fa-calculator"></i> Hesabu ya Faida</h3></div>
          <div class="card-body">
            <div class="form-grid">
              <div class="form-field">
                <label>Kipindi</label>
                <select id="profit-period" onchange="calcProfit()">
                  <option value="today">Leo</option>
                  <option value="week">Wiki hii</option>
                  <option value="month" selected>Mwezi huu</option>
                  <option value="year">Mwaka huu</option>
                </select>
              </div>
            </div>
            <div class="tbl-wrap" style="margin-top:16px">
              <table>
                <thead><tr><th>Aina</th><th>Kiasi</th><th>Asilimia</th></tr></thead>
                <tbody id="profit-tbody"></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- ===== STATS ===== -->
      <div class="section" id="sec-stats">
        <div class="stats-grid" style="margin-bottom:20px">
          <div class="stat-card">
            <div class="icon bg-teal c-teal"><i class="fas fa-calendar"></i></div>
            <div class="val">TSh <span id="st-month">0</span></div>
            <div class="lbl">Mauzo Mwezi</div>
          </div>
          <div class="stat-card">
            <div class="icon bg-blue c-blue"><i class="fas fa-calendar-week"></i></div>
            <div class="val">TSh <span id="st-week">0</span></div>
            <div class="lbl">Mauzo Wiki</div>
          </div>
          <div class="stat-card">
            <div class="icon bg-green c-green"><i class="fas fa-star"></i></div>
            <div class="val"><span id="st-top-prod">—</span></div>
            <div class="lbl">Bidhaa Bora</div>
          </div>
          <div class="stat-card">
            <div class="icon bg-orange c-orange"><i class="fas fa-user-star"></i></div>
            <div class="val"><span id="st-top-cust">—</span></div>
            <div class="lbl">Mteja Bora</div>
          </div>
        </div>
        <div class="charts-grid">
          <div class="chart-card">
            <h3>Mwenendo wa Mauzo (Miezi 6)</h3>
            <canvas id="monthChart"></canvas>
          </div>
          <div class="chart-card">
            <h3>Makundi ya Mauzo</h3>
            <canvas id="catChart"></canvas>
          </div>
        </div>
      </div>

      <!-- ===== PROFILE ===== -->
      <div class="section" id="sec-profile">
        <div class="profile-hero">
          <div class="profile-av" id="profile-av">M</div>
          <div class="profile-info">
            <h2 id="profile-name">Mtumiaji</h2>
            <p id="profile-biz">Biashara Yako</p>
          </div>
        </div>
        <div class="card">
          <div class="card-header"><h3><i class="fas fa-edit"></i> Hariri Akaunti</h3></div>
          <div class="card-body">
            <div class="form-grid">
              <div class="form-field">
                <label>Jina Kamili</label>
                <input type="text" id="pr-name" placeholder="Jina lako">
              </div>
              <div class="form-field">
                <label>Jina la Biashara</label>
                <input type="text" id="pr-biz" placeholder="Jina la duka/biashara">
              </div>
              <div class="form-field">
                <label>Simu</label>
                <input type="text" id="pr-phone" placeholder="+255...">
              </div>
              <div class="form-field">
                <label>Anwani</label>
                <input type="text" id="pr-address" placeholder="Mji, Mtaa">
              </div>
            </div>
            <div class="form-field">
              <label>Nenosiri Jipya (acha tupu kama hubadilishi)</label>
              <input type="password" id="pr-pass" placeholder="Nenosiri jipya">
            </div>
            <button class="btn btn-primary" onclick="saveProfile()"><i class="fas fa-save"></i> Hifadhi Mabadiliko</button>
          </div>
        </div>
        <div class="card" style="margin-top:16px">
          <div class="card-header"><h3><i class="fas fa-database"></i> Data</h3></div>
          <div class="card-body" style="display:flex;gap:10px;flex-wrap:wrap">
            <button class="btn btn-secondary" onclick="doExportExcel()"><i class="fas fa-file-excel"></i> Hamisha Excel</button>
            <button class="btn btn-secondary" onclick="doExportJSON()"><i class="fas fa-file-code"></i> Hamisha JSON</button>
            <button class="btn btn-secondary" onclick="doImportJSON()"><i class="fas fa-file-import"></i> Ingiza Data</button>
            <button class="btn btn-danger" onclick="clearAllData()"><i class="fas fa-trash-alt"></i> Futa Data Yote</button>
          </div>
        </div>
        <div class="card" style="margin-top:16px">
          <div class="card-header"><h3><i class="fas fa-shield-alt"></i> Msaada na Sera</h3></div>
          <div class="card-body" style="display:flex;gap:10px;flex-wrap:wrap">
            <button class="btn btn-secondary" onclick="openModal('modal-policy')"><i class="fas fa-shield-alt"></i> Sera ya Faragha</button>
            <button class="btn btn-secondary" onclick="openModal('modal-help')"><i class="fas fa-life-ring"></i> Msaada</button>
            <button class="btn btn-danger" onclick="doLogout()"><i class="fas fa-sign-out-alt"></i> Toka</button>
          </div>
        </div>
      </div>

    </div><!-- /content -->
  </main>
</div>

<!-- ===== MOBILE NAV ===== -->
<div class="mob-nav">
  <div class="mob-nav-inner">
    <button class="mob-btn active" onclick="goTo('dash')"><i class="fas fa-chart-pie"></i><span>Nyumbani</span></button>
    <button class="mob-btn" onclick="goTo('sales');openNewSale()"><i class="fas fa-shopping-cart"></i><span>Uza</span></button>
    <button class="mob-btn" onclick="goTo('products')"><i class="fas fa-boxes"></i><span>Bidhaa</span></button>
    <button class="mob-btn" onclick="goTo('customers')"><i class="fas fa-users"></i><span>Wateja</span></button>
    <button class="mob-btn" onclick="goTo('debts')"><i class="fas fa-money-check-alt"></i><span>Deni</span></button>
  </div>
</div>

<!-- ===== AI PANEL ===== -->
<button class="ai-fab" id="ai-fab" onclick="openAI()">🤖</button>
<div class="ai-panel" id="ai-panel">
  <div class="ai-head">
    <div class="ai-head-left">
      <span style="font-size:1.2rem">🤖</span>
      <div><div style="font-weight:700;font-size:.88rem">Mauzobook AI</div><div style="font-size:.72rem;opacity:.7">Online <span class="ai-dot"></span></div></div>
    </div>
    <button onclick="closeAI()" style="background:none;border:none;cursor:pointer;font-size:1.2rem;color:rgba(0,0,0,.6)">×</button>
  </div>
  <div class="ai-quick">
    <button onclick="aiAsk('Mauzo ya leo ni kiasi gani?')">📈 Mauzo Leo</button>
    <button onclick="aiAsk('Bidhaa zipi zinaisha stock?')">📦 Stock</button>
    <button onclick="aiAsk('Orodhesha madeni yote')">💰 Madeni</button>
    <button onclick="aiAsk('Nipe ushauri wa biashara')">🧠 Ushauri</button>
    <button onclick="aiAsk('Bidhaa gani inauza zaidi?')">🏆 Bora</button>
  </div>
  <div class="ai-body" id="ai-msgs">
    <div class="ai-msg bot">👋 <b>Karibu Mauzobook AI!</b><br>Ninaweza kukusaidia na:<br>• Ripoti za mauzo<br>• Usimamizi wa stock<br>• Madeni ya wateja<br>• Ushauri wa biashara</div>
  </div>
  <div class="ai-typing" id="ai-typing">🤖 AI inaandika...</div>
  <div class="ai-input">
    <input type="text" id="ai-input" placeholder="Uliza swali lolote..." onkeydown="if(event.key==='Enter')sendAI()">
    <button onclick="sendAI()">➤</button>
  </div>
</div>

<!-- ===== MODALS ===== -->
<!-- Product Modal -->
<div class="modal-overlay" id="modal-product">
  <div class="modal">
    <div class="modal-header">
      <h3 id="prod-modal-title"><i class="fas fa-box"></i> Ongeza Bidhaa</h3>
      <button class="modal-close" onclick="closeModal('modal-product')">×</button>
    </div>
    <div class="modal-body">
      <input type="hidden" id="pm-id">
      <div class="form-grid">
        <div class="form-field"><label>Jina la Bidhaa</label><input type="text" id="pm-name" placeholder="Mfano: Sukari"></div>
        <div class="form-field"><label>Kundi</label><select id="pm-cat"></select></div>
        <div class="form-field"><label>Bei ya Kununua (TSh)</label><input type="number" id="pm-buyprice" placeholder="0"></div>
        <div class="form-field"><label>Bei ya Kuuza (TSh)</label><input type="number" id="pm-price" placeholder="0"></div>
        <div class="form-field"><label>Stock ya Awali</label><input type="number" id="pm-stock" placeholder="0"></div>
        <div class="form-field"><label>Kiwango cha Chini (Alert)</label><input type="number" id="pm-minstock" placeholder="5"></div>
        <div class="form-field"><label>Barcode (Hiari)</label><input type="text" id="pm-barcode" placeholder="Nambari ya barcode"></div>
        <div class="form-field"><label>Kitengo</label><select id="pm-unit"><option>Kilo</option><option>Lita</option><option>Mkoba</option><option>Kipande</option><option>Dazeni</option><option>Karatasi</option></select></div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary" onclick="closeModal('modal-product')">Acha</button>
      <button class="btn btn-primary" onclick="saveProduct()">Hifadhi</button>
    </div>
  </div>
</div>

<!-- Customer Modal -->
<div class="modal-overlay" id="modal-customer">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-user-plus"></i> Ongeza Mteja</h3>
      <button class="modal-close" onclick="closeModal('modal-customer')">×</button>
    </div>
    <div class="modal-body">
      <input type="hidden" id="cm-id">
      <div class="form-grid">
        <div class="form-field"><label>Jina Kamili</label><input type="text" id="cm-name" placeholder="Jina la mteja"></div>
        <div class="form-field"><label>Simu</label><input type="text" id="cm-phone" placeholder="+255..."></div>
        <div class="form-field"><label>Anwani</label><input type="text" id="cm-address" placeholder="Mji, Mtaa"></div>
        <div class="form-field"><label>Email (Hiari)</label><input type="email" id="cm-email" placeholder="email@..."></div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary" onclick="closeModal('modal-customer')">Acha</button>
      <button class="btn btn-primary" onclick="saveCustomer()">Hifadhi</button>
    </div>
  </div>
</div>

<!-- Category Modal -->
<div class="modal-overlay" id="modal-cat">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-tag"></i> Ongeza Kundi</h3>
      <button class="modal-close" onclick="closeModal('modal-cat')">×</button>
    </div>
    <div class="modal-body">
      <div class="form-field"><label>Jina la Kundi</label><input type="text" id="cat-name" placeholder="Mfano: Vyakula"></div>
      <div class="form-field">
        <label>Ikoni</label>
        <div id="cat-icons" style="display:flex;gap:10px;flex-wrap:wrap;margin-top:8px">
          <span onclick="selectIcon(this)" data-icon="fa-utensils" class="cat-icon-pick" style="padding:8px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:1.2rem">🍽️</span>
          <span onclick="selectIcon(this)" data-icon="fa-tshirt" class="cat-icon-pick" style="padding:8px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:1.2rem">👕</span>
          <span onclick="selectIcon(this)" data-icon="fa-pills" class="cat-icon-pick" style="padding:8px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:1.2rem">💊</span>
          <span onclick="selectIcon(this)" data-icon="fa-broom" class="cat-icon-pick" style="padding:8px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:1.2rem">🧹</span>
          <span onclick="selectIcon(this)" data-icon="fa-mobile" class="cat-icon-pick" style="padding:8px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:1.2rem">📱</span>
          <span onclick="selectIcon(this)" data-icon="fa-box" class="cat-icon-pick selected" style="padding:8px 12px;border:2px solid var(--accent);border-radius:6px;cursor:pointer;font-size:1.2rem">📦</span>
        </div>
        <input type="hidden" id="cat-icon" value="fa-box">
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary" onclick="closeModal('modal-cat')">Acha</button>
      <button class="btn btn-primary" onclick="saveCat()">Hifadhi</button>
    </div>
  </div>
</div>

<!-- Purchase Modal -->
<div class="modal-overlay" id="modal-purchase">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-truck-loading"></i> Ongeza Ununuzi</h3>
      <button class="modal-close" onclick="closeModal('modal-purchase')">×</button>
    </div>
    <div class="modal-body">
      <div class="form-grid">
        <div class="form-field"><label>Bidhaa</label><select id="pur-prod"></select></div>
        <div class="form-field"><label>Idadi Iliyonunuliwa</label><input type="number" id="pur-qty" placeholder="0" min="1"></div>
        <div class="form-field"><label>Bei ya Kununua (TSh)</label><input type="number" id="pur-price" placeholder="0"></div>
        <div class="form-field"><label>Muuzaji</label><input type="text" id="pur-supplier" placeholder="Jina la muuzaji"></div>
        <div class="form-field"><label>Tarehe</label><input type="date" id="pur-date"></div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary" onclick="closeModal('modal-purchase')">Acha</button>
      <button class="btn btn-primary" onclick="savePurchase()">Hifadhi</button>
    </div>
  </div>
</div>

<!-- Expense Modal -->
<div class="modal-overlay" id="modal-expense">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-file-invoice-dollar"></i> Ongeza Matumizi</h3>
      <button class="modal-close" onclick="closeModal('modal-expense')">×</button>
    </div>
    <div class="modal-body">
      <div class="form-grid">
        <div class="form-field"><label>Maelezo</label><input type="text" id="ex-desc" placeholder="Mfano: Kodi ya duka"></div>
        <div class="form-field"><label>Kiasi (TSh)</label><input type="number" id="ex-amount" placeholder="0"></div>
        <div class="form-field"><label>Aina</label>
          <select id="ex-type">
            <option>Kodi</option><option>Mishahara</option><option>Umeme/Maji</option>
            <option>Usafirishaji</option><option>Matangazo</option><option>Nyingine</option>
          </select>
        </div>
        <div class="form-field"><label>Tarehe</label><input type="date" id="ex-date"></div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary" onclick="closeModal('modal-expense')">Acha</button>
      <button class="btn btn-primary" onclick="saveExpense()">Hifadhi</button>
    </div>
  </div>
</div>

<!-- Policy Modal -->
<div class="modal-overlay" id="modal-policy">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-shield-alt"></i> Sera ya Faragha</h3>
      <button class="modal-close" onclick="closeModal('modal-policy')">×</button>
    </div>
    <div class="modal-body" style="font-size:.87rem;color:var(--text2);line-height:1.7">
      <p><b style="color:var(--text)">1. Data Yako</b><br>Taarifa zote za biashara yako (mauzo, wateja, bidhaa, madeni, matumizi) zinahifadhiwa kwa usalama kwenye database yetu (Supabase) na zinapatikana kwako pekee ukiwa umeingia kwenye akaunti yako.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">2. Usalama</b><br>Nenosiri lako halihifadhiwi kwa maandishi wazi — linasimbwa (encrypted) na mfumo wa uthibitisho (authentication) tunaotumia. Hakuna mfanyakazi anayeweza kuona nenosiri lako.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">3. Kutumia Data</b><br>Hatuuzi wala kushiriki taarifa zako binafsi au za biashara yako na wahusika wengine bila ridhaa yako.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">4. Haki Zako</b><br>Unaweza kuhamisha (export) au kufuta data yako yote wakati wowote kupitia sehemu ya "Data" kwenye Akaunti yako.</p>
      <p style="margin-top:12px;color:var(--text3);font-size:.8rem">Sera hii inaweza kubadilishwa na mmiliki wa mfumo huu. Wasiliana na msimamizi wa biashara yako kwa maswali zaidi.</p>
    </div>
    <div class="modal-footer">
      <button class="btn btn-primary" onclick="closeModal('modal-policy')">Nimeelewa</button>
    </div>
  </div>
</div>

<!-- Help Modal -->
<div class="modal-overlay" id="modal-help">
  <div class="modal">
    <div class="modal-header">
      <h3><i class="fas fa-life-ring"></i> Msaada</h3>
      <button class="modal-close" onclick="closeModal('modal-help')">×</button>
    </div>
    <div class="modal-body" style="font-size:.87rem;color:var(--text2);line-height:1.7">
      <p><b style="color:var(--text)">📈 Kuongeza Uuzaji</b><br>Nenda "Mauzo" → chagua bidhaa, weka idadi na bei → chagua aina ya malipo → bonyeza "Hifadhi Uuzaji".</p>
      <p style="margin-top:12px"><b style="color:var(--text)">📦 Kusimamia Stock</b><br>Nenda "Bidhaa" kuongeza/kuhariri bidhaa. Mfumo utakuarifu stock ikiwa chini ya kiwango ulichoweka.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">💰 Madeni</b><br>Uuzaji wa aina ya "Deni" huongeza deni kiotomatiki. Nenda "Madeni" kuona na kutuma vikumbusho vya WhatsApp.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">🤖 AI Msaidizi</b><br>Bonyeza kitufe cha roboti chini kulia kuuliza maswali kuhusu biashara yako kwa lugha ya kawaida.</p>
      <p style="margin-top:12px"><b style="color:var(--text)">📞 Mawasiliano</b><br>Kwa msaada zaidi, wasiliana na msimamizi wa mfumo huu.</p>
    </div>
    <div class="modal-footer">
      <button class="btn btn-primary" onclick="closeModal('modal-help')">Funga</button>
    </div>
  </div>
</div>

<!-- TOAST -->
<div class="toast" id="toast">
  <i class="fas fa-check-circle"></i>
  <span class="toast-msg" id="toast-msg"></span>
  <button class="toast-close" onclick="document.getElementById('toast').classList.remove('show')">×</button>
</div>

<script>
// ===== SUPABASE CONFIG =====
// TODO: Weka URL na Anon Key za mradi wako wa Supabase hapa (Project Settings -> API)
const SUPABASE_URL = 'https://YOUR-PROJECT-REF.supabase.co';
const SUPABASE_ANON_KEY = 'YOUR-ANON-PUBLIC-KEY';
const sb = window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// ===== DATA STORE =====
let DB = {
  currentUser: null,
  sales: [],
  customers: [],
  products: [],
  categories: [],
  debts: [],
  expenses: [],
  purchases: [],
  notifications: [],
  activities: []
};

// ===== AUTH =====
function switchAuth(tab) {
  ['login','register','forgot'].forEach(t => document.getElementById('auth-'+t).style.display = 'none');
  document.getElementById('auth-'+tab).style.display = 'block';
  document.querySelectorAll('.auth-tab').forEach((el,i) => {
    el.classList.toggle('active', (i===0&&tab==='login')||(i===1&&tab==='register'));
  });
}

async function doLogin() {
  const u = document.getElementById('l-user').value.trim();
  const p = document.getElementById('l-pass').value;
  if (!u||!p) return toast('Jaza sehemu zote!','warn');
  const { data: profile, error: perr } = await sb.from('profiles').select('*').eq('username', u).maybeSingle();
  if (perr || !profile) return toast('Jina au nenosiri si sahihi!','danger');
  const { data, error } = await sb.auth.signInWithPassword({ email: profile.email, password: p });
  if (error) return toast('Jina au nenosiri si sahihi!','danger');
  await enterApp(profile);
}

async function doRegister() {
  const name = document.getElementById('r-name').value.trim();
  const u = document.getElementById('r-user').value.trim();
  const email = document.getElementById('r-email').value.trim();
  const p = document.getElementById('r-pass').value;
  const biz = document.getElementById('r-biz').value.trim();
  if (!name||!u||!email||!p||!biz) return toast('Jaza sehemu zote!','warn');
  if (p.length<6) return toast('Nenosiri lazima liwe na herufi 6+!','warn');

  const { data: existing } = await sb.from('profiles').select('id').eq('username', u).maybeSingle();
  if (existing) return toast('Username tayari ipo!','danger');

  const { data, error } = await sb.auth.signUp({ email, password: p });
  if (error) return toast('Hitilafu: '+error.message,'danger');
  const uid = data.user && data.user.id;
  if (!uid) return toast('Hitilafu ya usajili, jaribu tena!','danger');

  const profile = { id: uid, username: u, email, name, bizname: biz, phone: '', address: '' };
  const { error: perr } = await sb.from('profiles').insert(profile);
  if (perr) return toast('Hitilafu kuhifadhi profaili: '+perr.message,'danger');

  toast('Akaunti imefunguliwa! Unaingizwa...','success');

  // Auto-login: ingiza mtumiaji moja kwa moja baada ya kujiandikisha
  if (data.session) {
    await enterApp(profile);
  } else {
    const { data: sdata, error: serr } = await sb.auth.signInWithPassword({ email, password: p });
    if (serr) {
      toast('Kagua barua pepe yako kuthibitisha akaunti, kisha ingia.','info');
      switchAuth('login');
      return;
    }
    await enterApp(profile);
  }
}

async function doForgot() {
  const u = document.getElementById('f-user').value.trim();
  if (!u) return toast('Weka jina la mtumiaji!','warn');
  const { data: profile, error } = await sb.from('profiles').select('email').eq('username', u).maybeSingle();
  if (error || !profile) return toast('Mtumiaji hajapatikana!','danger');
  const { error: rerr } = await sb.auth.resetPasswordForEmail(profile.email);
  if (rerr) return toast('Hitilafu: '+rerr.message,'danger');
  toast('Kiungo cha kubadili nenosiri kimetumwa kwenye barua pepe yako!','success');
  switchAuth('login');
}

function showForgot() { switchAuth('login'); document.getElementById('auth-forgot').style.display='block'; document.getElementById('auth-login').style.display='none'; }

async function enterApp(profile) {
  DB.currentUser = profile;
  await loadUserData();
  document.getElementById('auth-screen').style.display='none';
  document.getElementById('app').style.display='flex';
  document.getElementById('user-name-sidebar').textContent = profile.name||profile.username;
  document.getElementById('biz-name-sidebar').textContent = profile.bizname||'Biashara';
  document.getElementById('user-av').textContent = (profile.name||profile.username).charAt(0).toUpperCase();
  document.getElementById('profile-av').textContent = (profile.name||profile.username).charAt(0).toUpperCase();
  document.getElementById('profile-name').textContent = profile.name||profile.username;
  document.getElementById('profile-biz').textContent = profile.bizname||'';
  document.getElementById('pr-name').value = profile.name||'';
  document.getElementById('pr-biz').value = profile.bizname||'';
  document.getElementById('pr-phone').value = profile.phone||'';
  document.getElementById('pr-address').value = profile.address||'';
  initApp();
}

async function doLogout() {
  if (!confirm('Toka kwenye mfumo?')) return;
  await sb.auth.signOut();
  DB.currentUser=null;
  document.getElementById('app').style.display='none';
  document.getElementById('auth-screen').style.display='flex';
  document.getElementById('l-user').value='';
  document.getElementById('l-pass').value='';
  switchAuth('login');
}

// ===== SUPABASE DATA SYNC =====
// Kila rekodi (sale, product, customer, n.k.) inahifadhiwa kama safu moja
// kwenye jedwali 'mauzobook_data' likiwa na kind (aina) + id + JSON ya data yenyewe.
// Angalia faili la mauzobook-schema.sql kwa muundo kamili wa database + RLS policies.
const DB_KINDS = ['sales','customers','products','categories','debts','expenses','purchases','activities'];

async function dbUpsert(kind, obj) {
  if (!DB.currentUser) return;
  const { error } = await sb.from('mauzobook_data').upsert({
    user_id: DB.currentUser.id, kind, id: obj.id, data: obj, updated_at: new Date().toISOString()
  });
  if (error) toast('Hitilafu kuhifadhi data: '+error.message,'danger');
}
async function dbDelete(kind, id) {
  if (!DB.currentUser) return;
  const { error } = await sb.from('mauzobook_data').delete()
    .eq('user_id', DB.currentUser.id).eq('kind', kind).eq('id', id);
  if (error) toast('Hitilafu kufuta data: '+error.message,'danger');
}
async function dbClearAll() {
  if (!DB.currentUser) return;
  const { error } = await sb.from('mauzobook_data').delete().eq('user_id', DB.currentUser.id);
  if (error) toast('Hitilafu kufuta data: '+error.message,'danger');
}
async function loadUserData() {
  DB_KINDS.forEach(k => DB[k] = []);
  if (!DB.currentUser) return;
  const { data, error } = await sb.from('mauzobook_data').select('*').eq('user_id', DB.currentUser.id);
  if (error) { toast('Hitilafu kupakia data: '+error.message,'danger'); return; }
  (data||[]).forEach(row => { if (DB[row.kind]) DB[row.kind].push(row.data); });
  ['sales','purchases','expenses','activities'].forEach(k => DB[k].sort((a,b)=>b.id-a.id));
}

// ===== APP INIT =====
function initApp() {
  // Seed demo data kwa mtumiaji mpya, ikiwa hana bidhaa/wateja/makundi bado
  let needsSeed = false;
  if (DB.categories.length===0) {
    DB.categories = [
      {id:1,name:'Vyakula',icon:'🍽️'},
      {id:2,name:'Vinywaji',icon:'🥤'},
      {id:3,name:'Nyumbani',icon:'🏠'},
      {id:4,name:'Nguo',icon:'👕'},
      {id:5,name:'Dawa',icon:'💊'},
      {id:6,name:'Nyingine',icon:'📦'}
    ];
    needsSeed = true;
  }
  if (DB.products.length===0) {
    DB.products = [
      {id:7,name:'Sukari',category:'Vyakula',buyprice:2200,price:2500,stock:15,minstock:5,unit:'Kilo',barcode:''},
      {id:8,name:'Unga wa Ngano',category:'Vyakula',buyprice:1500,price:1800,stock:30,minstock:5,unit:'Kilo',barcode:''},
      {id:9,name:'Mafuta ya Kupikia',category:'Vyakula',buyprice:2800,price:3200,stock:4,minstock:5,unit:'Lita',barcode:''},
      {id:10,name:'Sabuni',category:'Nyumbani',buyprice:1200,price:1500,stock:20,minstock:5,unit:'Kipande',barcode:''}
    ];
    needsSeed = true;
  }
  if (DB.customers.length===0) {
    DB.customers = [
      {id:11,name:'Ali Mohamed',phone:'0755123456',address:'Dar es Salaam',email:''},
      {id:12,name:'Fatuma Juma',phone:'0767654321',address:'Arusha',email:''}
    ];
    needsSeed = true;
  }
  if (needsSeed) {
    DB.categories.forEach(c=>dbUpsert('categories',c));
    DB.products.forEach(p=>dbUpsert('products',p));
    DB.customers.forEach(c=>dbUpsert('customers',c));
  }
  populateSelects();
  renderAll();
  setDate();
  initCharts();
  updateDash();
  addNotif('info','Karibu Mauzobook!','Mfumo umewashwa kikamilifu');
}

function setDate() {
  const t = new Date().toISOString().split('T')[0];
  ['s-date','pur-date','ex-date'].forEach(id=>{ const el=document.getElementById(id); if(el) el.value=t; });
}

function populateSelects() {
  // Product select in sales
  const ps = document.getElementById('s-product');
  ps.innerHTML = '<option value="">— Chagua bidhaa —</option>';
  DB.products.forEach(p => ps.innerHTML += `<option value="${p.id}">${p.name} (${fmt(p.price)})</option>`);
  // Product in purchase
  const pp = document.getElementById('pur-prod');
  if(pp){ pp.innerHTML='<option value="">— Chagua —</option>'; DB.products.forEach(p=>pp.innerHTML+=`<option value="${p.id}">${p.name}</option>`); }
  // Customer select
  const cs = document.getElementById('s-customer');
  cs.innerHTML = '<option value="cash">Fedha Taslimu</option>';
  DB.customers.forEach(c => cs.innerHTML += `<option value="${c.id}">${c.name}</option>`);
  // Cat in product modal
  const cp = document.getElementById('pm-cat');
  if(cp){ cp.innerHTML=''; DB.categories.forEach(c=>cp.innerHTML+=`<option>${c.name}</option>`); }
  // Cat filter
  const cf = document.getElementById('prod-cat-filter');
  if(cf){ cf.innerHTML='<option value="">Makundi Yote</option>'; DB.categories.forEach(c=>cf.innerHTML+=`<option>${c.name}</option>`); }
}

function renderAll() {
  renderSalesTable();
  renderCustomers();
  renderProducts();
  renderDebts();
  renderExpenses();
  renderPurchases();
  renderCategories();
  renderActivities();
  updateDash();
  calcProfit();
  updateStats();
}

// ===== NAVIGATION =====
const pageTitles = {
  dash:'Dashibodi','sales':'Mauzo',purchases:'Manunuzi',products:'Bidhaa',categories:'Makundi',
  customers:'Wateja',debts:'Madeni',expenses:'Matumizi',profit:'Faida',stats:'Takwimu',profile:'Akaunti'
};
const pageSubs = {
  dash:'Habari za biashara yako leo','sales':'Simamia mauzo yako',purchases:'Rekodi za ununuzi',
  products:'Bidhaa na stock',categories:'Makundi ya bidhaa',customers:'Wateja wako',
  debts:'Madeni ya kukusanya',expenses:'Matumizi ya biashara',profit:'Hesabu ya faida',
  stats:'Takwimu za kina',profile:'Mipangilio ya akaunti'
};

function goTo(sec) {
  document.querySelectorAll('.section').forEach(s=>s.classList.remove('active'));
  const el = document.getElementById('sec-'+sec);
  if(el) el.classList.add('active');
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  const nav = [...document.querySelectorAll('.nav-item')].find(n=>n.getAttribute('onclick')&&n.getAttribute('onclick').includes("'"+sec+"'"));
  if(nav) nav.classList.add('active');
  document.querySelectorAll('.mob-btn').forEach(b=>b.classList.remove('active'));
  const mb = [...document.querySelectorAll('.mob-btn')].find(b=>b.getAttribute('onclick')&&b.getAttribute('onclick').includes("'"+sec+"'"));
  if(mb) mb.classList.add('active');
  document.getElementById('page-title').textContent = pageTitles[sec]||sec;
  document.getElementById('page-sub').textContent = pageSubs[sec]||'';
  if(sec==='profit') calcProfit();
  if(sec==='stats') updateStats();
}

// ===== SALES =====
function onProductSelect() {
  const pid = parseInt(document.getElementById('s-product').value);
  const prod = DB.products.find(p=>p.id===pid);
  if(prod) { document.getElementById('s-price').value=prod.price; calcTotal(); }
}
function adjustQty(d) {
  const i = document.getElementById('s-qty');
  i.value = Math.max(1, (parseInt(i.value)||1)+d);
  calcTotal();
}
function calcTotal() {
  const q = parseInt(document.getElementById('s-qty').value)||0;
  const p = parseInt(document.getElementById('s-price').value)||0;
  document.getElementById('s-total').textContent = 'TSh '+fmt(q*p);
}
function clearSaleForm() {
  document.getElementById('s-product').value='';
  document.getElementById('s-qty').value='1';
  document.getElementById('s-price').value='';
  document.getElementById('s-total').textContent='TSh 0';
  document.getElementById('s-customer').value='cash';
  document.getElementById('s-note').value='';
  document.querySelector('input[name="pay"][value="cash"]').checked=true;
  setDate();
}
function openNewSale() { clearSaleForm(); document.getElementById('sale-form-card').scrollIntoView({behavior:'smooth'}); }

function saveSale() {
  const pid = parseInt(document.getElementById('s-product').value);
  const qty = parseInt(document.getElementById('s-qty').value)||0;
  const price = parseInt(document.getElementById('s-price').value)||0;
  const cid = document.getElementById('s-customer').value;
  const date = document.getElementById('s-date').value;
  const pay = document.querySelector('input[name="pay"]:checked').value;
  const note = document.getElementById('s-note').value;
  if(!pid||!qty||!price) return toast('Jaza bidhaa, idadi na bei!','warn');
  const prod = DB.products.find(p=>p.id===pid);
  if(!prod) return toast('Bidhaa haipo!','danger');
  if(prod.stock<qty) return toast(`Stock haitoshi! Iliyobaki: ${prod.stock}`,'danger');
  const custName = cid==='cash'?'Fedha Taslimu':(DB.customers.find(c=>c.id==cid)||{name:'?'}).name;
  const sale = {id:Date.now(),date,product:prod.name,productId:pid,qty,price,total:qty*price,customer:custName,customerId:cid,payment:pay,note,status:pay==='credit'?'pending':'completed'};
  DB.sales.unshift(sale);
  prod.stock -= qty;
  dbUpsert('sales', sale);
  dbUpsert('products', prod);
  if(pay==='credit'&&cid!=='cash') {
    const cust = DB.customers.find(c=>c.id==cid);
    const debt = {id:Date.now()+1,customer:custName,customerId:cid,amount:qty*price,date,status:'pending',saleId:sale.id};
    DB.debts.push(debt);
    dbUpsert('debts', debt);
    if(cust) { addNotif('warn',`Deni Jipya`,`${custName}: TSh ${fmt(qty*price)}`); }
  }
  if(prod.stock<=prod.minstock) addNotif('warn','Stock Chini',`${prod.name} imebaki ${prod.stock} tu!`);
  addActivity('cart',`Uuzaji: ${prod.name} × ${qty} = TSh ${fmt(qty*price)}`);
  renderAll();
  clearSaleForm();
  toast('Uuzaji umehifadhiwa!','success');
}

function renderSalesTable() {
  const q = (document.getElementById('sales-search').value||'').toLowerCase();
  const fd = document.getElementById('filter-date').value;
  let list = DB.sales.filter(s=>{
    const m = !q||s.product.toLowerCase().includes(q)||s.customer.toLowerCase().includes(q);
    const d = !fd||s.date===fd;
    return m&&d;
  });
  const tb = document.getElementById('sales-tbody');
  if(!list.length){tb.innerHTML='<tr><td colspan="8" style="text-align:center;color:var(--text3)">Hakuna rekodi</td></tr>';return;}
  tb.innerHTML = list.map(s=>`
    <tr>
      <td>${s.date}</td>
      <td class="td-bold">${s.product}</td>
      <td>${s.qty}</td>
      <td>${fmt(s.price)}</td>
      <td class="td-bold">${fmt(s.total)}</td>
      <td>${s.customer}</td>
      <td><span class="badge badge-${s.payment==='cash'?'teal':s.payment==='credit'?'red':'blue'}">${payLabel(s.payment)}</span></td>
      <td><button class="btn btn-icon btn-sm" onclick="deleteSale(${s.id})" title="Futa"><i class="fas fa-trash"></i></button></td>
    </tr>`).join('');
}
function payLabel(p){return{cash:'Fedha',mpesa:'M-Pesa',credit:'Deni',bank:'Benki'}[p]||p;}
function deleteSale(id){if(!confirm('Futa uuzaji huu?'))return;DB.sales=DB.sales.filter(s=>s.id!==id);dbDelete('sales',id);renderAll();toast('Imefutwa','success');}

// ===== PRODUCTS =====
function openProductModal(id) {
  const prod = id?DB.products.find(p=>p.id===id):null;
  document.getElementById('prod-modal-title').innerHTML=`<i class="fas fa-box"></i> ${prod?'Hariri':'Ongeza'} Bidhaa`;
  document.getElementById('pm-id').value=id||'';
  document.getElementById('pm-name').value=prod?prod.name:'';
  document.getElementById('pm-buyprice').value=prod?prod.buyprice:'';
  document.getElementById('pm-price').value=prod?prod.price:'';
  document.getElementById('pm-stock').value=prod?prod.stock:'';
  document.getElementById('pm-minstock').value=prod?prod.minstock:5;
  document.getElementById('pm-barcode').value=prod?prod.barcode:'';
  const cu = document.getElementById('pm-unit');
  if(cu&&prod) cu.value=prod.unit||'Kipande';
  const cc = document.getElementById('pm-cat');
  if(cc&&prod) [...cc.options].forEach(o=>o.selected=o.value===prod.category);
  openModal('modal-product');
}
function saveProduct() {
  const id = parseInt(document.getElementById('pm-id').value)||null;
  const p = {
    id:id||Date.now(),
    name:document.getElementById('pm-name').value.trim(),
    category:document.getElementById('pm-cat').value,
    buyprice:parseInt(document.getElementById('pm-buyprice').value)||0,
    price:parseInt(document.getElementById('pm-price').value)||0,
    stock:parseInt(document.getElementById('pm-stock').value)||0,
    minstock:parseInt(document.getElementById('pm-minstock').value)||5,
    unit:document.getElementById('pm-unit').value,
    barcode:document.getElementById('pm-barcode').value.trim()
  };
  if(!p.name||!p.price) return toast('Jaza jina na bei!','warn');
  if(id) { const i=DB.products.findIndex(x=>x.id===id); if(i>-1) DB.products[i]=p; }
  else DB.products.push(p);
  dbUpsert('products', p);
  populateSelects();
  renderAll();
  closeModal('modal-product');
  toast(id?'Bidhaa imesasishwa!':'Bidhaa imeongezwa!','success');
}
function deleteProduct(id){if(!confirm('Futa bidhaa hii?'))return;DB.products=DB.products.filter(p=>p.id!==id);dbDelete('products',id);populateSelects();renderAll();toast('Bidhaa imefutwa','success');}

function renderProducts() {
  const q=(document.getElementById('prod-search').value||'').toLowerCase();
  const cf=document.getElementById('prod-cat-filter').value;
  const list=DB.products.filter(p=>(!q||p.name.toLowerCase().includes(q))&&(!cf||p.category===cf));
  const g=document.getElementById('prod-grid');
  if(!list.length){g.innerHTML='<p style="color:var(--text3);grid-column:1/-1">Hakuna bidhaa</p>';return;}
  g.innerHTML=list.map(p=>{
    const low=p.stock<=p.minstock;
    const vlow=p.stock===0;
    return`<div class="prod-card">
      <div class="prod-card-top">
        <span class="prod-cat-badge badge-blue badge">${p.category}</span>
        ${low?`<span class="prod-stock-badge badge-${vlow?'red':'warn'} badge">${vlow?'Imeisha':'Inakwisha'}</span>`:''}
        <i class="fas fa-box" style="color:var(--accent);opacity:.6"></i>
      </div>
      <div class="prod-body">
        <div class="prod-name">${p.name}</div>
        <div class="prod-price">${fmt(p.price)} TSh</div>
        <div class="prod-stock">Stock: <b style="color:${low?'var(--danger)':'var(--success)'}">${p.stock}</b> ${p.unit} | Bei Nunua: ${fmt(p.buyprice)}</div>
        <div class="prod-actions">
          <button class="btn btn-icon btn-sm" onclick="openProductModal(${p.id})"><i class="fas fa-edit"></i></button>
          <button class="btn btn-danger btn-sm" onclick="deleteProduct(${p.id})"><i class="fas fa-trash"></i></button>
        </div>
      </div>
    </div>`;
  }).join('');
}

// ===== CATEGORIES =====
function openCatModal() { document.getElementById('cat-name').value=''; openModal('modal-cat'); }
function selectIcon(el) {
  document.querySelectorAll('.cat-icon-pick').forEach(x=>{x.style.border='1px solid var(--border)';x.classList.remove('selected');});
  el.style.border='2px solid var(--accent)';el.classList.add('selected');
  document.getElementById('cat-icon').value=el.getAttribute('data-icon');
}
const iconEmoji={'fa-utensils':'🍽️','fa-tshirt':'👕','fa-pills':'💊','fa-broom':'🧹','fa-mobile':'📱','fa-box':'📦'};
function saveCat() {
  const n=document.getElementById('cat-name').value.trim();
  const ic=document.getElementById('cat-icon').value;
  if(!n) return toast('Weka jina!','warn');
  const c = {id:Date.now(),name:n,icon:iconEmoji[ic]||'📦'};
  DB.categories.push(c);
  dbUpsert('categories', c);
  populateSelects();renderAll();closeModal('modal-cat');toast('Kundi limeongezwa!','success');
}
function renderCategories() {
  const g=document.getElementById('cat-grid');
  g.innerHTML=DB.categories.map(c=>`
    <div class="cat-item">
      <i style="font-size:1.8rem">${c.icon}</i>
      <span>${c.name}</span>
      <div style="margin-top:6px;font-size:.72rem;color:var(--text3)">${DB.products.filter(p=>p.category===c.name).length} bidhaa</div>
      <button class="btn btn-danger btn-sm" style="margin-top:8px" onclick="deleteCat(${c.id})"><i class="fas fa-trash"></i></button>
    </div>`).join('');
}
function deleteCat(id){if(!confirm('Futa kundi hili?'))return;DB.categories=DB.categories.filter(c=>c.id!==id);dbDelete('categories',id);populateSelects();renderAll();toast('Imefutwa','success');}

// ===== CUSTOMERS =====
function openCustomerModal(id) {
  const c=id?DB.customers.find(x=>x.id===id):null;
  document.getElementById('cm-id').value=id||'';
  document.getElementById('cm-name').value=c?c.name:'';
  document.getElementById('cm-phone').value=c?c.phone:'';
  document.getElementById('cm-address').value=c?c.address:'';
  document.getElementById('cm-email').value=c?c.email:'';
  openModal('modal-customer');
}
function saveCustomer() {
  const id=parseInt(document.getElementById('cm-id').value)||null;
  const c={id:id||Date.now(),name:document.getElementById('cm-name').value.trim(),phone:document.getElementById('cm-phone').value.trim(),address:document.getElementById('cm-address').value.trim(),email:document.getElementById('cm-email').value.trim()};
  if(!c.name) return toast('Weka jina la mteja!','warn');
  if(id){const i=DB.customers.findIndex(x=>x.id===id);if(i>-1)DB.customers[i]=c;}else DB.customers.push(c);
  dbUpsert('customers', c);
  populateSelects();renderAll();closeModal('modal-customer');toast(id?'Mteja amesasishwa!':'Mteja ameongezwa!','success');
}
function deleteCustomer(id){if(!confirm('Futa mteja huyu?'))return;DB.customers=DB.customers.filter(c=>c.id!==id);dbDelete('customers',id);populateSelects();renderAll();toast('Imefutwa','success');}
function renderCustomers() {
  const q=(document.getElementById('cust-search').value||'').toLowerCase();
  const list=DB.customers.filter(c=>!q||c.name.toLowerCase().includes(q)||c.phone.includes(q));
  const tb=document.getElementById('cust-tbody');
  if(!list.length){tb.innerHTML='<tr><td colspan="6" style="text-align:center;color:var(--text3)">Hakuna wateja</td></tr>';return;}
  tb.innerHTML=list.map(c=>{
    const debt=DB.debts.filter(d=>d.customerId==c.id).reduce((s,d)=>s+d.amount,0);
    const sales=DB.sales.filter(s=>s.customerId==c.id).length;
    return`<tr>
      <td class="td-bold">${c.name}</td>
      <td>${c.phone}</td>
      <td>${c.address}</td>
      <td class="${debt>0?'td-bold':''}"><span style="color:${debt>0?'var(--danger)':'var(--success)'}">${fmt(debt)}</span></td>
      <td>${sales}</td>
      <td style="display:flex;gap:5px">
        <button class="btn btn-icon btn-sm" onclick="openCustomerModal(${c.id})"><i class="fas fa-edit"></i></button>
        <button class="btn btn-danger btn-sm" onclick="deleteCustomer(${c.id})"><i class="fas fa-trash"></i></button>
      </td>
    </tr>`;
  }).join('');
}

// ===== DEBTS =====
function renderDebts() {
  const list=document.getElementById('debts-list');
  const active=DB.debts.filter(d=>d.status!=='paid');
  if(!active.length){list.innerHTML='<p style="color:var(--success);text-align:center;padding:20px">✓ Hakuna madeni!<br><small style="color:var(--text3)">Wateja wote wamelipa</small></p>';updateDash();return;}
  list.innerHTML=active.map(d=>`
    <div class="debt-item">
      <div class="debt-avatar">${d.customer.charAt(0).toUpperCase()}</div>
      <div class="debt-info"><h4>${d.customer}</h4><p>${d.date}</p></div>
      <div style="text-align:right">
        <div class="debt-amount">${fmt(d.amount)}</div>
        <span class="badge badge-${d.status==='overdue'?'red':'warn'}" style="margin-top:4px">${d.status==='overdue'?'Imechelewa':'Inasubiri'}</span>
      </div>
      <div class="debt-actions">
        <button class="btn btn-success btn-sm" onclick="markPaid(${d.id})"><i class="fas fa-check"></i></button>
        <button class="btn btn-secondary btn-sm" onclick="contactWA('${d.customer}')"><i class="fab fa-whatsapp"></i></button>
      </div>
    </div>`).join('');
  updateDash();
}
function markPaid(id){
  const d = DB.debts.find(x=>x.id===id);
  if(d){ d.status='paid'; dbUpsert('debts', d); }
  addActivity('check-circle',`Deni limelipwa`);renderAll();toast('Deni limelipwa!','success');
}
function sendWhatsApp(){
  const active=DB.debts.filter(d=>d.status!=='paid');
  if(!active.length)return toast('Hakuna madeni!','success');
  const msg=encodeURIComponent(`Habari! Kukumbusha deni lako la TSh ${fmt(active[0].amount)}. Tafadhali lipa haraka. Asante - ${(DB.currentUser||{}).bizname||'Duka'}`);
  window.open(`https://wa.me/?text=${msg}`,'_blank');
}
function sendSMSReminder(){toast('SMS Reminder inahitaji API ya SMS. Wasiliana na msanidi programu.','info');}
function contactWA(name){
  const c=DB.customers.find(x=>x.name===name);
  const debt=DB.debts.filter(d=>d.customer===name&&d.status!=='paid').reduce((s,d)=>s+d.amount,0);
  const msg=encodeURIComponent(`Habari ${name}! Kukumbusha deni lako la TSh ${fmt(debt)}. Tafadhali lipa haraka. Asante - ${(DB.currentUser||{}).bizname||'Duka'}`);
  const phone=(c&&c.phone?c.phone.replace(/^0/,'255'):'');
  window.open(`https://wa.me/${phone}?text=${msg}`,'_blank');
}

// ===== EXPENSES =====
function openExpenseModal(){document.getElementById('ex-desc').value='';document.getElementById('ex-amount').value='';document.getElementById('ex-type').value='Kodi';setDate();openModal('modal-expense');}
function saveExpense(){
  const e={id:Date.now(),desc:document.getElementById('ex-desc').value.trim(),amount:parseInt(document.getElementById('ex-amount').value)||0,type:document.getElementById('ex-type').value,date:document.getElementById('ex-date').value};
  if(!e.desc||!e.amount)return toast('Jaza maelezo na kiasi!','warn');
  DB.expenses.unshift(e);
  dbUpsert('expenses', e);
  addActivity('receipt',`Matumizi: ${e.desc} - TSh ${fmt(e.amount)}`);
  renderAll();closeModal('modal-expense');toast('Matumizi yamehifadhiwa!','success');
}
function deleteExpense(id){if(!confirm('Futa matumizi haya?'))return;DB.expenses=DB.expenses.filter(e=>e.id!==id);dbDelete('expenses',id);renderAll();toast('Imefutwa','success');}
const expIcons={Kodi:'🏠',Mishahara:'👥','Umeme/Maji':'💡',Usafirishaji:'🚛',Matangazo:'📢',Nyingine:'💼'};
function renderExpenses(){
  const list=document.getElementById('expenses-list');
  if(!DB.expenses.length){list.innerHTML='<p style="color:var(--text3);text-align:center">Hakuna matumizi yaliyorekodiwa</p>';return;}
  list.innerHTML=DB.expenses.map(e=>`
    <div class="exp-item">
      <div class="exp-icon bg-red" style="color:var(--danger);font-size:1.3rem">${expIcons[e.type]||'💼'}</div>
      <div class="exp-info"><h4>${e.desc}</h4><p>${e.type} • ${e.date}</p></div>
      <div class="exp-amount">TSh ${fmt(e.amount)}</div>
      <button class="btn btn-icon btn-sm" onclick="deleteExpense(${e.id})"><i class="fas fa-trash"></i></button>
    </div>`).join('');
}

// ===== PURCHASES =====
function openPurchaseModal(){document.getElementById('pur-qty').value='';document.getElementById('pur-price').value='';document.getElementById('pur-supplier').value='';setDate();openModal('modal-purchase');}
function savePurchase(){
  const pid=parseInt(document.getElementById('pur-prod').value);
  const qty=parseInt(document.getElementById('pur-qty').value)||0;
  const price=parseInt(document.getElementById('pur-price').value)||0;
  const sup=document.getElementById('pur-supplier').value.trim();
  const date=document.getElementById('pur-date').value;
  if(!pid||!qty||!price)return toast('Jaza sehemu zote!','warn');
  const prod=DB.products.find(p=>p.id===pid);
  if(!prod)return toast('Bidhaa haipo!','danger');
  const pur={id:Date.now(),product:prod.name,productId:pid,qty,price,total:qty*price,supplier:sup,date};
  DB.purchases.unshift(pur);
  prod.stock+=qty;
  dbUpsert('purchases', pur);
  dbUpsert('products', prod);
  addActivity('truck-loading',`Ununuzi: ${prod.name} × ${qty}`);
  renderAll();closeModal('modal-purchase');toast('Ununuzi umehifadhiwa!','success');
}
function renderPurchases(){
  const tb=document.getElementById('purchases-tbody');
  if(!DB.purchases.length){tb.innerHTML='<tr><td colspan="6" style="text-align:center;color:var(--text3)">Hakuna rekodi</td></tr>';return;}
  tb.innerHTML=DB.purchases.map(p=>`<tr><td>${p.date}</td><td class="td-bold">${p.product}</td><td>${p.qty}</td><td>${fmt(p.price)}</td><td class="td-bold">${fmt(p.total)}</td><td>${p.supplier||'—'}</td></tr>`).join('');
}

// ===== PROFIT =====
function calcProfit(){
  const period=document.getElementById('profit-period').value;
  const now=new Date();
  function inPeriod(date){
    const d=new Date(date);
    if(period==='today'){const t=new Date();return d.toDateString()===t.toDateString();}
    if(period==='week'){const w=new Date(now);w.setDate(w.getDate()-7);return d>=w;}
    if(period==='month'){return d.getMonth()===now.getMonth()&&d.getFullYear()===now.getFullYear();}
    if(period==='year'){return d.getFullYear()===now.getFullYear();}
    return true;
  }
  const rev=DB.sales.filter(s=>inPeriod(s.date)&&s.status==='completed').reduce((s,x)=>s+x.total,0);
  const cogs=DB.sales.filter(s=>inPeriod(s.date)&&s.status==='completed').reduce((s,x)=>{const p=DB.products.find(pr=>pr.name===x.product);return s+(p?p.buyprice*x.qty:0);},0);
  const exp=DB.expenses.filter(e=>inPeriod(e.date)).reduce((s,e)=>s+e.amount,0);
  const gross=rev-cogs;
  const net=gross-exp;
  document.getElementById('p-revenue').textContent='TSh '+fmt(rev);
  document.getElementById('p-cost').textContent='TSh '+fmt(cogs+exp);
  document.getElementById('p-profit').textContent='TSh '+fmt(net);
  document.getElementById('profit-tbody').innerHTML=`
    <tr><td>Mapato ya Mauzo</td><td class="td-bold c-teal">TSh ${fmt(rev)}</td><td>100%</td></tr>
    <tr><td>Gharama za Bidhaa (COGS)</td><td style="color:var(--danger)">TSh ${fmt(cogs)}</td><td>${rev?Math.round(cogs/rev*100):0}%</td></tr>
    <tr><td>Faida ya Mauzo (Gross)</td><td class="td-bold"style="color:var(--warn)">TSh ${fmt(gross)}</td><td>${rev?Math.round(gross/rev*100):0}%</td></tr>
    <tr><td>Matumizi ya Biashara</td><td style="color:var(--danger)">TSh ${fmt(exp)}</td><td>${rev?Math.round(exp/rev*100):0}%</td></tr>
    <tr><td><b>Faida Halisi (Net)</b></td><td class="td-bold" style="color:${net>=0?'var(--success)':'var(--danger)'}">TSh ${fmt(net)}</td><td><b>${rev?Math.round(net/rev*100):0}%</b></td></tr>`;
}

// ===== STATS =====
function updateStats(){
  const now=new Date();
  const monthSales=DB.sales.filter(s=>{ const d=new Date(s.date); return d.getMonth()===now.getMonth()&&d.getFullYear()===now.getFullYear()&&s.status==='completed';}).reduce((s,x)=>s+x.total,0);
  const weekAgo=new Date(now);weekAgo.setDate(weekAgo.getDate()-7);
  const weekSales=DB.sales.filter(s=>new Date(s.date)>=weekAgo&&s.status==='completed').reduce((s,x)=>s+x.total,0);
  document.getElementById('st-month').textContent=fmt(monthSales);
  document.getElementById('st-week').textContent=fmt(weekSales);
  // Top product
  const prodSales={};
  DB.sales.forEach(s=>{if(!prodSales[s.product])prodSales[s.product]=0;prodSales[s.product]+=s.qty;});
  const topP=Object.entries(prodSales).sort((a,b)=>b[1]-a[1])[0];
  document.getElementById('st-top-prod').textContent=topP?topP[0]:'—';
  // Top customer
  const custSales={};
  DB.sales.filter(s=>s.customerId!=='cash').forEach(s=>{if(!custSales[s.customer])custSales[s.customer]=0;custSales[s.customer]+=s.total;});
  const topC=Object.entries(custSales).sort((a,b)=>b[1]-a[1])[0];
  document.getElementById('st-top-cust').textContent=topC?topC[0]:'—';
}

// ===== DASHBOARD =====
function updateDash(){
  const now=new Date();
  const today=now.toISOString().split('T')[0];
  const todaySales=DB.sales.filter(s=>s.date===today&&s.status==='completed').reduce((s,x)=>s+x.total,0);
  const totalDebts=DB.debts.filter(d=>d.status!=='paid').reduce((s,d)=>s+d.amount,0);
  const monthExp=DB.expenses.filter(e=>{const d=new Date(e.date);return d.getMonth()===now.getMonth();}).reduce((s,e)=>s+e.amount,0);
  // Profit this month
  const monthSales=DB.sales.filter(s=>{const d=new Date(s.date);return d.getMonth()===now.getMonth()&&s.status==='completed';}).reduce((s,x)=>s+x.total,0);
  const cogs=DB.sales.filter(s=>{const d=new Date(s.date);return d.getMonth()===now.getMonth()&&s.status==='completed';}).reduce((s,x)=>{const p=DB.products.find(pr=>pr.name===x.product);return s+(p?p.buyprice*x.qty:0);},0);
  document.getElementById('d-today').textContent=fmt(todaySales);
  document.getElementById('d-cust').textContent=DB.customers.length;
  document.getElementById('d-profit').textContent=fmt(monthSales-cogs-monthExp);
  document.getElementById('d-debts').textContent=fmt(totalDebts);
  document.getElementById('d-prods').textContent=DB.products.length;
  document.getElementById('d-expenses').textContent=fmt(monthExp);
  // Badges
  const debtCount=DB.debts.filter(d=>d.status!=='paid').length;
  const nb=document.getElementById('nb-debts');nb.textContent=debtCount;nb.style.display=debtCount?'':'none';
  const lowStock=DB.products.filter(p=>p.stock<=p.minstock).length;
  const ns=document.getElementById('nb-stock');ns.textContent=lowStock;ns.style.display=lowStock?'':'none';
}

// ===== CHARTS =====
let charts={};
function initCharts(){
  const opt=(color,fill)=>({responsive:true,maintainAspectRatio:true,plugins:{legend:{display:false}},scales:{x:{grid:{color:'rgba(255,255,255,.04)'},ticks:{color:'#8a95a3',font:{size:10}}},y:{grid:{color:'rgba(255,255,255,.04)'},ticks:{color:'#8a95a3',font:{size:10}}}},elements:{line:{tension:.4}}});
  // Week chart
  const wc=document.getElementById('weekChart');
  if(wc){
    const ctx=wc.getContext('2d');
    const labels=['Ju','Ju2','Ju3','Al','Ij','Ju4','J5'];
    const now=new Date();
    const vals=Array.from({length:7},(_,i)=>{
      const d=new Date(now);d.setDate(d.getDate()-6+i);
      const ds=d.toISOString().split('T')[0];
      return DB.sales.filter(s=>s.date===ds&&s.status==='completed').reduce((s,x)=>s+x.total,0);
    });
    charts.week=new Chart(ctx,{type:'line',data:{labels,datasets:[{data:vals,borderColor:'#00d4aa',backgroundColor:'rgba(0,212,170,.08)',fill:true,pointBackgroundColor:'#00d4aa',pointRadius:4}]},options:opt()});
  }
  // Top products
  const tpc=document.getElementById('topProdChart');
  if(tpc){
    const ctx=tpc.getContext('2d');
    const prodCount={};DB.sales.forEach(s=>{if(!prodCount[s.product])prodCount[s.product]=0;prodCount[s.product]+=s.qty;});
    const sorted=Object.entries(prodCount).sort((a,b)=>b[1]-a[1]).slice(0,5);
    charts.top=new Chart(ctx,{type:'bar',data:{labels:sorted.map(x=>x[0]),datasets:[{data:sorted.map(x=>x[1]),backgroundColor:['#00d4aa','#0099ff','#ff6b35','#ff4757','#ffa502'],borderRadius:4}]},options:{responsive:true,plugins:{legend:{display:false}},scales:{x:{grid:{display:false},ticks:{color:'#8a95a3',font:{size:10}}},y:{grid:{color:'rgba(255,255,255,.04)'},ticks:{color:'#8a95a3',font:{size:10}}}}}});
  }
  // Month chart
  const mc=document.getElementById('monthChart');
  if(mc){
    const ctx=mc.getContext('2d');
    const months=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
    const now=new Date();
    const vals=Array.from({length:6},(_,i)=>{
      const m=(now.getMonth()-5+i+12)%12;const y=now.getFullYear()-((now.getMonth()-5+i+12<12)?1:0);
      return DB.sales.filter(s=>{const d=new Date(s.date);return d.getMonth()===m&&d.getFullYear()===y&&s.status==='completed';}).reduce((s,x)=>s+x.total,0);
    });
    const mlabels=Array.from({length:6},(_,i)=>months[(now.getMonth()-5+i+12)%12]);
    charts.month=new Chart(ctx,{type:'bar',data:{labels:mlabels,datasets:[{data:vals,backgroundColor:'rgba(0,153,255,.6)',borderColor:'#0099ff',borderWidth:1,borderRadius:4}]},options:opt()});
  }
  // Cat chart
  const cc=document.getElementById('catChart');
  if(cc){
    const ctx=cc.getContext('2d');
    const catSales={};DB.sales.forEach(s=>{const p=DB.products.find(pr=>pr.name===s.product);if(p){if(!catSales[p.category])catSales[p.category]=0;catSales[p.category]+=s.total;}});
    const entries=Object.entries(catSales);
    charts.cat=new Chart(ctx,{type:'doughnut',data:{labels:entries.map(x=>x[0]),datasets:[{data:entries.map(x=>x[1]),backgroundColor:['#00d4aa','#0099ff','#ff6b35','#ff4757','#ffa502','#7bed9f'],borderWidth:0}]},options:{responsive:true,plugins:{legend:{labels:{color:'#8a95a3',font:{size:10}}}}}});
  }
}

// ===== PDF RECEIPT =====
function printPDFReceipt(){
  const pid=parseInt(document.getElementById('s-product').value);
  const qty=parseInt(document.getElementById('s-qty').value)||0;
  const price=parseInt(document.getElementById('s-price').value)||0;
  if(!pid||!qty||!price)return toast('Jaza uuzaji kwanza!','warn');
  const prod=DB.products.find(p=>p.id===pid);
  const custName=document.getElementById('s-customer').options[document.getElementById('s-customer').selectedIndex].text;
  const date=document.getElementById('s-date').value;
  const pay=document.querySelector('input[name="pay"]:checked').value;
  const total=qty*price;
  const {jsPDF}=window.jspdf;
  const doc=new jsPDF({unit:'mm',format:[80,120]});
  doc.setFont('helvetica');
  doc.setFontSize(14);doc.setFont(undefined,'bold');
  doc.text((DB.currentUser||{}).bizname||'MAUZOBOOK',40,12,{align:'center'});
  doc.setFontSize(8);doc.setFont(undefined,'normal');
  doc.text('RISITI YA UUZAJI',40,18,{align:'center'});
  doc.setLineWidth(.3);doc.line(5,21,75,21);
  doc.setFontSize(8);
  doc.text(`Tarehe: ${date}`,7,27);
  doc.text(`Mteja: ${custName}`,7,33);
  doc.text(`Malipo: ${payLabel(pay)}`,7,39);
  doc.line(5,42,75,42);
  doc.setFont(undefined,'bold');doc.text('Bidhaa',7,48);doc.text('Idadi',42,48);doc.text('Jumla',60,48);
  doc.line(5,51,75,51);
  doc.setFont(undefined,'normal');
  doc.text((prod?prod.name:'Bidhaa').substring(0,18),7,57);
  doc.text(String(qty),42,57);
  doc.text(fmt(total),60,57);
  doc.line(5,60,75,60);
  doc.setFont(undefined,'bold');doc.text('JUMLA: TSh '+fmt(total),7,66);
  doc.line(5,69,75,69);
  doc.setFont(undefined,'normal');doc.setFontSize(7);
  doc.text('Asante kwa kununua!',40,76,{align:'center'});
  doc.text((DB.currentUser||{}).phone||'',40,81,{align:'center'});
  doc.save(`risiti-${Date.now()}.pdf`);
  toast('Risiti imetengenezwa!','success');
}

// ===== BARCODE SCANNER =====
function doScan(){
  toast('Scanner: Andika au scan barcode kwenye kisanduku cha bidhaa (barcode field)','info');
  const bc=prompt('Weka barcode nambari:');
  if(!bc)return;
  const prod=DB.products.find(p=>p.barcode===bc.trim());
  if(prod){
    goTo('sales');
    const sel=document.getElementById('s-product');
    [...sel.options].forEach((o,i)=>{if(o.value==prod.id)sel.selectedIndex=i;});
    onProductSelect();
    toast(`Bidhaa imepatikana: ${prod.name}`,'success');
  } else toast('Bidhaa haipo na barcode hiyo!','danger');
}

// ===== EXPORT =====
function doExportExcel(){
  const wb=XLSX.utils.book_new();
  const addSheet=(name,data)=>{if(data.length){const ws=XLSX.utils.json_to_sheet(data);XLSX.utils.book_append_sheet(wb,ws,name);}};
  addSheet('Mauzo',DB.sales);
  addSheet('Wateja',DB.customers);
  addSheet('Bidhaa',DB.products);
  addSheet('Matumizi',DB.expenses);
  addSheet('Manunuzi',DB.purchases);
  addSheet('Madeni',DB.debts);
  XLSX.writeFile(wb,`mauzobook-${new Date().toISOString().split('T')[0]}.xlsx`);
  toast('Excel imetengenezwa!','success');
}
function doExportJSON(){
  const data={sales:DB.sales,customers:DB.customers,products:DB.products,categories:DB.categories,debts:DB.debts,expenses:DB.expenses,purchases:DB.purchases,exported:new Date().toISOString()};
  const blob=new Blob([JSON.stringify(data,null,2)],{type:'application/json'});
  const a=document.createElement('a');a.href=URL.createObjectURL(blob);a.download=`mauzobook-${Date.now()}.json`;a.click();
  toast('JSON imetolewa!','success');
}
function doImportJSON(){
  const inp=document.createElement('input');inp.type='file';inp.accept='.json';
  inp.onchange=e=>{
    const f=e.target.files[0];const r=new FileReader();
    r.onload=ev=>{
      try{
        const d=JSON.parse(ev.target.result);
        if(!confirm('Hii itabadilisha data ya sasa. Endelea?'))return;
        ['sales','customers','products','categories','debts','expenses','purchases'].forEach(k=>{
          if(d[k]){ DB[k]=d[k]; d[k].forEach(obj=>dbUpsert(k,obj)); }
        });
        populateSelects();renderAll();toast('Data imeingizwa!','success');
      }catch{toast('Faili halijakubalika!','danger');}
    };r.readAsText(f);
  };inp.click();
}
function clearAllData(){
  if(!confirm('Hii itafuta DATA YOTE. Una uhakika?'))return;
  if(!confirm('Hii ni hatua ya mwisho. Futa?'))return;
  DB.sales=[];DB.customers=[];DB.products=[];DB.debts=[];DB.expenses=[];DB.purchases=[];DB.activities=[];
  dbClearAll();
  populateSelects();renderAll();toast('Data yote imefutwa','danger');
}

// ===== PROFILE =====
async function saveProfile(){
  const u=DB.currentUser;
  if(!u)return;
  u.name=document.getElementById('pr-name').value.trim();
  u.bizname=document.getElementById('pr-biz').value.trim();
  u.phone=document.getElementById('pr-phone').value.trim();
  u.address=document.getElementById('pr-address').value.trim();
  const np=document.getElementById('pr-pass').value;
  if(np){
    if(np.length<6)return toast('Nenosiri liwe na herufi 6+!','warn');
    const { error: pwerr } = await sb.auth.updateUser({ password: np });
    if(pwerr) return toast('Hitilafu kubadili nenosiri: '+pwerr.message,'danger');
  }
  const { error } = await sb.from('profiles').update({
    name:u.name, bizname:u.bizname, phone:u.phone, address:u.address
  }).eq('id', u.id);
  if(error) return toast('Hitilafu kuhifadhi profaili: '+error.message,'danger');
  document.getElementById('user-name-sidebar').textContent=u.name||u.username;
  document.getElementById('biz-name-sidebar').textContent=u.bizname||'Biashara';
  document.getElementById('profile-name').textContent=u.name||u.username;
  document.getElementById('profile-biz').textContent=u.bizname||'';
  document.getElementById('user-av').textContent=(u.name||u.username).charAt(0).toUpperCase();
  document.getElementById('profile-av').textContent=(u.name||u.username).charAt(0).toUpperCase();
  document.getElementById('pr-pass').value='';
  toast('Akaunti imesasishwa!','success');
}

// ===== NOTIFICATIONS =====
let notifs=[];
function addNotif(type,title,msg){
  notifs.unshift({id:Date.now(),type,title,msg,time:new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})});
  if(notifs.length>20)notifs.pop();
  document.getElementById('notif-dot').style.display='block';
  renderNotifs();
}
function renderNotifs(){
  const icons={info:'fas fa-info-circle',warn:'fas fa-exclamation-triangle',danger:'fas fa-times-circle',success:'fas fa-check-circle'};
  const colors={info:'var(--accent2)',warn:'var(--warn)',danger:'var(--danger)',success:'var(--success)'};
  const list=document.getElementById('notif-list');
  if(!notifs.length){list.innerHTML='<p style="padding:16px;text-align:center;color:var(--text3);font-size:.82rem">Hakuna arifa</p>';return;}
  list.innerHTML=notifs.slice(0,8).map(n=>`
    <div class="notif-item">
      <div class="notif-icon" style="background:rgba(0,0,0,.2);color:${colors[n.type]||colors.info}"><i class="${icons[n.type]||icons.info}"></i></div>
      <div class="notif-text"><strong>${n.title}</strong>${n.msg}<div class="notif-time">${n.time}</div></div>
    </div>`).join('');
}
function toggleNotif(){const p=document.getElementById('notif-panel');p.classList.toggle('open');document.getElementById('notif-dot').style.display='none';}
function clearNotifs(){notifs=[];renderNotifs();document.getElementById('notif-panel').classList.remove('open');}
document.addEventListener('click',e=>{const np=document.getElementById('notif-panel');if(!np.contains(e.target)&&!e.target.closest('.notif-btn'))np.classList.remove('open');});

// ===== ACTIVITIES =====
function addActivity(icon,text){
  const a = {id:Date.now(),icon,text,time:new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})};
  DB.activities.unshift(a);
  if(DB.activities.length>10){ const removed=DB.activities.pop(); dbDelete('activities',removed.id); }
  dbUpsert('activities', a);
  renderActivities();
}
function renderActivities(){
  const list=document.getElementById('activity-list');
  if(!DB.activities.length){list.innerHTML='<p style="color:var(--text3);text-align:center;padding:10px">Bado hakuna shughuli</p>';return;}
  list.innerHTML=DB.activities.map(a=>`
    <div style="display:flex;align-items:center;gap:10px;padding:10px 0;border-bottom:1px solid var(--border)">
      <div style="width:32px;height:32px;border-radius:8px;background:var(--bg3);display:flex;align-items:center;justify-content:center;color:var(--accent);font-size:.85rem"><i class="fas fa-${a.icon}"></i></div>
      <div style="flex:1"><div style="font-size:.85rem">${a.text}</div><div style="font-size:.72rem;color:var(--text3)">${a.time}</div></div>
    </div>`).join('');
}

// ===== AI ASSISTANT =====
function openAI(){document.getElementById('ai-panel').classList.add('open');document.getElementById('ai-fab').style.display='none';}
function closeAI(){document.getElementById('ai-panel').classList.remove('open');document.getElementById('ai-fab').style.display='block';}
function aiAsk(q){document.getElementById('ai-input').value=q;sendAI();}
function sendAI(){
  const inp=document.getElementById('ai-input');
  const msg=inp.value.trim();if(!msg)return;
  const body=document.getElementById('ai-msgs');
  body.innerHTML+=`<div class="ai-msg user">${msg}</div>`;
  inp.value='';body.scrollTop=body.scrollHeight;
  document.getElementById('ai-typing').style.display='block';
  setTimeout(()=>{
    document.getElementById('ai-typing').style.display='none';
    const reply=getSmartAIReply(msg);
    body.innerHTML+=`<div class="ai-msg bot">${reply}</div>`;
    body.scrollTop=body.scrollHeight;
  },800+Math.random()*600);
}
document.addEventListener('DOMContentLoaded',()=>{
  const ai=document.getElementById('ai-input');
  if(ai)ai.addEventListener('keydown',e=>{if(e.key==='Enter')sendAI();});
});
function getSmartAIReply(msg){
  const m=msg.toLowerCase();
  const now=new Date();const today=now.toISOString().split('T')[0];
  const todaySales=DB.sales.filter(s=>s.date===today&&s.status==='completed');
  const todayTotal=todaySales.reduce((s,x)=>s+x.total,0);
  if(m.includes('mauzo')&&(m.includes('leo')||m.includes('today'))){
    return `📈 <b>Mauzo ya Leo (${today}):</b><br>Jumla: <b>TSh ${fmt(todayTotal)}</b><br>Rekodi: ${todaySales.length}`;
  }
  if(m.includes('stock')||m.includes('zinaisha')||m.includes('zimeisha')){
    const low=DB.products.filter(p=>p.stock<=p.minstock);
    if(!low.length)return '✅ Bidhaa zote zina stock ya kutosha!';
    return '⚠️ <b>Bidhaa zenye stock chini:</b><br>'+low.map(p=>`• ${p.name}: <b>${p.stock}</b> ${p.unit}`).join('<br>');
  }
  if(m.includes('deni')||m.includes('madeni')){
    const active=DB.debts.filter(d=>d.status!=='paid');
    const total=active.reduce((s,d)=>s+d.amount,0);
    if(!active.length)return '✅ Hakuna madeni yaliyobaki!';
    return `💰 <b>Madeni Yaliyobaki:</b><br>Jumla: <b>TSh ${fmt(total)}</b><br>Wateja: ${active.length}<br><br>`+active.slice(0,3).map(d=>`• ${d.customer}: TSh ${fmt(d.amount)}`).join('<br>');
  }
  if(m.includes('wateja')){
    return `👥 <b>Wateja:</b><br>Waliohifadhiwa: <b>${DB.customers.length}</b><br>Wanaodaiwa: ${DB.debts.filter(d=>d.status!=='paid').length}`;
  }
  if(m.includes('bidhaa bora')||m.includes('inauza zaidi')){
    const pc={};DB.sales.forEach(s=>{if(!pc[s.product])pc[s.product]=0;pc[s.product]+=s.qty;});
    const sorted=Object.entries(pc).sort((a,b)=>b[1]-a[1]).slice(0,3);
    if(!sorted.length)return '📊 Bado hakuna data ya mauzo';
    return '🏆 <b>Bidhaa Zinazouza Zaidi:</b><br>'+sorted.map((x,i)=>`${i+1}. ${x[0]}: idadi ${x[1]}`).join('<br>');
  }
  if(m.includes('faida')||m.includes('profit')){
    const rev=DB.sales.filter(s=>s.status==='completed').reduce((s,x)=>s+x.total,0);
    const cogs=DB.sales.filter(s=>s.status==='completed').reduce((s,x)=>{const p=DB.products.find(pr=>pr.name===x.product);return s+(p?p.buyprice*x.qty:0);},0);
    const exp=DB.expenses.reduce((s,e)=>s+e.amount,0);
    return `📊 <b>Faida Yote:</b><br>Mapato: TSh ${fmt(rev)}<br>Gharama bidhaa: TSh ${fmt(cogs)}<br>Matumizi: TSh ${fmt(exp)}<br><b>Faida halisi: TSh ${fmt(rev-cogs-exp)}</b>`;
  }
  if(m.includes('ushauri')||m.includes('advice')||m.includes('nasaha')){
    const low=DB.products.filter(p=>p.stock<=p.minstock);
    const debts=DB.debts.filter(d=>d.status!=='paid');
    let advice='🧠 <b>Ushauri wa Biashara:</b><br>';
    if(low.length)advice+=`⚠️ Nunua stok ya: ${low.map(p=>p.name).join(', ')}<br>`;
    if(debts.length)advice+=`💰 Kukusanya madeni ${debts.length} kutoka kwa wateja<br>`;
    const todayTot=DB.sales.filter(s=>s.date===today).reduce((s,x)=>s+x.total,0);
    if(todayTot===0)advice+='📢 Fanya matangazo — hakuna uuzaji leo bado<br>';
    advice+='📈 Angalia bidhaa zinazouza zaidi na ongeza stok yao';
    return advice;
  }
  if(m.includes('jumla')||m.includes('summary')){
    const rev=DB.sales.filter(s=>s.status==='completed').reduce((s,x)=>s+x.total,0);
    return `📋 <b>Muhtasari wa Biashara:</b><br>💰 Mapato yote: TSh ${fmt(rev)}<br>👥 Wateja: ${DB.customers.length}<br>📦 Bidhaa: ${DB.products.length}<br>🧾 Madeni: TSh ${fmt(DB.debts.filter(d=>d.status!=='paid').reduce((s,d)=>s+d.amount,0))}`;
  }
  return `🤖 Samahani sijaelewa vizuri. Unaweza kuuliza:<br>• Mauzo ya leo<br>• Stock bidhaa<br>• Madeni<br>• Ushauri wa biashara<br>• Faida<br>• Bidhaa bora`;
}

// ===== MODALS =====
function openModal(id){document.getElementById(id).classList.add('open');}
function closeModal(id){document.getElementById(id).classList.remove('open');}
document.querySelectorAll('.modal-overlay').forEach(m=>{m.addEventListener('click',e=>{if(e.target===m)m.classList.remove('open');});});

// ===== TOAST =====
let toastTimer;
function toast(msg,type='success'){
  const t=document.getElementById('toast');const m=document.getElementById('toast-msg');
  t.className=`toast show ${type}`;
  const icons={success:'fas fa-check-circle',danger:'fas fa-times-circle',warn:'fas fa-exclamation-triangle',info:'fas fa-info-circle'};
  t.querySelector('i').className=icons[type]||icons.info;
  m.textContent=msg;
  clearTimeout(toastTimer);
  toastTimer=setTimeout(()=>t.classList.remove('show'),4000);
}

// ===== HELPERS =====
function fmt(n){return Number(n||0).toLocaleString();}

// ===== SESSION RESTORE =====
// Ikiwa mtumiaji tayari ana session halali (aliingia hapo awali), mrudishe moja kwa moja kwenye app
(async function restoreSession(){
  const { data: { session } } = await sb.auth.getSession();
  if (session && session.user) {
    const { data: profile } = await sb.from('profiles').select('*').eq('id', session.user.id).maybeSingle();
    if (profile) await enterApp(profile);
  }
})();
</script>
</body>
</html>