<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Celtics Draft Tracker</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;-webkit-user-select:none;user-select:none}
body{font-family:system-ui;background:#0c2340;height:100vh;overflow:hidden}
.app{display:flex;flex-direction:column;height:100vh;background:#f0f2f5}
.header{background:linear-gradient(135deg,#0c2340 0%,#1a3a52 100%);color:white;padding:12px 16px;box-shadow:0 2px 8px rgba(0,0,0,.3)}
.header h1{font-size:1.3em;margin-bottom:8px}
.stats{display:flex;gap:20px;background:rgba(255,255,255,.1);padding:8px 12px;border-radius:6px;text-align:center}
.stat{flex:1}
.stat-val{font-size:1.2em;font-weight:700}
.stat-label{font-size:.75em;opacity:.9}
.tabs{display:flex;background:white;border-bottom:1px solid #ddd}
.tab{flex:1;padding:12px;border:none;background:white;font-weight:600;border-bottom:3px solid transparent;cursor:pointer;color:#666;font-size:.95em}
.tab.on{color:#0c2340;border-bottom-color:#0c2340;background:#f5f5f5}
.list{flex:1;overflow-y:auto;padding:8px;-webkit-overflow-scrolling:touch}
.game{background:white;padding:12px;margin-bottom:8px;border-radius:8px;border-left:4px solid #FFD700;display:flex;gap:10px;cursor:pointer;align-items:center;transition:all .2s}
.game:active{transform:scale(.98)}
.game.picked{background:#e8e8e8;opacity:.6}
.game.picked .team{text-decoration:line-through;color:#999}
.game.or{border-left-color:#FF8C00}
.game.re{border-left-color:#FF6B6B}
.game.pu{border-left-color:#9B59B6}
.check{width:22px;height:22px;border:2px solid #ccc;border-radius:50%;display:flex;align-items:center;justify-content:center;flex-shrink:0;font-weight:700;color:#0c2340;background:white}
.game.picked .check{background:#0c2340;color:white;border:2px solid #0c2340}
.info{flex:1;min-width:0}
.rank{font-size:.8em;color:#999}
.team{font-weight:700;font-size:1em;color:#333}
.date{font-size:.8em;color:#666;margin-top:2px}
.val{text-align:right;font-weight:700;color:#0c2340;min-width:40px;font-size:.95em}
.footer{background:white;padding:10px;display:flex;gap:8px;border-top:1px solid #ddd}
.btn{flex:1;padding:10px;border:none;border-radius:6px;font-weight:600;cursor:pointer;font-size:.9em;text-transform:uppercase}
.btn-main{background:#0c2340;color:white}
.btn-main:active{background:#051829}
.btn-secondary{background:#e8e8e8;color:#333}
.btn-secondary:active{background:#d0d0d0}
.empty{text-align:center;padding:40px;color:#999}
.empty-emoji{font-size:3em;margin-bottom:10px}
.empty h3{font-size:1.1em;margin-bottom:4px;color:#666}
</style>
</head>
<body>
<div class="app">
<div class="header">
<h1>🏀 DRAFT TRACKER</h1>
<div class="stats">
<div class="stat"><div class="stat-val" id="p">0</div><div class="stat-label">Picked</div></div>
<div class="stat"><div class="stat-val" id="r">40</div><div class="stat-label">Remain</div></div>
<div class="stat"><div class="stat-val" id="pct">0%</div><div class="stat-label">Done</div></div>
</div>
</div>
<div class="tabs">
<button class="tab on" onclick="show('all')">Remaining</button>
<button class="tab" onclick="show('done')">Picked</button>
</div>
<div class="list" id="list"></div>
<div class="footer">
<button class="btn btn-secondary" onclick="reset()">↻ Reset</button>
<button class="btn btn-main" onclick="stats()">📊 Stats</button>
</div>
</div>

<script>
const games=[
{r:1,o:'PHI',d:'Jan 21',dy:'Thu',v:9.56,t:'1'},
{r:2,o:'PHI',d:'Mar 7',dy:'Sun',v:9.46,t:'1'},
{r:3,o:'OKC',d:'Mar 12',dy:'Fri',v:17.06,t:'1'},
{r:4,o:'NYK',d:'Oct 23',dy:'Fri',v:9.46,t:'1'},
{r:5,o:'SAS',d:'Mar 24',dy:'Wed',v:15.84,t:'1'},
{r:6,o:'MIA',d:'Dec 25',dy:'Fri',v:10.15,t:'2'},
{r:7,o:'MIN',d:'Feb 12',dy:'Fri',v:15.14,t:'1'},
{r:8,o:'HOU',d:'Jan 11',dy:'Mon',v:14.80,t:'1'},
{r:9,o:'GSW',d:'Feb 10',dy:'Wed',v:13.60,t:'1'},
{r:10,o:'DAL',d:'Nov 15',dy:'Sun',v:12.02,t:'1'},
{r:11,o:'LAL',d:'Feb 2',dy:'Tue',v:14.92,t:'1'},
{r:12,o:'DEN',d:'Feb 9',dy:'Tue',v:15.88,t:'1'},
{r:13,o:'DET',d:'Dec 20',dy:'Sun',v:8.18,t:'1'},
{r:14,o:'NYK',d:'Jan 13',dy:'Wed',v:8.56,t:'1'},
{r:15,o:'PHX',d:'Jan 29',dy:'Fri',v:13.65,t:'2'},
{r:16,o:'WAS',d:'Dec 14',dy:'Mon',v:7.68,t:'1'},
{r:17,o:'WAS',d:'Dec 16',dy:'Wed',v:7.28,t:'1'},
{r:18,o:'UTA',d:'Dec 18',dy:'Fri',v:12.55,t:'2'},
{r:19,o:'CLE',d:'Feb 26',dy:'Fri',v:10.30,t:'2'},
{r:20,o:'CHI',d:'Oct 30',dy:'Fri',v:13.88,t:'3'},
{r:21,o:'ATL',d:'Nov 27',dy:'Fri',v:13.88,t:'3'},
{r:22,o:'MIA',d:'Apr 2',dy:'Fri',v:10.15,t:'2'},
{r:23,o:'TOR',d:'Feb 14',dy:'Sun',v:10.88,t:'4'},
{r:24,o:'NOP',d:'Mar 21',dy:'Sun',v:13.22,t:'3'},
{r:25,o:'CHA',d:'Jan 23',dy:'Sat',v:12.00,t:'4'},
{r:26,o:'ORL',d:'Mar 18',dy:'Thu',v:9.10,t:'2'},
{r:27,o:'CLE',d:'Dec 28',dy:'Mon',v:9.05,t:'1'},
{r:28,o:'BKN',d:'Jan 16',dy:'Sat',v:12.00,t:'4'},
{r:29,o:'TOR',d:'Jan 25',dy:'Mon',v:7.92,t:'1'},
{r:30,o:'MEM',d:'Mar 11',dy:'Thu',v:11.00,t:'4'},
{r:31,o:'CHI',d:'Oct 26',dy:'Mon',v:11.38,t:'4'},
{r:32,o:'POR',d:'Dec 23',dy:'Wed',v:11.30,t:'2'},
{r:33,o:'IND',d:'Jan 20',dy:'Wed',v:7.65,t:'2'},
{r:34,o:'ORL',d:'Nov 16',dy:'Mon',v:8.60,t:'2'},
{r:35,o:'MIL',d:'Apr 11',dy:'Sun',v:10.50,t:'4'},
{r:36,o:'LAC',d:'Jan 27',dy:'Wed',v:10.24,t:'3'},
{r:37,o:'SAC',d:'Mar 16',dy:'Tue',v:9.00,t:'4'},
{r:38,o:'CHA',d:'Dec 2',dy:'Wed',v:8.00,t:'4'},
{r:39,o:'MIL',d:'Nov 4',dy:'Wed',v:8.00,t:'4'},
{r:40,o:'BKN',d:'Oct 27',dy:'Tue',v:9.00,t:'4'}
];

let picked=[], mode='all';

function getTierClass(t){
  if(t==='1') return 'ye';
  if(t==='2') return 'or';
  if(t==='3') return 're';
  return 'pu';
}

function render(){
  let list='';
  games.forEach(g=>{
    let p=picked.includes(g.r);
    if(mode==='all' && p) return;
    if(mode==='done' && !p) return;
    let tierClass=getTierClass(g.t);
    list+=`<div class="game ${tierClass} ${p?'picked':''}" onclick="click(${g.r})"><div class="check">${p?'✓':''}</div><div class="info"><div class="rank">#${g.r}</div><div class="team">${g.o}</div><div class="date">${g.d} ${g.dy}</div></div><div class="val">${g.v}</div></div>`;
  });
  if(!list) list='<div class="empty"><div class="empty-emoji">'+(mode==='done'?'🔍':'🎉')+'</div><h3>'+(mode==='done'?'No picks yet':'All picked!')+'</h3></div>';
  document.getElementById('list').innerHTML=list;
  document.getElementById('p').textContent=picked.length;
  document.getElementById('r').textContent=40-picked.length;
  document.getElementById('pct').textContent=Math.round(picked.length/40*100)+'%';
}

function click(r){
  if(picked.includes(r)) picked=picked.filter(x=>x!==r);
  else picked.push(r);
  render();
}

function show(m){
  mode=m;
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('on'));
  event.target.classList.add('on');
  render();
}

function reset(){
  if(picked.length>0 && confirm('Reset all picks?')){
    picked=[];
    render();
  }
}

function stats(){
  let total=games.filter(g=>picked.includes(g.r)).reduce((a,g)=>a+g.v,0);
  let avg=picked.length>0?total/picked.length:0;
  alert('📊 DRAFT STATS\n\n✅ Picked: '+picked.length+'/40\n📊 Total Value: '+total.toFixed(2)+'\n📈 Avg Value: '+avg.toFixed(2));
}

render();
</script>
</body>
</html>
