import { useState, useEffect, useRef } from "react";

var ID=function(){return Date.now().toString(36)+Math.random().toString(36).slice(2,6);};
function useSaved(k,ini){var [v,setV]=useState(ini);useEffect(function(){if(typeof window==="undefined"||!window.storage)return;window.storage.get(k).then(function(r){if(r&&r.value){try{setV(JSON.parse(r.value));}catch(e){}}}).catch(function(){});},[]);function u(n){setV(n);if(typeof window!=="undefined"&&window.storage)window.storage.set(k,JSON.stringify(n)).catch(function(){});}return[v,u];}

var S={bg:"#0a0a0f",c1:"#12121a",c2:"#1a1a25",bd:"#2a2a3a",ac:"#00d4ff",a2:"#ff6b35",ok:"#00e676",wn:"#ffab00",tx:"#e8e8f0",t2:"#8888a0",t3:"#55556a",pp:"#e040fb",red:"#ff5252"};
var grad="linear-gradient(135deg,#00d4ff,#7c3aed)";
function fmtSec(s){var m=Math.floor(s/60);var ss=s%60;return m+":"+String(ss).padStart(2,"0");}
function fmtMin(s){return Math.round(s/60)+"分";}
function todayStr(){var d=new Date();return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");}
function dateLbl(ds){if(!ds)return"";var p=ds.split("-");return Number(p[1])+"/"+Number(p[2]);}
function daysAgo(n){var d=new Date();d.setDate(d.getDate()-n);return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");}
function daysFromNow(n){var d=new Date();d.setDate(d.getDate()+n);return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");}
function daysBetween(from,to){if(!from||!to)return 0;var d1=new Date(from);var d2=new Date(to);d1.setHours(0,0,0,0);d2.setHours(0,0,0,0);return Math.round((d2-d1)/86400000);}
function fmtDateFull(ds){if(!ds)return"";var p=ds.split("-");var d=new Date(Number(p[0]),Number(p[1])-1,Number(p[2]));var w=["日","月","火","水","木","金","土"][d.getDay()];return Number(p[1])+"月"+Number(p[2])+"日("+w+")";}
// Time HH:MM <-> seconds
function timeToSec(t){if(!t)return 0;var p=t.split(":");return Number(p[0])*3600+Number(p[1])*60;}
function secToTime(s){s=Math.max(0,s);var h=Math.floor(s/3600);var m=Math.floor((s%3600)/60);return String(h).padStart(2,"0")+":"+String(m).padStart(2,"0");}
// Compute schedule: returns array of {start, end} for each item
function computeSchedule(items,startTime){
  var cur=timeToSec(startTime||"09:00");
  return items.map(function(it){var s=cur;cur+=itemTotal(it);return{start:secToTime(s),end:secToTime(cur)};});
}
function totalEndTime(items,startTime){
  var cur=timeToSec(startTime||"09:00");
  items.forEach(function(it){cur+=itemTotal(it);});
  return secToTime(cur);
}
var iS={padding:"10px 12px",background:S.c2,border:"1px solid "+S.bd,borderRadius:10,color:S.tx,fontSize:14,outline:"none",width:"100%",fontFamily:"sans-serif"};
var taS={padding:"12px 14px",background:S.c2,border:"1px solid "+S.bd,borderRadius:10,color:S.tx,fontSize:14,outline:"none",width:"100%",fontFamily:"sans-serif",lineHeight:1.6};
var btnP={padding:14,background:grad,border:"none",borderRadius:12,color:"#fff",fontSize:15,fontWeight:600,cursor:"pointer",width:"100%",fontFamily:"sans-serif"};

/* Categories */
var CATS=[
  {id:"pass",name:"パス",cl:"#00d4ff"},
  {id:"gb",name:"グラボー",cl:"#ff6b35"},
  {id:"clr",name:"クリライ",cl:"#00bcd4"},
  {id:"game",name:"試合",cl:"#ff9800"},
  {id:"pos",name:"ポジ別",cl:"#7c4dff"},
  {id:"shot",name:"シュート",cl:"#ff4081"},
  {id:"6v6",name:"6on6",cl:"#00e676"},
  {id:"1v1",name:"対人",cl:"#ffab00"},
  {id:"wc",name:"W-up/C-down",cl:"#009688"},
  {id:"mtg",name:"MTG",cl:"#2196f3"},
  {id:"off",name:"OFF",cl:"#f44336"},
  {id:"other",name:"その他",cl:"#78909c"},
];
function catInfo(id){return CATS.find(function(c){return c.id===id;})||CATS[CATS.length-1];}
// Determine calendar dot color by menu content priority: OFF > game > MTG > practice
function menuDotColor(m){
  if(!m||!m.items||m.items.length===0)return"#00e676";
  var cats=m.items.map(function(it){return it.cat||"other";});
  if(cats.indexOf("off")>=0)return"#f44336";
  if(cats.indexOf("game")>=0)return"#ff9800";
  // All items are MTG
  if(cats.every(function(c){return c==="mtg";}))return"#2196f3";
  return"#00e676";
}

/* Time calc helpers */
function itemWork(it){return(it.duration||0)*(it.sets||1);}
function itemRest(it){return((it.rest||0)*Math.max((it.sets||1)-1,0))+(it.transition||0);}
function itemTotal(it){return itemWork(it)+itemRest(it);}

/* ═══ SAMPLE DATA ═══ */
var sampleMenus=[
  {id:"m1",date:todayStr(),title:dateLbl(todayStr())+"の練習",startTime:"09:00",createdAt:"2026-03-28T07:00:00",items:[
    {id:"i1",name:"ジョグ＆ダイナミックストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:"股関節とハムストリングを重点的に"},
    {id:"i2",name:"パスキャッチ（2人組）",cat:"pass",duration:300,sets:3,rest:30,transition:60,memo:"利き手・逆手交互。キャッチ後の持ち替え速く"},
    {id:"i1",name:"ジョグ＆ダイナミックストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:"股関節とハムストリングを重点的に"},
    {id:"i2",name:"パスキャッチ（2人組）",cat:"pass",duration:300,sets:3,rest:60,transition:60,memo:"利き手・逆手交互。キャッチ後の持ち替え速く"},
    {id:"i3",name:"グラウンドボール",cat:"gb",duration:180,sets:4,rest:60,transition:60,memo:"低い姿勢でボックスアウト意識"},
    {id:"i4",name:"1on1（AT vs DF）",cat:"1v1",duration:240,sets:5,rest:60,transition:120,memo:"ATはフェイント2種以上。DFはフットワーク"},
    {id:"i5",name:"3on3 ハーフコート",cat:"pos",duration:360,sets:4,rest:60,transition:120,memo:"オフボールの動き重視。ピック確認"},
    {id:"i6",name:"6on6 フルフィールド",cat:"6v6",duration:480,sets:3,rest:120,transition:120,memo:"ゾーンDF攻略パターン実践。声掛け徹底"},
    {id:"i7",name:"シュート練習",cat:"shot",duration:300,sets:2,rest:60,transition:60,memo:"ランシュート・スタンディング交互"},
    {id:"i8",name:"クリア＆ライド確認",cat:"clr",duration:240,sets:2,rest:60,transition:60,memo:"クリアの展開パターン3つ確認"},
    {id:"i9",name:"クールダウン＆ストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:0,memo:"呼吸を整えながらゆっくり"},
  ]},
  {id:"m2",date:daysAgo(1),title:"フィジカルデー",startTime:"17:30",createdAt:"2026-03-27T07:00:00",items:[
    {id:"i20",name:"ウォームアップジョグ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:"心拍数を上げる"},
    {id:"i21",name:"ラダードリル",cat:"other",duration:120,sets:4,rest:60,transition:60,memo:"クイック→サイド→クロスオーバー"},
    {id:"i22",name:"スプリント",cat:"other",duration:60,sets:10,rest:60,transition:120,memo:"30m全力。フォーム崩さない"},
    {id:"i23",name:"シャトルラン",cat:"other",duration:60,sets:5,rest:60,transition:60,memo:"20m×5往復。切り返し速く"},
    {id:"i24",name:"体幹トレーニング",cat:"other",duration:60,sets:6,rest:60,transition:60,memo:"プランク→サイドプランク→バードドッグ"},
    {id:"i25",name:"クールダウン",cat:"wc",duration:600,sets:1,rest:0,transition:0,memo:""},
  ]},
  {id:"m3",date:daysAgo(3),title:"試合前日 軽め調整",startTime:"15:00",createdAt:"2026-03-25T07:00:00",items:[
    {id:"i30",name:"軽いジョグ＆ストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:"強度50%"},
    {id:"i31",name:"パスキャッチ（軽め）",cat:"pass",duration:300,sets:2,rest:60,transition:60,memo:"感覚の確認"},
    {id:"i32",name:"セットプレー確認",cat:"game",duration:300,sets:3,rest:60,transition:60,memo:"明日使うパターン3つ確認"},
    {id:"i33",name:"シュート確認",cat:"shot",duration:180,sets:2,rest:60,transition:60,memo:"枠確認程度"},
    {id:"i34",name:"クリア＆ライド確認",cat:"clr",duration:240,sets:2,rest:60,transition:60,memo:"声掛け・パスコース共有"},
    {id:"i35",name:"ミーティング",cat:"other",duration:900,sets:1,rest:0,transition:0,memo:"戦術確認。相手チーム特徴共有"},
  ]},
];
var sampleTpl=[
  {id:"t1",name:"平日 基本メニュー",items:[
    {id:"ti1",name:"ジョグ＆ストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:""},
    {id:"ti2",name:"パスキャッチ",cat:"pass",duration:300,sets:3,rest:60,transition:60,memo:"利き手・逆手交互"},
    {id:"ti3",name:"グラウンドボール",cat:"gb",duration:180,sets:4,rest:60,transition:60,memo:""},
    {id:"ti4",name:"1on1",cat:"1v1",duration:240,sets:5,rest:60,transition:120,memo:""},
    {id:"ti5",name:"3on3",cat:"pos",duration:360,sets:4,rest:60,transition:120,memo:""},
    {id:"ti6",name:"6on6",cat:"6v6",duration:480,sets:3,rest:120,transition:120,memo:""},
    {id:"ti7",name:"シュート練習",cat:"shot",duration:300,sets:2,rest:60,transition:60,memo:""},
    {id:"ti8",name:"クールダウン",cat:"wc",duration:600,sets:1,rest:0,transition:0,memo:""},
  ]},
  {id:"t2",name:"フィジカルデー",items:[
    {id:"ti10",name:"ウォームアップ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:""},
    {id:"ti11",name:"ラダードリル",cat:"other",duration:120,sets:4,rest:60,transition:60,memo:""},
    {id:"ti12",name:"スプリント",cat:"other",duration:60,sets:10,rest:60,transition:120,memo:"30m"},
    {id:"ti13",name:"シャトルラン",cat:"other",duration:60,sets:5,rest:60,transition:60,memo:""},
    {id:"ti14",name:"体幹トレーニング",cat:"other",duration:60,sets:6,rest:60,transition:60,memo:""},
    {id:"ti15",name:"クールダウン",cat:"wc",duration:600,sets:1,rest:0,transition:0,memo:""},
  ]},
  {id:"t3",name:"試合前日 調整",items:[
    {id:"ti20",name:"軽いジョグ＆ストレッチ",cat:"wc",duration:600,sets:1,rest:0,transition:60,memo:"強度50%"},
    {id:"ti21",name:"パスキャッチ（軽め）",cat:"pass",duration:300,sets:2,rest:60,transition:60,memo:""},
    {id:"ti22",name:"セットプレー確認",cat:"game",duration:300,sets:3,rest:60,transition:60,memo:""},
    {id:"ti23",name:"シュート確認",cat:"shot",duration:180,sets:2,rest:60,transition:60,memo:""},
    {id:"ti24",name:"クリア＆ライド確認",cat:"clr",duration:240,sets:2,rest:60,transition:60,memo:""},
    {id:"ti25",name:"ミーティング",cat:"other",duration:900,sets:1,rest:0,transition:0,memo:""},
  ]},
];

var sampleGames=[
  {id:"gm1",date:daysFromNow(14),title:"開幕戦",type:"opening",memo:"vs A大学"},
  {id:"gm2",date:daysFromNow(60),title:"最終戦",type:"final",memo:"vs B大学"},
];

/* ═══ MAIN ═══ */
export default function App(){
  var [menus,setMenus]=useSaved("lpm-menus",sampleMenus);
  var [templates,setTemplates]=useSaved("lpm-tpl",sampleTpl);
  var [games,setGames]=useSaved("lpm-games",sampleGames);
  var [tab,setTab]=useState("today");
  var [sub,setSub]=useState(null);
  var [editMenu,setEditMenu]=useState(null);
  var [timerMenu,setTimerMenu]=useState(null);

  var today=todayStr();
  var todayMenu=menus.find(function(m){return m.date===today;})||null;

  function saveMenu(m){var ex=menus.find(function(x){return x.id===m.id;});if(ex)setMenus(menus.map(function(x){return x.id===m.id?m:x;}));else setMenus(menus.concat([m]));setEditMenu(null);setSub(null);}
  function deleteMenu(id){setMenus(menus.filter(function(x){return x.id!==id;}));}
  function saveTpl(t){var ex=templates.find(function(x){return x.id===t.id;});if(ex)setTemplates(templates.map(function(x){return x.id===t.id?t:x;}));else setTemplates(templates.concat([t]));}
  function deleteTpl(id){setTemplates(templates.filter(function(x){return x.id!==id;}));}

  if(timerMenu){return <TimerView menu={timerMenu} onExit={function(){setTimerMenu(null);}}/>;}

  return(<div style={{maxWidth:480,margin:"0 auto",minHeight:"100vh",background:S.bg,color:S.tx,fontFamily:"sans-serif",display:"flex",flexDirection:"column"}}>
    <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",padding:"14px 16px",borderBottom:"1px solid "+S.bd}}>
      <span style={{fontWeight:800,fontSize:20,background:grad,WebkitBackgroundClip:"text",WebkitTextFillColor:"transparent"}}>LAX Practice</span></div>
    <div style={{flex:1,padding:16,paddingBottom:72,overflowY:"auto"}}>
      {!sub&&tab==="today"&&<TodayView menu={todayMenu} games={games} menus={menus} onNew={function(){var m={id:ID(),date:today,title:dateLbl(today)+"の練習",startTime:"09:00",items:[],createdAt:new Date().toISOString()};setEditMenu(m);setSub("edit");}} onEdit={function(m){setEditMenu(m);setSub("edit");}} onTimer={function(m){setTimerMenu(m);}} onDelete={deleteMenu} templates={templates} onFromTpl={function(t){var m={id:ID(),date:today,title:dateLbl(today)+"の練習",startTime:"09:00",items:t.items.map(function(it){return Object.assign({},it,{id:ID()});}),createdAt:new Date().toISOString()};setEditMenu(m);setSub("edit");}}/>}
      {!sub&&tab==="calendar"&&<CalView menus={menus} games={games} setGames={setGames} onSelect={function(m){setEditMenu(m);setSub("edit");}} onNew={function(date){var m={id:ID(),date:date,title:dateLbl(date)+"の練習",startTime:"09:00",items:[],createdAt:new Date().toISOString()};setEditMenu(m);setSub("edit");}} onTimer={function(m){setTimerMenu(m);}} onDelete={deleteMenu}/>}
      {!sub&&tab==="analysis"&&<AnalysisView menus={menus}/>}
      {sub==="edit"&&editMenu&&<MenuEditor menu={editMenu} onSave={editMenu._isTpl?function(updated){saveTpl({id:updated.id,name:(updated._tplName||"無題").trim(),items:updated.items});setEditMenu(null);setSub(null);}:saveMenu} onCancel={function(){setEditMenu(null);setSub(null);}} onSaveTpl={saveTpl} templates={templates} onDeleteTpl={deleteTpl} isTplMode={editMenu._isTpl} onEditTpl={function(t){setEditMenu({id:t.id,_isTpl:true,_tplName:t.name,items:JSON.parse(JSON.stringify(t.items))});}}/>}
    </div>
    <div style={{position:"fixed",bottom:0,left:"50%",transform:"translateX(-50%)",width:"100%",maxWidth:480,display:"flex",justifyContent:"space-around",padding:"8px 0 12px",background:"rgba(10,10,15,0.95)",borderTop:"1px solid "+S.bd,zIndex:100}}>
      {[{id:"today",lb:"今日"},{id:"calendar",lb:"カレンダー"},{id:"analysis",lb:"練習分析"}].map(function(n){return(<button key={n.id} onClick={function(){setTab(n.id);setSub(null);setEditMenu(null);}} style={{display:"flex",flexDirection:"column",alignItems:"center",background:"none",border:"none",color:tab===n.id&&!sub?S.ac:S.t3,fontFamily:"sans-serif",cursor:"pointer",padding:"2px 8px",fontSize:11,fontWeight:500,position:"relative"}}>
        {tab===n.id&&!sub&&<div style={{position:"absolute",top:-4,width:16,height:2,background:S.ac,borderRadius:1}}/>}<span>{n.lb}</span></button>);})}
    </div>
  </div>);
}

/* ═══ TIME SUMMARY ═══ */
function TimeSummary(p){
  var items=p.items||[];
  var totalWork=items.reduce(function(s,it){return s+itemWork(it);},0);
  var totalRest=items.reduce(function(s,it){return s+itemRest(it);},0);
  var totalAll=totalWork+totalRest;
  // Group by category
  var catMap={};items.forEach(function(it){var c=it.cat||"other";if(!catMap[c])catMap[c]={work:0,rest:0};catMap[c].work+=itemWork(it);catMap[c].rest+=itemRest(it);});
  var catKeys=CATS.map(function(c){return c.id;}).filter(function(id){return catMap[id];});
  if(totalAll===0)return null;

  return(<div style={{marginBottom:14}}>
    {/* Stack bar */}
    <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}><span style={{fontSize:11,fontWeight:600,color:S.t2}}>時間配分</span><span style={{fontSize:11,color:S.t2}}>合計 {fmtMin(totalAll)}</span></div>
    <div style={{display:"flex",borderRadius:8,overflow:"hidden",height:22}}>
      {items.map(function(it,i){var dur=itemTotal(it);var pct=(dur/totalAll)*100;if(pct<0.5)return null;var ci=catInfo(it.cat||"other");
        return(<div key={it.id||i} title={it.name+" "+fmtMin(dur)} style={{width:pct+"%",background:ci.cl,display:"flex",alignItems:"center",justifyContent:"center",fontSize:7,fontWeight:600,color:"#000",overflow:"hidden",whiteSpace:"nowrap",padding:"0 1px"}}>{pct>8?it.name.slice(0,4):""}</div>);})}
    </div>

    {/* Category breakdown */}
    <div style={{marginTop:10,background:S.c1,border:"1px solid "+S.bd,borderRadius:10,padding:12}}>
      <div style={{fontSize:11,fontWeight:600,color:S.t2,marginBottom:8}}>カテゴリ別</div>
      {catKeys.map(function(cid){var ci=catInfo(cid);var d=catMap[cid];return(<div key={cid} style={{display:"flex",alignItems:"center",justifyContent:"space-between",padding:"4px 0",borderBottom:"1px solid "+S.bd}}>
        <div style={{display:"flex",alignItems:"center",gap:6}}><div style={{width:8,height:8,borderRadius:2,background:ci.cl}}/><span style={{fontSize:12,color:S.tx}}>{ci.name}</span></div>
        <span style={{fontSize:12,color:S.t2,fontFamily:"monospace"}}>{fmtMin(d.work)}</span>
      </div>);})}
      {/* Totals */}
      <div style={{marginTop:8,paddingTop:8,borderTop:"2px solid "+S.bd}}>
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}><span style={{fontSize:12,fontWeight:600,color:S.ok}}>実働時間</span><span style={{fontSize:12,fontWeight:600,color:S.ok,fontFamily:"monospace"}}>{fmtMin(totalWork)}</span></div>
        <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}><span style={{fontSize:12,fontWeight:600,color:S.wn}}>休憩時間合計</span><span style={{fontSize:12,fontWeight:600,color:S.wn,fontFamily:"monospace"}}>{fmtMin(totalRest)}</span></div>
        <div style={{display:"flex",justifyContent:"space-between"}}><span style={{fontSize:13,fontWeight:700,color:S.tx}}>総練習時間</span><span style={{fontSize:13,fontWeight:700,color:S.ac,fontFamily:"monospace"}}>{fmtMin(totalAll)}</span></div>
      </div>
    </div>
  </div>);
}

/* ═══ MENU CARD ═══ */
function MenuCard(p){
  var m=p.menu;var totalWork=m.items.reduce(function(s,it){return s+itemWork(it);},0);var totalRest=m.items.reduce(function(s,it){return s+itemRest(it);},0);
  var startTime=m.startTime||"09:00";var endTime=totalEndTime(m.items,startTime);
  var schedule=computeSchedule(m.items,startTime);
  return(<div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:14,padding:16,marginBottom:12}}>
    <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:10}}>
      <div style={{fontSize:16,fontWeight:700}}>{m.title||"練習メニュー"}</div>
      {p.onTimer&&<button onClick={function(){p.onTimer(m);}} style={{padding:"6px 12px",background:"linear-gradient(135deg,#00e676,#00b0ff)",border:"none",borderRadius:8,color:"#000",fontSize:11,fontWeight:700,cursor:"pointer",fontFamily:"sans-serif"}}>▶ 開始</button>}
    </div>
    {m.items.length>0&&(<div style={{display:"flex",alignItems:"center",gap:8,marginBottom:10,padding:"8px 10px",background:S.c2,borderRadius:8}}>
      <span style={{fontSize:13,fontWeight:700,color:S.ac,fontFamily:"monospace"}}>{startTime}</span>
      <span style={{fontSize:11,color:S.t3}}>→</span>
      <span style={{fontSize:13,fontWeight:700,color:S.pp,fontFamily:"monospace"}}>{endTime}</span>
      <span style={{fontSize:11,color:S.t2,marginLeft:"auto"}}>{fmtMin(totalWork+totalRest)}</span>
    </div>)}
    <div style={{display:"flex",gap:12,marginBottom:10,fontSize:11,color:S.t2}}>
      <span>{m.items.length}メニュー</span><span style={{color:S.ok}}>実働 {fmtMin(totalWork)}</span><span style={{color:S.wn}}>休憩 {fmtMin(totalRest)}</span>
    </div>
    {m.items.map(function(it,i){var ci=catInfo(it.cat||"other");var sc=schedule[i];return(<div key={it.id} style={{display:"flex",alignItems:"center",gap:8,padding:"8px 0",borderTop:i>0?"1px solid rgba(42,42,58,0.5)":"none"}}>
      <div style={{fontSize:12,fontWeight:700,color:ci.cl,fontFamily:"monospace",minWidth:42,textAlign:"right"}}>{sc.start}</div>
      <div style={{width:6,height:6,borderRadius:3,background:ci.cl,flexShrink:0}}/>
      <div style={{flex:1,minWidth:0}}><div style={{fontSize:13,fontWeight:500,overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{it.name} <span style={{fontSize:10,color:ci.cl}}>({ci.name})</span></div>
        <div style={{fontSize:10,color:S.t2}}>{fmtMin(it.duration||0)} × {it.sets||1}セット{it.rest?" / 休憩"+fmtMin(it.rest):""}{it.transition?" / 転換"+fmtMin(it.transition):""}</div></div>
      {it.memo&&<div style={{fontSize:9,color:S.t3,maxWidth:60,overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{it.memo}</div>}
    </div>);})}
    <div style={{display:"flex",gap:8,marginTop:10}}>
      {p.onEdit&&<button onClick={function(){p.onEdit(m);}} style={{flex:1,padding:8,background:S.c2,border:"1px solid "+S.bd,borderRadius:8,color:S.t2,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>✏️ 編集</button>}
      {p.onDelete&&<button onClick={function(){p.onDelete(m.id);}} style={{padding:8,background:S.c2,border:"1px solid "+S.bd,borderRadius:8,color:S.red,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>🗑</button>}
    </div>
  </div>);
}

/* ═══ TODAY VIEW ═══ */
function TodayView(p){
  var today=todayStr();
  // Compute upcoming games (future only) sorted by date
  var upcoming=(p.games||[]).filter(function(g){return g.date>=today;}).sort(function(a,b){return a.date.localeCompare(b.date);});
  // Count practice days (practice/MTG/game, not OFF) between today and target date
  function countPracticeDays(target){
    if(!target||target<today)return 0;
    return (p.menus||[]).filter(function(m){
      if(m.date<today||m.date>=target)return false;
      var cats=(m.items||[]).map(function(it){return it.cat||"other";});
      if(cats.length===0)return false;
      // exclude if all OFF
      if(cats.every(function(c){return c==="off";}))return false;
      return true;
    }).length;
  }
  function gameTypeColor(t){return t==="opening"?"#00d4ff":t==="final"?"#e040fb":"#ff9800";}
  function gameTypeLbl(t){return t==="opening"?"開幕戦":t==="final"?"最終戦":"重要試合";}
  return(<div>
    <div style={{fontSize:22,fontWeight:700,marginBottom:4}}>今日の練習</div>
    <div style={{fontSize:13,color:S.t2,marginBottom:16}}>{dateLbl(today)}</div>
    {upcoming.length>0&&(<div style={{marginBottom:16}}>
      {upcoming.slice(0,3).map(function(g){
        var d=daysBetween(today,g.date);var cl=gameTypeColor(g.type);var pd=countPracticeDays(g.date);
        return(<div key={g.id} style={{display:"flex",alignItems:"center",gap:10,padding:"10px 14px",background:S.c1,border:"1px solid "+cl+"55",borderRadius:12,marginBottom:6}}>
          <div style={{width:8,height:8,borderRadius:4,background:cl,flexShrink:0}}/>
          <div style={{flex:1,minWidth:0}}>
            <div style={{fontSize:12,fontWeight:600,color:cl}}>{g.title||gameTypeLbl(g.type)}</div>
            <div style={{fontSize:11,color:S.t2}}>{fmtDateFull(g.date)}{g.memo?" ・ "+g.memo:""}</div>
          </div>
          <div style={{textAlign:"right",flexShrink:0}}>
            <div style={{fontSize:18,fontWeight:800,color:cl,lineHeight:1,fontFamily:"monospace"}}>{d===0?"当日":"あと"+d+"日"}</div>
            <div style={{fontSize:9,color:S.t3,marginTop:2}}>練習{pd}日</div>
          </div>
        </div>);
      })}
    </div>)}
    {p.menu?<MenuCard menu={p.menu} onEdit={p.onEdit} onTimer={p.onTimer} onDelete={p.onDelete}/>
    :(<div style={{textAlign:"center",padding:40}}>
      <div style={{fontSize:40,marginBottom:12,opacity:0.3}}>🥍</div>
      <div style={{color:S.t3,fontSize:14,marginBottom:20}}>今日のメニューはまだありません</div>
      <button onClick={p.onNew} style={Object.assign({},btnP,{marginBottom:10})}>＋ 新規メニュー作成</button>
      {p.templates.length>0&&(<div>
        <div style={{fontSize:12,color:S.t2,marginTop:16,marginBottom:8}}>テンプレートから作成</div>
        {p.templates.map(function(t){return(<button key={t.id} onClick={function(){p.onFromTpl(t);}} style={{display:"flex",alignItems:"center",justifyContent:"space-between",width:"100%",padding:"12px 14px",background:S.c1,border:"1px solid "+S.bd,borderRadius:12,color:S.tx,fontFamily:"sans-serif",cursor:"pointer",marginBottom:6,textAlign:"left"}}>
          <div><div style={{fontSize:14,fontWeight:600}}>{t.name}</div><div style={{fontSize:11,color:S.t2}}>{t.items.length}メニュー</div></div><span style={{color:S.t3}}>›</span></button>);})}
      </div>)}
    </div>)}
  </div>);
}

/* ═══ CALENDAR VIEW ═══ */
function CalView(p){
  var [month,setMonth]=useState(function(){var d=new Date();return{y:d.getFullYear(),m:d.getMonth()};});
  var dim=new Date(month.y,month.m+1,0).getDate();
  var fd=(new Date(month.y,month.m,1).getDay()+6)%7;
  var cells=[];for(var i=0;i<fd;i++)cells.push(null);for(var d=1;d<=dim;d++)cells.push(d);
  var [selDate,setSelDate]=useState(null);
  var [showGames,setShowGames]=useState(false);
  var [editGame,setEditGame]=useState(null); // game being edited
  var selMenu=selDate?p.menus.find(function(m){return m.date===selDate;}):null;
  var games=p.games||[];
  var today=todayStr();
  var upcoming=games.filter(function(g){return g.date>=today;}).sort(function(a,b){return a.date.localeCompare(b.date);});

  function countPracticeDays(target){
    if(!target||target<today)return 0;
    return (p.menus||[]).filter(function(m){
      if(m.date<today||m.date>=target)return false;
      var cats=(m.items||[]).map(function(it){return it.cat||"other";});
      if(cats.length===0)return false;
      if(cats.every(function(c){return c==="off";}))return false;
      return true;
    }).length;
  }
  function gameTypeColor(t){return t==="opening"?"#00d4ff":t==="final"?"#e040fb":"#ff9800";}
  function gameTypeLbl(t){return t==="opening"?"開幕戦":t==="final"?"最終戦":"重要試合";}

  function addGame(){setEditGame({id:ID(),date:today,title:"",type:"important",memo:""});}
  function saveGame(g){if(!g.title||!g.date)return;var ex=games.find(function(x){return x.id===g.id;});if(ex)p.setGames(games.map(function(x){return x.id===g.id?g:x;}));else p.setGames(games.concat([g]));setEditGame(null);}
  function delGame(id){if(confirm("削除しますか？")){p.setGames(games.filter(function(x){return x.id!==id;}));setEditGame(null);}}

  return(<div>
    <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:14}}>
      <div style={{fontSize:22,fontWeight:700}}>シーズンカレンダー</div>
      <button onClick={function(){setShowGames(!showGames);}} style={{padding:"6px 12px",background:showGames?S.ac+"22":S.c1,border:"1px solid "+(showGames?S.ac:S.bd),borderRadius:8,color:showGames?S.ac:S.t2,fontSize:11,fontWeight:600,cursor:"pointer",fontFamily:"sans-serif"}}>🏆 試合管理</button>
    </div>

    {/* Upcoming games summary */}
    {!showGames&&upcoming.length>0&&(<div style={{marginBottom:14}}>
      {upcoming.slice(0,2).map(function(g){var d=daysBetween(today,g.date);var cl=gameTypeColor(g.type);var pd=countPracticeDays(g.date);
        return(<div key={g.id} style={{display:"flex",alignItems:"center",gap:10,padding:"10px 14px",background:S.c1,border:"1px solid "+cl+"55",borderRadius:12,marginBottom:6}}>
          <div style={{width:8,height:8,borderRadius:4,background:cl,flexShrink:0}}/>
          <div style={{flex:1,minWidth:0}}>
            <div style={{fontSize:12,fontWeight:600,color:cl}}>{g.title||gameTypeLbl(g.type)}</div>
            <div style={{fontSize:11,color:S.t2}}>{fmtDateFull(g.date)}{g.memo?" ・ "+g.memo:""}</div>
          </div>
          <div style={{textAlign:"right",flexShrink:0}}>
            <div style={{fontSize:18,fontWeight:800,color:cl,lineHeight:1,fontFamily:"monospace"}}>{d===0?"当日":"あと"+d+"日"}</div>
            <div style={{fontSize:9,color:S.t3,marginTop:2}}>練習{pd}日</div>
          </div>
        </div>);})}
    </div>)}

    {/* Game management panel */}
    {showGames&&(<div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,marginBottom:14}}>
      <div style={{fontSize:12,fontWeight:600,color:S.t2,marginBottom:10}}>登録された試合</div>
      {games.length===0&&!editGame&&<div style={{fontSize:12,color:S.t3,padding:"8px 0",textAlign:"center"}}>試合がまだ登録されていません</div>}
      {games.sort(function(a,b){return a.date.localeCompare(b.date);}).map(function(g){
        if(editGame&&editGame.id===g.id)return null;
        var cl=gameTypeColor(g.type);var isPast=g.date<today;
        return(<div key={g.id} onClick={function(){setEditGame(Object.assign({},g));}} style={{display:"flex",alignItems:"center",gap:8,padding:"8px 10px",background:S.c2,borderRadius:8,marginBottom:6,cursor:"pointer",opacity:isPast?0.5:1}}>
          <div style={{width:6,height:6,borderRadius:3,background:cl,flexShrink:0}}/>
          <div style={{flex:1,minWidth:0}}>
            <div style={{fontSize:12,fontWeight:600,color:cl}}>{g.title||gameTypeLbl(g.type)}</div>
            <div style={{fontSize:10,color:S.t2}}>{fmtDateFull(g.date)}{g.memo?" ・ "+g.memo:""}</div>
          </div>
          <span style={{color:S.t3,fontSize:11}}>›</span>
        </div>);
      })}
      {editGame&&(<div style={{background:S.c2,border:"1px solid "+S.ac,borderRadius:10,padding:12,marginTop:8}}>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:8}}>
          <div><div style={{fontSize:10,color:S.t2,marginBottom:4}}>日付</div><input type="date" value={editGame.date} onChange={function(e){setEditGame(Object.assign({},editGame,{date:e.target.value}));}} style={Object.assign({},iS,{colorScheme:"dark"})}/></div>
          <div><div style={{fontSize:10,color:S.t2,marginBottom:4}}>種別</div>
            <select value={editGame.type} onChange={function(e){setEditGame(Object.assign({},editGame,{type:e.target.value}));}} style={iS}>
              <option value="opening">開幕戦</option><option value="final">最終戦</option><option value="important">重要試合</option>
            </select></div>
        </div>
        <div style={{marginBottom:8}}><div style={{fontSize:10,color:S.t2,marginBottom:4}}>試合名</div><input value={editGame.title} onChange={function(e){setEditGame(Object.assign({},editGame,{title:e.target.value}));}} placeholder="例：vs A大学" style={iS}/></div>
        <div style={{marginBottom:10}}><div style={{fontSize:10,color:S.t2,marginBottom:4}}>メモ</div><input value={editGame.memo||""} onChange={function(e){setEditGame(Object.assign({},editGame,{memo:e.target.value}));}} placeholder="会場・対戦相手など" style={iS}/></div>
        <div style={{display:"flex",gap:6}}>
          <button onClick={function(){saveGame(editGame);}} disabled={!editGame.title||!editGame.date} style={{flex:1,padding:8,background:grad,border:"none",borderRadius:8,color:"#fff",fontSize:12,fontWeight:600,cursor:"pointer",opacity:editGame.title&&editGame.date?1:0.4,fontFamily:"sans-serif"}}>保存</button>
          {games.find(function(x){return x.id===editGame.id;})&&<button onClick={function(){delGame(editGame.id);}} style={{padding:8,background:"transparent",border:"1px solid "+S.red,borderRadius:8,color:S.red,fontSize:11,cursor:"pointer",fontFamily:"sans-serif"}}>🗑</button>}
          <button onClick={function(){setEditGame(null);}} style={{padding:8,background:"transparent",border:"1px solid "+S.bd,borderRadius:8,color:S.t2,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>取消</button>
        </div>
      </div>)}
      {!editGame&&<button onClick={addGame} style={{width:"100%",padding:10,background:"transparent",border:"1px dashed "+S.bd,borderRadius:10,color:S.ac,fontSize:12,cursor:"pointer",fontFamily:"sans-serif",marginTop:8}}>＋ 試合を追加</button>}
    </div>)}

    <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:12}}>
      <button onClick={function(){setMonth(month.m===0?{y:month.y-1,m:11}:{y:month.y,m:month.m-1});}} style={{background:"none",border:"none",color:S.t2,fontSize:18,cursor:"pointer",fontFamily:"sans-serif"}}>‹</button>
      <span style={{fontSize:16,fontWeight:600}}>{month.y}年{month.m+1}月</span>
      <button onClick={function(){setMonth(month.m===11?{y:month.y+1,m:0}:{y:month.y,m:month.m+1});}} style={{background:"none",border:"none",color:S.t2,fontSize:18,cursor:"pointer",fontFamily:"sans-serif"}}>›</button></div>
    <div style={{display:"grid",gridTemplateColumns:"repeat(7,1fr)",gap:4,marginBottom:16}}>
      {["月","火","水","木","金","土","日"].map(function(d){return <div key={d} style={{textAlign:"center",fontSize:10,color:S.t3,padding:4}}>{d}</div>;})}
      {cells.map(function(d,i){if(!d)return <div key={"e"+i}/>;
        var ds=month.y+"-"+String(month.m+1).padStart(2,"0")+"-"+String(d).padStart(2,"0");
        var menuOfDay=p.menus.find(function(m){return m.date===ds;});var has=!!menuOfDay;var dotCl=has?menuDotColor(menuOfDay):null;
        var gameOfDay=games.find(function(g){return g.date===ds;});
        var isSel=selDate===ds;var isT=ds===todayStr();
        var brdr=gameOfDay?gameTypeColor(gameOfDay.type):(isT?S.ac:(isSel?S.ac:has?S.bd:"transparent"));
        var brdrW=gameOfDay||isT?2:1;
        return(<button key={ds} onClick={function(){setSelDate(isSel?null:ds);}} style={{padding:6,background:isSel?S.ac+"33":has?S.c1:"transparent",border:brdrW+"px solid "+brdr,borderRadius:8,color:isSel?S.ac:isT?S.ac:S.tx,fontSize:13,fontWeight:isT||gameOfDay?700:400,cursor:"pointer",fontFamily:"sans-serif",position:"relative",textAlign:"center"}}>
          {d}
          {gameOfDay&&<div style={{position:"absolute",top:1,right:2,fontSize:8,color:gameTypeColor(gameOfDay.type),fontWeight:700}}>★</div>}
          {has&&<div style={{position:"absolute",bottom:2,left:"50%",transform:"translateX(-50%)",width:5,height:5,borderRadius:3,background:dotCl}}/>}</button>);})}</div>
    {/* Legend */}
    <div style={{display:"flex",flexWrap:"wrap",gap:10,marginBottom:12,fontSize:10,color:S.t2}}>
      <div style={{display:"flex",alignItems:"center",gap:4}}><div style={{width:6,height:6,borderRadius:3,background:"#00e676"}}/>練習</div>
      <div style={{display:"flex",alignItems:"center",gap:4}}><div style={{width:6,height:6,borderRadius:3,background:"#ff9800"}}/>試合</div>
      <div style={{display:"flex",alignItems:"center",gap:4}}><div style={{width:6,height:6,borderRadius:3,background:"#2196f3"}}/>MTG</div>
      <div style={{display:"flex",alignItems:"center",gap:4}}><div style={{width:6,height:6,borderRadius:3,background:"#f44336"}}/>OFF</div>
      <div style={{display:"flex",alignItems:"center",gap:4,color:"#00d4ff"}}><span style={{fontWeight:700}}>★</span>重要試合</div>
    </div>
    {selDate&&(<div><div style={{fontSize:14,fontWeight:600,marginBottom:10}}>{dateLbl(selDate)}</div>
      {selMenu?<MenuCard menu={selMenu} onEdit={p.onSelect} onTimer={p.onTimer} onDelete={p.onDelete}/>
      :<div style={{textAlign:"center",padding:20}}><div style={{color:S.t3,fontSize:13,marginBottom:12}}>メニューなし</div>
        <button onClick={function(){p.onNew(selDate);}} style={Object.assign({},btnP,{fontSize:13,padding:10})}>＋ この日のメニューを作成</button></div>}
    </div>)}
  </div>);
}

/* ═══ MENU EDITOR ═══ */
function MenuEditor(p){
  var [menu,setMenu]=useState(JSON.parse(JSON.stringify(p.menu)));
  var [tplName,setTplName]=useState("");
  var [showTpl,setShowTpl]=useState(false);
  var [showTplList,setShowTplList]=useState(false);

  function addItem(){setMenu(Object.assign({},menu,{items:menu.items.concat([{id:ID(),name:"",cat:"other",duration:300,sets:1,rest:60,transition:60,memo:""}])}));}
  function upd(idx,field,val){var items=menu.items.map(function(it,i){if(i!==idx)return it;var n={};for(var k in it)n[k]=it[k];n[field]=val;return n;});setMenu(Object.assign({},menu,{items:items}));}
  function rem(idx){setMenu(Object.assign({},menu,{items:menu.items.filter(function(_,i){return i!==idx;})}));}
  function mov(idx,dir){var items=menu.items.slice();var ni=idx+dir;if(ni<0||ni>=items.length)return;var tmp=items[idx];items[idx]=items[ni];items[ni]=tmp;setMenu(Object.assign({},menu,{items:items}));}

  function replaceTpl(t){
    var newItems=t.items.map(function(it){return Object.assign({},it,{id:ID()});});
    setMenu(Object.assign({},menu,{items:newItems}));
    setShowTplList(false);
  }

  return(<div>
    <button onClick={p.onCancel} style={{background:"none",border:"none",color:S.t2,fontSize:14,cursor:"pointer",padding:"8px 0",marginBottom:12,fontFamily:"sans-serif"}}>← 戻る</button>
    <div style={{display:"flex",alignItems:"center",gap:8,marginBottom:16}}>
      <div style={{fontSize:20,fontWeight:700}}>{p.isTplMode?"テンプレート編集":"メニュー編集"}</div>
      {p.isTplMode&&<span style={{fontSize:10,fontWeight:600,padding:"3px 8px",borderRadius:6,background:S.ac+"22",color:S.ac}}>TEMPLATE</span>}
    </div>
    {p.isTplMode?(<div style={{marginBottom:12}}>
      <div style={{fontSize:12,color:S.t2,marginBottom:6}}>テンプレート名</div>
      <input value={menu._tplName||""} onChange={function(e){setMenu(Object.assign({},menu,{_tplName:e.target.value}));}} placeholder="例：平日基本メニュー" style={iS}/>
    </div>):(<div style={{display:"grid",gridTemplateColumns:"2fr 1fr",gap:8,marginBottom:12}}>
      <div><div style={{fontSize:12,color:S.t2,marginBottom:6}}>タイトル</div><input value={menu.title} onChange={function(e){setMenu(Object.assign({},menu,{title:e.target.value}));}} style={iS}/></div>
      <div><div style={{fontSize:12,color:S.t2,marginBottom:6}}>開始時刻</div><input type="time" value={menu.startTime||"09:00"} onChange={function(e){setMenu(Object.assign({},menu,{startTime:e.target.value}));}} style={Object.assign({},iS,{colorScheme:"dark"})}/></div>
    </div>)}
    {!p.isTplMode&&menu.items.length>0&&(<div style={{display:"flex",alignItems:"center",gap:8,marginBottom:12,padding:"8px 10px",background:S.c2,borderRadius:8}}>
      <span style={{fontSize:13,fontWeight:700,color:S.ac,fontFamily:"monospace"}}>{menu.startTime||"09:00"}</span>
      <span style={{fontSize:11,color:S.t3}}>→</span>
      <span style={{fontSize:13,fontWeight:700,color:S.pp,fontFamily:"monospace"}}>{totalEndTime(menu.items,menu.startTime||"09:00")}</span>
      <span style={{fontSize:10,color:S.t2,marginLeft:"auto"}}>終了予定</span>
    </div>)}

    {/* Template Panel - hidden in template edit mode */}
    {!p.isTplMode&&(<div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,marginBottom:16}}>
      <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:showTplList||showTpl?12:0}}>
        <span style={{fontSize:13,fontWeight:600,color:S.t2}}>📋 テンプレート</span>
        <div style={{display:"flex",gap:6}}>
          <button onClick={function(){setShowTplList(!showTplList);setShowTpl(false);}} style={{padding:"7px 12px",background:showTplList?S.ac+"22":"transparent",border:"1px solid "+(showTplList?S.ac:S.bd),borderRadius:8,color:showTplList?S.ac:S.t2,fontSize:11,fontWeight:600,cursor:"pointer",fontFamily:"sans-serif"}}>📂 読み込む</button>
          <button onClick={function(){setShowTpl(!showTpl);setShowTplList(false);}} disabled={menu.items.length===0} style={{padding:"7px 12px",background:showTpl?S.pp+"22":"transparent",border:"1px solid "+(showTpl?S.pp:S.bd),borderRadius:8,color:menu.items.length===0?S.t3:(showTpl?S.pp:S.t2),fontSize:11,fontWeight:600,cursor:menu.items.length===0?"default":"pointer",fontFamily:"sans-serif",opacity:menu.items.length===0?0.5:1}}>💾 保存</button>
        </div>
      </div>

      {/* Load template panel */}
      {showTplList&&(<div>
        <div style={{fontSize:11,color:S.t3,marginBottom:10,lineHeight:1.5}}>
          保存したテンプレートを呼び出します。<br/>
          <span style={{color:S.ok}}>「使う」</span>で現在の内容に置き換え。<span style={{color:S.ac}}>「編集」</span>でテンプレ自体を変更できます。
        </div>
        {p.templates.length===0?(<div style={{textAlign:"center",padding:24,color:S.t3,fontSize:12}}>
          テンプレートがありません<br/><span style={{fontSize:10}}>メニューを作成して「💾 保存」から登録できます</span>
        </div>):p.templates.map(function(t){
          var totalW=t.items.reduce(function(s,it){return s+itemWork(it);},0);
          return(<div key={t.id} style={{background:S.c2,borderRadius:10,padding:12,marginBottom:8,border:"1px solid "+S.bd}}>
            <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:10}}>
              <div style={{flex:1,minWidth:0}}>
                <div style={{fontSize:14,fontWeight:600,marginBottom:2}}>{t.name}</div>
                <div style={{fontSize:10,color:S.t2}}>{t.items.length}メニュー ・ 実働{fmtMin(totalW)}</div>
              </div>
              <button onClick={function(){if(confirm("「"+t.name+"」を削除しますか？")){p.onDeleteTpl(t.id);}}} title="削除" style={{padding:"4px 8px",background:"transparent",border:"1px solid "+S.bd,borderRadius:6,color:S.red,fontSize:11,cursor:"pointer",fontFamily:"sans-serif"}}>🗑</button>
            </div>
            <div style={{display:"flex",gap:6}}>
              <button onClick={function(){if(menu.items.length===0||confirm("現在のメニューを置き換えますか？")){replaceTpl(t);}}} style={{flex:1,padding:9,background:S.ok,border:"none",borderRadius:8,color:"#000",fontSize:12,fontWeight:700,cursor:"pointer",fontFamily:"sans-serif"}}>📥 使う</button>
              <button onClick={function(){if(menu.items.length>0&&!confirm("現在の編集中の内容は失われます。テンプレ編集に移りますか？"))return;p.onEditTpl(t);}} style={{flex:1,padding:9,background:"transparent",border:"1px solid "+S.ac,borderRadius:8,color:S.ac,fontSize:12,fontWeight:600,cursor:"pointer",fontFamily:"sans-serif"}}>✏️ 編集</button>
            </div>
          </div>);
        })}
      </div>)}

      {/* Save as template panel */}
      {showTpl&&(<div>
        <div style={{fontSize:11,color:S.t3,marginBottom:10,lineHeight:1.5}}>
          現在のメニュー（{menu.items.length}項目）を新しいテンプレートとして保存します。
        </div>
        <div style={{fontSize:11,color:S.t2,marginBottom:4}}>テンプレート名</div>
        <input value={tplName} onChange={function(e){setTplName(e.target.value);}} placeholder="例：平日基本メニュー" style={iS} autoFocus/>
        <div style={{display:"flex",gap:8,marginTop:10}}>
          <button onClick={function(){if(tplName.trim()&&menu.items.length>0){p.onSaveTpl({id:ID(),name:tplName.trim(),items:JSON.parse(JSON.stringify(menu.items))});setShowTpl(false);setTplName("");}}} disabled={!tplName.trim()||menu.items.length===0} style={{flex:1,padding:10,background:grad,border:"none",borderRadius:8,color:"#fff",fontSize:13,fontWeight:600,cursor:"pointer",opacity:tplName.trim()&&menu.items.length>0?1:0.4,fontFamily:"sans-serif"}}>新規テンプレ保存</button>
          <button onClick={function(){setShowTpl(false);setTplName("");}} style={{padding:10,background:"transparent",border:"1px solid "+S.bd,borderRadius:8,color:S.t2,fontSize:13,cursor:"pointer",fontFamily:"sans-serif"}}>取消</button>
        </div>
      </div>)}
    </div>)}

    {!p.isTplMode&&<TimeSummary items={menu.items}/>}

    {(function(){var sched=computeSchedule(menu.items,menu.startTime||"09:00");return menu.items.map(function(it,i){var ci=catInfo(it.cat||"other");var sc=sched[i];return(<div key={it.id} style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,marginBottom:8}}>
      <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:10}}>
        <div style={{display:"flex",alignItems:"center",gap:8}}>
          <div style={{width:8,height:8,borderRadius:4,background:ci.cl}}/>
          <span style={{fontSize:13,fontWeight:600,color:S.t2}}>#{i+1}</span>
          {!p.isTplMode&&<span style={{fontSize:12,fontWeight:700,color:ci.cl,fontFamily:"monospace"}}>{sc.start}</span>}
          {!p.isTplMode&&<span style={{fontSize:10,color:S.t3}}>→ {sc.end}</span>}
        </div>
        <div style={{display:"flex",gap:4}}>
          <button onClick={function(){mov(i,-1);}} disabled={i===0} style={{padding:"2px 8px",background:S.c2,border:"1px solid "+S.bd,borderRadius:6,color:i===0?S.t3:S.tx,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>↑</button>
          <button onClick={function(){mov(i,1);}} disabled={i===menu.items.length-1} style={{padding:"2px 8px",background:S.c2,border:"1px solid "+S.bd,borderRadius:6,color:i===menu.items.length-1?S.t3:S.tx,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>↓</button>
          <button onClick={function(){rem(i);}} style={{padding:"2px 8px",background:S.c2,border:"1px solid "+S.bd,borderRadius:6,color:S.red,fontSize:12,cursor:"pointer",fontFamily:"sans-serif"}}>✕</button></div></div>
      <div style={{marginBottom:8}}><div style={{fontSize:11,color:S.t2,marginBottom:4}}>メニュー名</div><input value={it.name} onChange={function(e){upd(i,"name",e.target.value);}} placeholder="例：6on6" style={iS}/></div>
      <div style={{marginBottom:8}}><div style={{fontSize:11,color:S.t2,marginBottom:4}}>カテゴリ</div>
        <div style={{display:"flex",flexWrap:"wrap",gap:4}}>{CATS.map(function(c){return(<button key={c.id} onClick={function(){upd(i,"cat",c.id);}} style={{padding:"4px 10px",borderRadius:16,fontSize:10,fontWeight:it.cat===c.id?600:400,background:it.cat===c.id?c.cl+"33":"transparent",border:"1px solid "+(it.cat===c.id?c.cl:S.bd),color:it.cat===c.id?c.cl:S.t3,cursor:"pointer",fontFamily:"sans-serif"}}>{c.name}</button>);})}</div></div>
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:8}}>
        <div><div style={{fontSize:11,color:S.t2,marginBottom:4}}>時間（分）</div><input type="number" min="0" step="1" value={typeof it.duration==="number"&&it.duration>=0?String(Math.round(it.duration/60)):""} onChange={function(e){var v=e.target.value.replace(/^0+(?=\d)/,"");if(v==="")upd(i,"duration",0);else{var n=parseInt(v,10);if(!isNaN(n)&&n>=0)upd(i,"duration",n*60);}}} style={iS}/></div>
        <div><div style={{fontSize:11,color:S.t2,marginBottom:4}}>セット数</div><input type="number" min="1" step="1" value={it.sets===""||it.sets===undefined?"":String(it.sets)} onChange={function(e){var v=e.target.value.replace(/^0+(?=\d)/,"");if(v==="")upd(i,"sets","");else{var n=parseInt(v,10);if(!isNaN(n))upd(i,"sets",Math.max(1,n));}}} onBlur={function(e){if(e.target.value===""||!Number(e.target.value))upd(i,"sets",1);}} style={iS}/></div></div>
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:8}}>
        <div><div style={{fontSize:11,color:S.t2,marginBottom:4}}>セット間休憩（分）</div><input type="number" min="0" step="1" value={typeof it.rest==="number"&&it.rest>=0?String(Math.round(it.rest/60)):""} onChange={function(e){var v=e.target.value.replace(/^0+(?=\d)/,"");if(v==="")upd(i,"rest",0);else{var n=parseInt(v,10);if(!isNaN(n)&&n>=0)upd(i,"rest",n*60);}}} style={iS}/></div>
        <div><div style={{fontSize:11,color:S.t2,marginBottom:4}}>転換休憩（分）</div><input type="number" min="0" step="1" value={typeof it.transition==="number"&&it.transition>=0?String(Math.round(it.transition/60)):""} onChange={function(e){var v=e.target.value.replace(/^0+(?=\d)/,"");if(v==="")upd(i,"transition",0);else{var n=parseInt(v,10);if(!isNaN(n)&&n>=0)upd(i,"transition",n*60);}}} style={iS}/></div></div>
      <div><div style={{fontSize:11,color:S.t2,marginBottom:4}}>メモ</div><input value={it.memo||""} onChange={function(e){upd(i,"memo",e.target.value);}} placeholder="意識ポイントなど" style={iS}/></div>
    </div>);});})()}

    <button onClick={addItem} style={{width:"100%",padding:12,background:"transparent",border:"1px dashed "+S.bd,borderRadius:12,color:S.ac,fontSize:14,cursor:"pointer",fontFamily:"sans-serif",marginBottom:16}}>＋ メニュー追加</button>
    <button onClick={function(){if(p.isTplMode&&!(menu._tplName||"").trim()){alert("テンプレート名を入力してください");return;}p.onSave(menu);}} disabled={menu.items.length===0||(p.isTplMode&&!(menu._tplName||"").trim())} style={Object.assign({},btnP,{opacity:menu.items.length>0&&(!p.isTplMode||(menu._tplName||"").trim())?1:0.4})}>{p.isTplMode?"テンプレートを保存":"保存する"}</button>
  </div>);
}

