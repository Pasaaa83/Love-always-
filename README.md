<!DOCTYPE html>
<html lang="fa" dir="rtl">
...
<!-- ا<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Planner عاشقونه پارسا و روژینا</title>
<style>
:root{
  --bg:#fff5f7; --card:#ffffffdd; --ink:#3d2a2e; --rose:#ff6b8a;
  --rose-dark:#e35173; --accent:#ffd6e0; --shadow:0 10px 30px rgba(0,0,0,.08);
  --radius:18px;
}
html,body{margin:0;background:var(--bg);color:var(--ink);font-family:Vazirmatn,system-ui,-apple-system,Segoe UI,Roboto,Arial,sans-serif;}
header{position:sticky;top:0;z-index:5;background:linear-gradient(180deg,#ffe9ef,transparent);backdrop-filter:saturate(120%) blur(6px);}
.wrap{max-width:900px;margin:auto;padding:16px;}
.title{display:flex;align-items:center;gap:10px;flex-wrap:wrap;justify-content:center;text-align:center}
.title h1{font-size:22px;margin:0;font-weight:800;letter-spacing:.2px}
.title .heart{font-size:22px; animation: beat 1.4s infinite;}
@keyframes beat{0%,100%{transform:scale(1)} 20%{transform:scale(1.18)}}
.card{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);padding:14px}
.hero{display:flex;flex-direction:column;align-items:center;gap:10px}
.hero img{width:100%;max-height:280px;object-fit:cover;border-radius:16px;box-shadow:var(--shadow)}
.hero .controls{display:flex;gap:8px;flex-wrap:wrap;justify-content:center}
.btn{appearance:none;border:0;border-radius:999px;background:var(--rose);color:#fff;padding:10px 14px;font-weight:700;box-shadow:var(--shadow);cursor:pointer}
.btn.secondary{background:#fff;color:var(--ink);border:1px solid #eee}
.grid{display:grid;grid-template-columns:1fr;gap:14px;margin-top:14px}
@media(min-width:640px){.grid{grid-template-columns:1fr 1fr}}
.day{position:relative}
.day h3{margin:.2rem 0 .6rem 0;display:flex;align-items:center;gap:8px;font-size:18px}
.chip{background:var(--accent);border-radius:999px;padding:2px 8px;font-size:12px}
.task{display:flex;align-items:flex-start;gap:10px;padding:10px;border-radius:12px;border:1px solid #f3e1e6;background:#fff}
.time{font-weight:800;min-width:64px}
.task label{flex:1;cursor:pointer;}
.task input[type="checkbox"]{width:20px;height:20px;margin-top:2px}
footer{margin:18px auto 60px}
.poem{background:linear-gradient(180deg,#ffffff,#fff0f4);border:1px dashed #ffd0dc;padding:16px;border-radius:16px;text-align:center}
.fab{position:fixed;inset:auto 16px 16px auto;background:var(--rose);color:#fff;border:0;border-radius:50%;width:56px;height:56px;font-size:22px;box-shadow:0 10px 20px rgba(255,89,129,.35);cursor:pointer}
.hint{font-size:12px;opacity:.8;margin-top:4px;text-align:center}
.stickers{display:flex;gap:6px;flex-wrap:wrap;justify-content:center;margin-top:8px}
.st{filter:drop-shadow(0 4px 10px rgba(0,0,0,.12))}
</style>
</head>
<body>
<header>
<div class="wrap">
  <div class="title">
    <span class="heart">💞</span>
    <h1>بین خانومی و آقایی: پارسا و روژینا جون</h1>
    <span class="heart">💞</span>
  </div>
  <div class="hint" id="sinceText">—</div>
</div>
</header>
<main class="wrap">
<section class="card hero">
  <img id="heroImg" alt="زوج انیمه عاشق" src="" />
  <div class="controls">
    <label class="btn secondary" for="imgPicker">انتخاب/تغییر عکس</label>
    <input id="imgPicker" type="file" accept="image/*" style="display:none" />
    <button class="btn" id="setStart">ثبت تاریخ شروع 💗</button>
  </div>
  <div class="hint">عکس انتخابی و تیک‌ها داخل گوشی‌تون ذخیره می‌شن.</div>
  <div class="stickers">
    <span class="st">❤️</span><span class="st">💖</span><span class="st">✨</span><span class="st">🦋</span><span class="st">💗</span>
  </div>
</section>
<section class="grid" id="weekGrid"></section>
<footer class="wrap">
  <div class="poem" id="poem">
    میان تپشِ قلب‌هامان، راهی به هم داریم؛<br/>
    پارسا و روژینا، دو پرندهٔ یک آسمانیم؛<br/>
    هر روز که می‌گذرد، عشق‌مان جوان‌تر می‌شود؛<br/>
    تا ابد، دست در دست، کنارِ هم می‌مانیم. 💞
  </div>
</footer>
</main>
<button class="fab" id="resetBtn" title="ریست هفتگی">↻</button>
<script>
const storage={
  get:(k,d=null)=>{try{const v=localStorage.getItem(k);return v?JSON.parse(v):d}catch{return d}},
  set:(k,v)=>localStorage.setItem(k,JSON.stringify(v)),
  del:k=>localStorage.removeItem(k)
};

function getWeekIndex(d=new Date()){
  const epoch = new Date('2025-01-04T00:00:00');
  const diffDays = Math.floor((new Date(d.toDateString())-epoch)/(1000*60*60*24));
  return Math.floor(diffDays/7);
}

function ensureWeeklyReset(){
  const cur = getWeekIndex();
  const last = storage.get('lastWeek');
  if(last===null || last!==cur){
    Object.keys(localStorage).forEach(k=>{ if(k.startsWith('tick:')) localStorage.removeItem(k); });
    storage.set('lastWeek',cur);
  }
}

/* عکس هدر */
const heroImg=document.getElementById('heroImg');
const imgPicker=document.getElementById('imgPicker');
function loadSavedImage(){
  const data=storage.get('hero');
  if(data){ heroImg.src=data; }
  else{
    heroImg.src='data:image/svg+xml;utf8,'+encodeURIComponent(`
      <svg xmlns="http://www.w3.org/2000/svg" width="1200" height="700">
        <defs>
          <linearGradient id="g" x1="0" y1="0" x2="1" y2="1">
            <stop offset="0" stop-color="#ffe3ec"/>
            <stop offset="1" stop-color="#fff"/>
          </linearGradient>
        </defs>
        <rect width="100%" height="100%" fill="url(#g)"/>
        <text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" font-family="sans-serif" font-size="36" fill="#e35173">عکس عاشقونه‌تون رو اضافه کنید 💖</text>
      </svg>`);
  }
}
imgPicker.addEventListener('change',e=>{
  const file = e.target.files?.[0];
  if(!file) return;
  const reader=new FileReader();
  reader.onload=()=>{ storage.set('hero',reader.result); heroImg.src=reader.result; };
  reader.readAsDataURL(file);
});

/* تاریخ شروع */
const sinceText=document.getElementById('sinceText');
const setStartBtn=document.getElementById('setStart');
function renderSince(){
  const start=storage.get('startDate');
  if(!start){
    sinceText.textContent='روی «ثبت تاریخ شروع 💗» بزن تا روزشمارت فعال شود.';
    return;
  }
  const s=new Date(start.toString().split('T')[0]);
  const now=new Date();
  const days=Math.floor((now-s)/(1000*60*60*24));
  sinceText.textContent = `از ${s.toLocaleDateString('fa-IR')} تا امروز، ${days.toLocaleString('fa-IR')} روز کنار هم این برنامه را داریم 💞`;
}
setStartBtn.addEventListener('click',()=>{
  const pick=prompt('تاریخ شروع را وارد کن (مثال: 1403/06/01 یا 2024-08-20):');
  if(!pick) return;
  let d;
  if(pick.includes('-')) d=new Date(pick);
  else if(pick.includes('/')) d=new Date(pick.replace(/\//g,'-'));
  if(isNaN(d)) return alert('تاریخ نامعتبر بود.');
  storage.set('startDate',d);
  renderSince();
});

/* برنامه روزانه */
const daysFa=['شنبه','یکشنبه','دوشنبه','سه‌شنبه','چهارشنبه','پنجشنبه','جمعه'];
function scheduleFor(dayIndex){
  if(dayIndex===6) return [['تمام روز','عشق و حال دوتایی و لذت بردن از همدیگه 💖']];
  const isEvenDay=[0,2,4].includes(dayIndex);
  const baseMorning=['10','بیدار شدن، بوسیدن همدیگه، از روی هم بلند شدن؛ خانومی با عشق صبحانه بده 🥐💗'];
  let mid1, mid2, after5, late, cuddle;
  if(isEvenDay){
    mid1=['11–15','شروع عشق و عاشقی با درس: پارسا ۱ ساعت درس/نیم ساعت استراحت 🔁، روژینا بی‌وقفه تا ۱۵ درس 📚'];
    after5=['15–17','عشق و حال دوتایی؛ هر کاری دلمان بخواهد ولی باهم 💞'];
    mid2=['17–22','ادامه درس و عشق: پارسا تا ۲۱ به همان روش 🔁، روژینا تا ۲۱ پیوسته درس 📖'];
    late=['21–24','وقت آزاد دوتایی؛ اما برای هم وقت می‌گذاریم 🌙'];
  }else{
    mid1=['11–15','درس: پارسا ۱ ساعت درس/نیم ساعت استراحت 🔁، روژینا پیوسته تا ۱۵ درس 📚'];
    after5=['15–17','عشق و عاشقی دوتایی باهم 💞'];
    mid2=['17–22','کلاس‌های خانومی؛ پارسا تنهاست و مشغول کارهای خودش می‌شود 🎧'];
    late=['22–24','وقتی برگشت: دوباره باهم درس می‌خوانیم تا نیمه‌شب 📘👫'];
  }
  cuddle=['24–01','لحظهٔ صمیمیت و نزدیکی 💗 سپس خواب در آغوش هم 😴'];
  return [baseMorning, mid1, after5, mid2, late, cuddle];
}

function dayChip(dayIndex){
  if(dayIndex===6) return '<span class="chip">جمعهٔ عشق</span>';
  const tag=[0,2,4].includes(dayIndex)? 'روزِ زوج':'روزِ فرد';
  return `<span class="chip">${tag}</span>`;
}

function tickKey(dayIndex, idx){
  return `tick:${getWeekIndex()}:${dayIndex}:${idx}`;
}

function renderWeek(){
  const grid=document.getElementById('weekGrid');
  grid.innerHTML='';
  for(let d=0; d<7; d++){
    const card=document.createElement('div');
    card.className='card day';
    card.innerHTML=`<h3>${daysFa[d]} ${dayChip(d)}</h3>`;
    const list=document.createElement('div');
    const tasks=scheduleFor(d);
    tasks.forEach((t,i)=>{
      const row=document.createElement('div');
      row.className='task';
      const key = tickKey(d,i);
      row.innerHTML=`
        <div class="time">${t[0]}</div>
        <label for="${key}">${t[1]}</label>
        <input type="checkbox" id="${key}" />
      `;
      const cb=row.querySelector('input');
      cb.checked=!!storage.get(key,false);
      cb.addEventListener('change',()=>storage.set(key,cb.checked));
      list.appendChild(row);
    });
    card.appendChild(list);
    grid.appendChild(card);
  }
}

/* ریست دستی */
document.getElementById('resetBtn').addEventListener('click',()=>{
  if(confirm('تیک‌های این هفته پاک شود؟')){
    Object.keys(localStorage).forEach(k=>{ if(k.startsWith('tick:')) localStorage.removeItem(k); });
    renderWeek();
  }
});

ensureWeeklyReset();
renderWeek();
loadSavedImage();
renderSince();
</script>
</body>
</html>دامه همان کدی که قبلاً برایت فرستادم اینجا قرار بده -->
...
</html>
