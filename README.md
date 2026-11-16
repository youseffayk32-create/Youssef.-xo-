<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>X-O YF FINAL</title>
<script>
// === الأدمن كله هنا في الأول ===
var scoreX=0,scoreO=0,scoreD=0,soundOn=true,myHist=[],level="medium",mode="comp";
function openAdmin(){document.getElementById('adminModal').style.display='block';refreshAdmin();}
function closeAdmin(){document.getElementById('adminModal').style.display='none';}
function adminLogin(){
  if(document.getElementById("adminPass").value==="X-O#2026@YF!Admin9"){
    document.getElementById("loginArea").style.display="none";
    document.getElementById("adminArea").style.display="block";
    document.getElementById("loginError").style.display="none";refreshAdmin();
  }else{document.getElementById("loginError").style.display="block";}
}
function adminLogout(){document.getElementById("loginArea").style.display="block";document.getElementById("adminArea").style.display="none";document.getElementById("adminPass").value="";}
function refreshAdmin(){
  try{
    var t=scoreX+scoreO+scoreD;
    document.getElementById("statTotal").innerText=t;
    document.getElementById("statX").innerText=scoreX;
    document.getElementById("statO").innerText=scoreO;
    document.getElementById("statD").innerText=scoreD;
    document.getElementById("statRate").innerText=(t==0?0:Math.round(scoreX/t*100))+"%";
    document.getElementById("statLast").innerText=myHist.length>0?myHist[0].r+" - "+myHist[0].t:"-";
    document.getElementById("adminLevel").value=level;
    document.getElementById("adminMode").innerText=mode=="comp"?"ضد الكمبيوتر":(mode=="friend"?"ضد صاحبك":"أونلاين");
    document.getElementById("soundBtn").innerText=soundOn?"شغال ✅":"مقفول 🔇";
    var list=document.getElementById("historyList");
    if(myHist.length==0){list.innerHTML="";document.getElementById("noHistory").style.display="block";}
    else{document.getElementById("noHistory").style.display="none";var h="";for(var i=0;i<myHist.length;i++){h+='<div class="history-item"><span>'+myHist[i].r+'</span><span>'+myHist[i].m+' - '+myHist[i].t+'</span></div>';}list.innerHTML=h;}
  }catch(e){}
}
function toggleSound(){soundOn=!soundOn;try{localStorage.setItem("YF_SOUND",soundOn?"on":"off");}catch(e){}refreshAdmin();}
function adminChangeLevel(){level=document.getElementById("adminLevel").value;try{localStorage.setItem("YF_LEVEL",level);}catch(e){}refreshAdmin();}
function wipeAll(){
  if(!confirm("متأكد؟ هيمسح كل النقط والسجل!"))return;
  scoreX=0;scoreO=0;scoreD=0;myHist=[];soundOn=true;level="medium";
  try{localStorage.removeItem("YF_X");localStorage.removeItem("YF_O");localStorage.removeItem("YF_D");localStorage.removeItem("YF_HIST");}catch(e){}
  refreshAdmin();if(typeof reset==="function")reset();alert("اتمسح كله ✅");
}
</script>
<script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
<style>
*{box-sizing:border-box}
body{margin:0;background:#000;color:#fff;font-family:Arial;text-align:center;padding-bottom:110px}
.header{background:#111;padding:14px;border-bottom:2px solid #C9A86A}
.gold{color:#C9A86A}
.score{display:grid;grid-template-columns:1fr 1fr 1fr;gap:8px;margin:12px;padding:0 10px}
.box{background:#111;border:1px solid #C9A86A;border-radius:12px;padding:10px 5px}
.box b{font-size:22px;color:#C9A86A;display:block}
.modes{display:flex;justify-content:center;gap:8px;flex-wrap:wrap;margin:10px;padding:0 10px}
.mode-btn{background:#111;color:#fff;border:1px solid #333;padding:9px 13px;border-radius:20px;cursor:pointer}
.mode-btn.active{background:#C9A86A;color:#000;font-weight:bold}
#board{width:300px;margin:18px auto;display:grid;grid-template-columns:90px 90px 90px;gap:10px;justify-content:center}
.cell{width:90px;height:90px;background:#111;border:2px solid #C9A86A;font-size:46px;font-weight:bold;display:flex;align-items:center;justify-content:center;border-radius:14px;cursor:pointer}
.cell.X{color:#00ffcc}.cell.O{color:#ffcc00}.cell.win{background:#C9A86A;color:#000}
#status{font-size:20px;margin:10px;color:#C9A86A;font-weight:bold}
.btn{background:#C9A86A;color:#000;border:none;padding:12px 22px;border-radius:20px;font-weight:bold;cursor:pointer;margin:5px}
.btn2{background:#222;color:#fff;border:1px solid #C9A86A;padding:12px 18px;border-radius:20px;cursor:pointer;margin:5px}
.btn-danger{background:#ff3333;color:#fff;border:none;padding:12px 18px;border-radius:20px;font-weight:bold;cursor:pointer;margin:5px}
.admin-btn{display:none !important}
.exit-btn{position:fixed;bottom:20px;right:20px;background:#222;color:#C9A86A;border:1px solid #C9A86A;padding:12px 16px;border-radius:25px;font-weight:bold;cursor:pointer;z-index:10}
.online-box{background:#111;border:1px solid #C9A86A;border-radius:14px;padding:14px;margin:10px 14px;display:none}
input{padding:10px;border-radius:12px;border:1px solid #C9A86A;background:#000;color:#fff;width:80%;margin:5px;text-align:center;font-size:16px}
.code-big{font-size:26px;color:#C9A86A;font-weight:bold}
.modal{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.95);z-index:100;overflow:auto;padding:14px}
.admin-box{background:#111;border:1.5px solid #C9A86A;border-radius:14px;padding:18px;margin:20px auto;max-width:460px;text-align:right}
.admin-stat{background:#000;border:1px solid #333;border-radius:10px;padding:10px;margin:8px 0;display:flex;justify-content:space-between}
.admin-stat b{color:#C9A86A}
.history-item{background:#000;border-bottom:1px solid #222;padding:8px;font-size:13px;display:flex;justify-content:space-between}
.switch{display:flex;justify-content:space-between;align-items:center;background:#000;padding:10px;border-radius:10px;margin:8px 0}
</style>
</head>
<body>
<div class="header"><h2 class="gold" id="secretTitle" style="margin:0">❌⭕ X-O أونلاين</h2><p style="color:#777;font-size:12px;margin:5px 0 0">YF - Youssef Fayek</p></div>
<div class="score">
<div class="box">انت X<b id="scoreX">0</b></div>
<div class="box">تعادل<b id="scoreD">0</b></div>
<div class="box"><span id="enemyName">الكمبيوتر O</span><b id="scoreO">0</b></div>
</div>
<div class="modes">
<button class="mode-btn active" id="btnVsComp" onclick="setMode('comp')">ضد الكمبيوتر 🤖</button>
<button class="mode-btn" id="btnVsFriend" onclick="setMode('friend')">ضد صاحبك 👬</button>
<button class="mode-btn" id="btnOnline" onclick="setMode('online')">أونلاين 🌐</button>
</div>
<div class="modes" id="levels">
<button class="mode-btn" onclick="setLevel('easy',this)">سهل 😊</button>
<button class="mode-btn active" onclick="setLevel('medium',this)">متوسط 😎</button>
<button class="mode-btn" onclick="setLevel('hard',this)">مستحيل 💀</button>
</div>
<div id="onlineBox" class="online-box">
<p style="margin:0;color:#C9A86A;font-weight:bold">🌐 العب أونلاين مع صاحبك</p>
<p id="onlineStatus" style="font-size:13px;color:#aaa">لسه مش متصل</p>
<div id="createArea"><button class="btn" onclick="createRoom()">اعمل غرفة + كود</button></div>
<div id="myCodeArea" style="display:none"><p>ابعت الكود ده لصاحبك:</p><div class="code-big" id="myCode">---</div><button class="btn2" onclick="copyCode()">نسخ الكود 📋</button></div>
<div style="margin-top:10px;border-top:1px solid #333;padding-top:10px"><input id="joinCode" placeholder="اكتب كود صاحبك"><br><button class="btn" onclick="joinRoom()">ادخل الغرفة 🚪</button></div>
<button class="btn2" onclick="leaveOnline()">خروج من الأونلاين</button>
</div>
<div id="status">الدور على: X</div>
<div id="board">
<div class="cell" onclick="play(0)"></div>
<div class="cell" onclick="play(1)"></div>
<div class="cell" onclick="play(2)"></div>
<div class="cell" onclick="play(3)"></div>
<div class="cell" onclick="play(4)"></div>
<div class="cell" onclick="play(5)"></div>
<div class="cell" onclick="play(6)"></div>
<div class="cell" onclick="play(7)"></div>
<div class="cell" onclick="play(8)"></div>
</div>
<button class="btn" onclick="reset()">🔄 جولة جديدة</button>
<button class="btn2" onclick="resetScore()">تصفير النقط</button>
<p style="color:#666;font-size:12px">YF © 2026 - Youssef Fayek</p>
<button class="admin-btn" onclick="openAdmin()">👑</button>
<button class="exit-btn" onclick="resetScore();reset()">خروج</button>

<div id="adminModal" class="modal">
<div class="admin-box">
<h3 class="gold" style="text-align:center;margin:0">👑 لوحة تحكم الأدمن</h3>
<div id="loginArea" style="text-align:center">
<input type="password" id="adminPass" placeholder="اكتب الباسورد">
<br><button class="btn" onclick="adminLogin()">دخول</button>
<p id="loginError" style="color:#ff5555;font-size:13px;display:none">الباسورد غلط!</p>
</div>
<div id="adminArea" style="display:none">
<p style="color:#00ff88">✅ أهلا يا يوسف، انت الأدمن</p>
<div class="admin-stat"><span>📊 إجمالي الجولات</span><b id="statTotal">0</b></div>
<div class="admin-stat"><span>❌ فوز X</span><b id="statX">0</b></div>
<div class="admin-stat"><span>⭕ فوز O</span><b id="statO">0</b></div>
<div class="admin-stat"><span>🤝 تعادل</span><b id="statD">0</b></div>
<div class="admin-stat"><span>🔥 نسبة فوزك</span><b id="statRate">0%</b></div>
<div class="admin-stat"><span>🕒 آخر جولة</span><b id="statLast">-</b></div>
<h4 class="gold">⚙️ التحكم</h4>
<div class="switch"><span>🔊 الصوت</span><button class="btn2" id="soundBtn" onclick="toggleSound()">شغال ✅</button></div>
<div class="switch"><span>🤖 مستوى الكمبيوتر</span><select id="adminLevel" onchange="adminChangeLevel()"><option value="easy">سهل</option><option value="medium" selected>متوسط</option><option value="hard">مستحيل</option></select></div>
<div class="switch"><span>🎮 الوضع الحالي</span><b id="adminMode">ضد الكمبيوتر</b></div>
<h4 class="gold">📜 آخر 10 جولات</h4>
<div id="historyList" style="background:#000;border-radius:10px;overflow:hidden"></div>
<p id="noHistory" style="color:#777;font-size:13px;text-align:center">لسه مفيش جولات اتلعبت</p>
<div style="text-align:center;margin-top:15px">
<button class="btn2" onclick="resetScore();refreshAdmin()">تصفير النقط فقط</button>
<button class="btn-danger" onclick="wipeAll()">مسح كل البيانات 🗑️</button>
<br><button class="btn2" onclick="adminLogout()">تسجيل خروج</button>
<button class="btn" onclick="closeAdmin()">إغلاق</button>
</div>
</div>
</div>
</div>

<script>
var board=["","","","","","","","",""];
var turn="X"; var gameOver=false;
var peer=null, conn=null, mySymbol="X", isHost=false;
try{
if(localStorage.getItem("YF_X")){scoreX=parseInt(localStorage.getItem("YF_X"))||0;scoreO=parseInt(localStorage.getItem("YF_O"))||0;scoreD=parseInt(localStorage.getItem("YF_D"))||0;}
if(localStorage.getItem("YF_HIST")){myHist=JSON.parse(localStorage.getItem("YF_HIST"));}
if(localStorage.getItem("YF_SOUND")==="off"){soundOn=false;}
if(localStorage.getItem("YF_LEVEL")){level=localStorage.getItem("YF_LEVEL");}
}catch(e){}
function setMode(m){mode=m;document.getElementById("btnVsComp").className="mode-btn"+(m=="comp"?" active":"");document.getElementById("btnVsFriend").className="mode-btn"+(m=="friend"?" active":"");document.getElementById("btnOnline").className="mode-btn"+(m=="online"?" active":"");document.getElementById("enemyName").innerText=m=="comp"?"الكمبيوتر O":(m=="online"?"صاحبك أونلاين O":"صاحبك O");document.getElementById("levels").style.display=m=="comp"?"flex":"none";document.getElementById("onlineBox").style.display=m=="online"?"block":"none";if(m!="online"&&conn){try{conn.close()}catch(e){}conn=null;}reset();}
function setLevel(l,btn){level=l;try{localStorage.setItem("YF_LEVEL",l);}catch(e){}var b=document.getElementById("levels").getElementsByTagName("button");for(var i=0;i<b.length;i++){b[i].className="mode-btn";}if(btn)btn.className="mode-btn active";reset();}
function makeCode(){var c="ABCDEFGHJKLMNPQRSTUVWXYZ23456789";var r="";for(var i=0;i<5;i++){r+=c[Math.floor(Math.random()*c.length)];}return "YF-"+r;}
function createRoom(){if(typeof Peer==="undefined"){document.getElementById("onlineStatus").innerText="النت ضعيف - حاول تاني";return;}var code=makeCode();document.getElementById("onlineStatus").innerText="بيعمل الغرفة...";isHost=true;mySymbol="X";peer=new Peer("yf-xo-"+code);peer.on("open",function(){document.getElementById("myCode").innerText=code;document.getElementById("myCodeArea").style.display="block";document.getElementById("createArea").style.display="none";document.getElementById("onlineStatus").innerText="الغرفة اتعملت - ابعت الكود لصاحبك";});peer.on("connection",function(c){conn=c;setupConn();document.getElementById("onlineStatus").innerText="✅ صاحبك دخل! انت X وهو O";sendData({type:"start"});reset();});peer.on("error",function(){document.getElementById("onlineStatus").innerText="حصل مشكلة - دوس تاني";});}
function joinRoom(){if(typeof Peer==="undefined"){document.getElementById("onlineStatus").innerText="النت ضعيف";return;}var code=document.getElementById("joinCode").value.trim().toUpperCase();if(!code){alert("اكتب الكود الاول");return;}if(code.indexOf("YF-")!=0)code="YF-"+code;document.getElementById("onlineStatus").innerText="بيحاول يدخل...";isHost=false;mySymbol="O";peer=new Peer();peer.on("open",function(){conn=peer.connect("yf-xo-"+code);setupConn();});peer.on("error",function(){document.getElementById("onlineStatus").innerText="الكود غلط";});}
function setupConn(){conn.on("open",function(){document.getElementById("onlineStatus").innerText=isHost?"✅ صاحبك دخل!":"✅ دخلت الغرفة! انت O";reset();});conn.on("data",function(d){if(d.type=="move"){doMove(d.index,d.player);}if(d.type=="reset"){reset();}if(d.type=="start"){reset();}});conn.on("close",function(){document.getElementById("onlineStatus").innerText="صاحبك خرج";});}
function sendData(d){if(conn&&conn.open){conn.send(d);}}
function copyCode(){var t=document.getElementById("myCode").innerText;if(navigator.clipboard){navigator.clipboard.writeText(t).then(function(){alert("اتنسخ ✅");});}else{prompt("انسخ الكود:",t);}}
function leaveOnline(){if(conn){try{conn.close()}catch(e){}}if(peer){try{peer.destroy()}catch(e){}}conn=null;peer=null;document.getElementById("myCodeArea").style.display="none";document.getElementById("createArea").style.display="block";document.getElementById("onlineStatus").innerText="خرجت من الأونلاين";setMode("comp");}
function play(i){if(board[i]!=""||gameOver)return;if(mode=="comp"&&turn=="O")return;if(mode=="online"){if(!conn||!conn.open){alert("لسه مش متصل بصاحبك");return;}if(turn!=mySymbol){alert("مش دورك، استنى صاحبك");return;}doMove(i,turn);sendData({type:"move",index:i,player:turn});return;}doMove(i,turn);if(!gameOver&&mode=="comp"&&turn=="O"){document.getElementById("status").innerText="الكمبيوتر بيفكر... 🤔";setTimeout(computerMove,500);}}
function addHistory(r){var n=new Date();var t=n.getHours()+":"+(n.getMinutes()<10?"0":"")+n.getMinutes();myHist.unshift({r:r,m:mode,t:t});if(myHist.length>10)myHist=myHist.slice(0,10);try{localStorage.setItem("YF_HIST",JSON.stringify(myHist));}catch(e){}}
function doMove(i,player){if(board[i]!="")return;board[i]=player;var cells=document.getElementsByClassName("cell");cells[i].innerText=player;cells[i].className="cell "+player;var win=checkWin();if(win){gameOver=true;for(var k=0;k<win.length;k++){cells[win[k]].className+=" win";}if(player=="X"){scoreX++;document.getElementById("status").innerText="🎉 X كسب!";addHistory("فوز X");}else{scoreO++;document.getElementById("status").innerText="🎉 O كسب!";addHistory("فوز O");}speak("اللي كسب هو "+player);saveScore();updateScore();return;}if(board.indexOf("")==-1){gameOver=true;scoreD++;document.getElementById("status").innerText="😅 تعادل!";addHistory("تعادل");saveScore();updateScore();return;}turn=turn=="X"?"O":"X";if(mode=="online"){document.getElementById("status").innerText="الدور على: "+turn+(turn==mySymbol?" - دورك انت":" - دور صاحبك");}else{document.getElementById("status").innerText="الدور على: "+turn;}}
function checkWin(){var w=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];for(var i=0;i<w.length;i++){var a=w[i];if(board[a[0]]&&board[a[0]]==board[a[1]]&&board[a[0]]==board[a[2]]){return a;}}return null;}
function computerMove(){if(gameOver)return;var m=-1;if(level=="easy"){m=randomMove();}else if(level=="medium"){m=bestMove()!=-1&&Math.random()<0.7?bestMove():randomMove();}else{m=bestMove();}if(m!=-1)doMove(m,"O");}
function randomMove(){var e=[];for(var i=0;i<9;i++){if(board[i]=="")e.push(i);}if(e.length==0)return -1;return e[Math.floor(Math.random()*e.length)];}
function bestMove(){var win=findWin("O");if(win!=-1)return win;var b=findWin("X");if(b!=-1)return b;if(board[4]=="")return 4;var c=[0,2,6,8];var f=[];for(var i=0;i<c.length;i++){if(board[c[i]]=="")f.push(c[i]);}if(f.length>0)return f[Math.floor(Math.random()*f.length)];return randomMove();}
function findWin(p){var w=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];for(var i=0;i<w.length;i++){var a=w[i];var cp=0,ce=0,em=-1;for(var j=0;j<3;j++){if(board[a[j]]==p)cp++;if(board[a[j]]==""){ce++;em=a[j];}}if(cp==2&&ce==1)return em;}return -1;}
function speak(t){if(!soundOn)return;try{var s=new SpeechSynthesisUtterance(t);s.lang="ar-EG";speechSynthesis.speak(s);}catch(e){}}
function updateScore(){document.getElementById("scoreX").innerText=scoreX;document.getElementById("scoreO").innerText=scoreO;document.getElementById("scoreD").innerText=scoreD;}
function saveScore(){try{localStorage.setItem("YF_X",scoreX);localStorage.setItem("YF_O",scoreO);localStorage.setItem("YF_D",scoreD);}catch(e){}}
function reset(){board=["","","","","","","","",""];turn="X";gameOver=false;if(mode=="online"){document.getElementById("status").innerText="الدور على: X"+(mySymbol=="X"?" - دورك انت":" - دور صاحبك");}else{document.getElementById("status").innerText="الدور على: X";}var cells=document.getElementsByClassName("cell");for(var i=0;i<cells.length;i++){cells[i].innerText="";cells[i].className="cell";}if(mode=="online"&&isHost){sendData({type:"reset"});}}
function resetScore(){scoreX=0;scoreO=0;scoreD=0;saveScore();updateScore();reset();refreshAdmin();}
updateScore();
// === دخول الأدمن المخفي - 5 ضغطات على العنوان ===
var secretClicks=0;
document.getElementById("secretTitle").addEventListener("click",function(){
  secretClicks++;
  if(secretClicks>=5){ secretClicks=0; openAdmin(); }
});
</script>
</body>
</html>