/* ═══ ANALYSIS VIEW ═══ */
function AnalysisView(p){
  var [rangeStart,setRangeStart]=useState(function(){var d=new Date();d.setDate(d.getDate()-6);return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");});
  var [rangeEnd,setRangeEnd]=useState(todayStr());

  var targetMenus=p.menus.filter(function(x){return x.date>=rangeStart&&x.date<=rangeEnd;});

  var allItems=[];targetMenus.forEach(function(m){m.items.forEach(function(it){allItems.push(it);});});

  var totalWork=allItems.reduce(function(s,it){return s+itemWork(it);},0);
  var totalRest=allItems.reduce(function(s,it){return s+itemRest(it);},0);
  var totalAll=totalWork+totalRest;

  // Category breakdown
  var catMap={};allItems.forEach(function(it){var c=it.cat||"other";if(!catMap[c])catMap[c]={work:0,rest:0,count:0};catMap[c].work+=itemWork(it);catMap[c].rest+=itemRest(it);catMap[c].count++;});
  var catKeys=CATS.map(function(c){return c.id;}).filter(function(id){return catMap[id];});

  return(<div>
    <div style={{fontSize:22,fontWeight:700,marginBottom:4}}>練習分析</div>
    <div style={{fontSize:13,color:S.t2,marginBottom:16}}>カテゴリ別の時間配分を確認</div>

    {/* Date range selector */}
    <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8,marginBottom:16}}>
      <div><div style={{fontSize:12,color:S.t2,marginBottom:6}}>開始</div><input type="date" value={rangeStart} onChange={function(e){setRangeStart(e.target.value);}} style={Object.assign({},iS,{colorScheme:"dark"})}/></div>
      <div><div style={{fontSize:12,color:S.t2,marginBottom:6}}>終了</div><input type="date" value={rangeEnd} onChange={function(e){setRangeEnd(e.target.value);}} style={Object.assign({},iS,{colorScheme:"dark"})}/></div>
    </div>

    {allItems.length===0?(<div style={{textAlign:"center",padding:40,color:S.t3}}>
      <div style={{fontSize:32,marginBottom:8,opacity:0.3}}>📊</div>
      <div>該当するメニューがありません</div>
    </div>):(<div>
      {/* Header stats */}
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:8,marginBottom:16}}>
        <div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,textAlign:"center"}}>
          <div style={{fontSize:10,color:S.t2,marginBottom:4}}>実働時間</div>
          <div style={{fontSize:20,fontWeight:800,color:S.ok}}>{fmtMin(totalWork)}</div></div>
        <div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,textAlign:"center"}}>
          <div style={{fontSize:10,color:S.t2,marginBottom:4}}>休憩時間</div>
          <div style={{fontSize:20,fontWeight:800,color:S.wn}}>{fmtMin(totalRest)}</div></div>
        <div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,textAlign:"center"}}>
          <div style={{fontSize:10,color:S.t2,marginBottom:4}}>総時間</div>
          <div style={{fontSize:20,fontWeight:800,color:S.ac}}>{fmtMin(totalAll)}</div></div>
      </div>

      <div style={{fontSize:12,color:S.t2,marginBottom:12}}>{targetMenus.length}日分の練習データ</div>

      {/* Stack bar */}
      <div style={{fontSize:12,fontWeight:600,color:S.t2,marginBottom:8}}>カテゴリ別 時間配分</div>
      {totalWork>0&&<div style={{display:"flex",borderRadius:10,overflow:"hidden",height:28,marginBottom:12}}>
        {catKeys.map(function(cid){var ci=catInfo(cid);var d=catMap[cid];var pct=(d.work/totalWork)*100;if(pct<0.5)return null;
          return(<div key={cid} style={{width:pct+"%",background:ci.cl,display:"flex",alignItems:"center",justifyContent:"center",fontSize:8,fontWeight:700,color:"#000",overflow:"hidden",whiteSpace:"nowrap",padding:"0 2px"}}>{pct>10?ci.name:""}</div>);})}
      </div>}

      {/* Category detail cards */}
      {catKeys.map(function(cid){var ci=catInfo(cid);var d=catMap[cid];var pct=totalWork>0?Math.round((d.work/totalWork)*100):0;
        return(<div key={cid} style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,marginBottom:8}}>
          <div style={{display:"flex",alignItems:"center",justifyContent:"space-between"}}>
            <div style={{display:"flex",alignItems:"center",gap:8}}>
              <div style={{width:12,height:12,borderRadius:3,background:ci.cl}}/>
              <span style={{fontSize:14,fontWeight:600}}>{ci.name}</span>
              <span style={{fontSize:11,color:S.t3}}>{d.count}項目</span>
            </div>
            <div style={{textAlign:"right"}}>
              <div style={{fontSize:16,fontWeight:700,color:ci.cl,fontFamily:"monospace"}}>{fmtMin(d.work)}</div>
              <div style={{fontSize:10,color:S.t3}}>{pct}%</div>
            </div>
          </div>
          {/* Mini progress bar */}
          <div style={{height:4,background:S.c2,borderRadius:2,marginTop:8,overflow:"hidden"}}>
            <div style={{width:pct+"%",height:"100%",background:ci.cl,borderRadius:2}}/></div>
        </div>);})}

      {/* Work vs Rest ratio */}
      <div style={{background:S.c1,border:"1px solid "+S.bd,borderRadius:12,padding:14,marginTop:12}}>
        <div style={{fontSize:12,fontWeight:600,color:S.t2,marginBottom:10}}>実働 vs 休憩の比率</div>
        <div style={{display:"flex",borderRadius:8,overflow:"hidden",height:24}}>
          {totalAll>0&&<div style={{width:((totalWork/totalAll)*100)+"%",background:S.ok,display:"flex",alignItems:"center",justifyContent:"center",fontSize:9,fontWeight:700,color:"#000"}}>実働 {Math.round((totalWork/totalAll)*100)}%</div>}
          {totalAll>0&&<div style={{width:((totalRest/totalAll)*100)+"%",background:S.wn,display:"flex",alignItems:"center",justifyContent:"center",fontSize:9,fontWeight:700,color:"#000"}}>休憩 {Math.round((totalRest/totalAll)*100)}%</div>}
        </div>
      </div>
    </div>)}
  </div>);
}

/* ═══ TIMER VIEW ═══ */
function TimerView(p){
  var items=p.menu.items;
  var [mi,setMi]=useState(0);var [si,setSi]=useState(0);var [phase,setPhase]=useState("work");
  var [time,setTime]=useState(0);var [running,setRunning]=useState(false);var [done,setDone]=useState(false);
  var ref=useRef(null);
  var cur=items[mi]||null;
  var totalDur=cur?(phase==="work"?(cur.duration||0):phase==="rest"?(cur.rest||0):(cur.transition||0)):0;

  useEffect(function(){if(cur&&!done){setTime(phase==="work"?(cur.duration||0):phase==="rest"?(cur.rest||0):(cur.transition||0));};},[mi,si,phase]);
  useEffect(function(){if(running&&time>0){ref.current=setTimeout(function(){setTime(time-1);},1000);}else if(running&&time===0){adv();}return function(){if(ref.current)clearTimeout(ref.current);};},[running,time]);

  function adv(){
    if(phase==="work"){if(si<(cur.sets||1)-1){if((cur.rest||0)>0)setPhase("rest");else{setSi(si+1);setPhase("work");}}
      else if(mi<items.length-1){if((cur.transition||0)>0)setPhase("transition");else{setMi(mi+1);setSi(0);setPhase("work");}}
      else{setRunning(false);setDone(true);}}
    else if(phase==="rest"){setSi(si+1);setPhase("work");}
    else{setMi(mi+1);setSi(0);setPhase("work");}}

  var acC=phase==="work"?S.ac:phase==="rest"?S.ok:S.wn;
  var phL=phase==="work"?"WORK":phase==="rest"?"REST":"TRANSITION";
  var prog=totalDur>0?(1-time/totalDur):0;
  var ci=cur?catInfo(cur.cat||"other"):null;
  var schedule=computeSchedule(items,p.menu.startTime||"09:00");
  var curSched=schedule[mi]||{start:"",end:""};

  if(done){return(<div style={{minHeight:"100vh",background:"#0a0a0f",display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",padding:24,fontFamily:"sans-serif",color:S.tx}}>
    <div style={{fontSize:48,marginBottom:16}}>🎉</div><div style={{fontSize:28,fontWeight:800,marginBottom:8}}>COMPLETE</div>
    <div style={{fontSize:14,color:S.t2,marginBottom:32}}>練習おつかれさま！</div>
    <button onClick={p.onExit} style={Object.assign({},btnP,{maxWidth:320})}>戻る</button></div>);}

  return(<div style={{minHeight:"100vh",background:phase==="work"?"#0a0a0f":phase==="rest"?"#0a1a0f":"#1a0a0f",display:"flex",flexDirection:"column",fontFamily:"sans-serif",color:S.tx,userSelect:"none"}}>
    <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",padding:"12px 16px"}}>
      <button onClick={function(){setRunning(false);p.onExit();}} style={{background:"none",border:"none",color:S.t2,fontSize:14,cursor:"pointer",fontFamily:"sans-serif"}}>✕ 終了</button>
      <div style={{fontSize:11,color:S.t2}}>{mi+1}/{items.length}</div></div>
    <div style={{textAlign:"center",padding:"0 16px"}}>
      <div style={{fontSize:11,color:S.t3,marginBottom:2,fontFamily:"monospace"}}>{curSched.start} → {curSched.end}</div>
      <div style={{fontSize:14,color:S.t2,marginBottom:4}}>現在のメニュー</div>
      <div style={{fontSize:24,fontWeight:800}}>{cur?cur.name:"—"}</div>
      {ci&&<div style={{display:"inline-block",marginTop:6,padding:"2px 10px",borderRadius:12,background:ci.cl+"22",color:ci.cl,fontSize:11,fontWeight:600}}>{ci.name}</div>}
      {cur&&cur.memo&&<div style={{fontSize:12,color:S.t3,marginTop:4}}>{cur.memo}</div>}</div>
    <div style={{textAlign:"center",marginTop:16}}>
      <div style={{display:"inline-block",padding:"4px 16px",borderRadius:20,background:acC+"22",color:acC,fontSize:14,fontWeight:700,letterSpacing:2}}>{phL}</div>
      <div style={{fontSize:13,color:S.t2,marginTop:6}}>セット {si+1}/{cur?(cur.sets||1):1}</div></div>
    <div style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",padding:24}}>
      <div style={{fontSize:96,fontWeight:800,fontFamily:"monospace",color:acC,lineHeight:1}}>{fmtSec(time)}</div>
      <div style={{width:"80%",maxWidth:300,height:6,background:S.c2,borderRadius:3,marginTop:24,overflow:"hidden"}}>
        <div style={{width:(prog*100)+"%",height:"100%",background:acC,borderRadius:3,transition:"width 1s linear"}}/></div></div>
    <div style={{display:"flex",justifyContent:"center",gap:16,padding:"24px 16px 48px"}}>
      <button onClick={function(){setRunning(!running);}} style={{width:72,height:72,borderRadius:"50%",background:running?S.red:acC,border:"none",display:"flex",alignItems:"center",justifyContent:"center",fontSize:24,color:"#fff",cursor:"pointer",fontWeight:700,fontFamily:"sans-serif"}}>{running?"⏸":"▶"}</button>
      <button onClick={function(){adv();}} style={{width:56,height:56,borderRadius:"50%",background:S.c2,border:"2px solid "+S.bd,display:"flex",alignItems:"center",justifyContent:"center",fontSize:16,color:S.t2,cursor:"pointer",fontFamily:"sans-serif"}}>⏭</button></div>
    <div style={{padding:"0 16px 24px"}}><div style={{fontSize:11,color:S.t3,marginBottom:6}}>次のメニュー</div>
      {items.slice(mi+1,mi+3).map(function(it,i){var nci=catInfo(it.cat||"other");var ns=schedule[mi+1+i]||{start:""};return(<div key={it.id} style={{fontSize:12,color:S.t2,padding:"3px 0",display:"flex",alignItems:"center",gap:6}}><span style={{fontFamily:"monospace",color:nci.cl,fontWeight:600,minWidth:42}}>{ns.start}</span><span>{it.name}</span><span style={{color:nci.cl,fontSize:10}}>({nci.name})</span></div>);})}
      {mi>=items.length-1&&<div style={{fontSize:12,color:S.t3}}>最後のメニューです</div>}</div>
  </div>);
}
