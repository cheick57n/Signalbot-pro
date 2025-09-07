
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Hassim-Nature 🌿 — Diagnostic Premium</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
<style>
/* --- RESET --- */
*{margin:0;padding:0;box-sizing:border-box;}
body{font-family:'Poppins',sans-serif;background:#0b1a0b;color:#fff;line-height:1.5;}
a{text-decoration:none;color:inherit;}

/* --- HEADER --- */
header{background:linear-gradient(90deg,#2eb82e,#1a4d1a);padding:20px 30px;display:flex;justify-content:space-between;align-items:center;box-shadow:0 6px 20px rgba(0,0,0,0.4);}
header h1{font-size:28px;font-weight:700;}
nav a{margin-left:20px;font-weight:600;transition:0.3s;}
nav a:hover{opacity:0.8;}

/* --- LAYOUT --- */
.container{max-width:1300px;margin:30px auto;display:grid;grid-template-columns:2fr 1fr;gap:30px;padding:0 20px;}
@media(max-width:1000px){.container{grid-template-columns:1fr;}}

/* --- PANEL --- */
.panel{background:rgba(0,0,0,0.6);border-radius:24px;padding:30px;box-shadow:0 10px 40px rgba(0,0,0,0.5);backdrop-filter:blur(8px);transition:0.4s;}
.panel h2{color:#adebad;text-align:center;margin-bottom:20px;font-size:24px;}

/* --- INPUT --- */
input, select{width:100%;padding:14px;margin-bottom:16px;border-radius:12px;border:none;background:rgba(255,255,255,0.1);color:#fff;font-size:15px;}
input::placeholder, select::placeholder{color:rgba(255,255,255,0.7);}

/* --- SYMPTOMS GRID --- */
.symptoms-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:15px;}
.symptom{background:rgba(255,255,255,0.05);padding:18px;border-radius:20px;text-align:center;cursor:pointer;transition:0.4s;transform-style:preserve-3d;box-shadow:0 4px 12px rgba(0,0,0,0.3);font-weight:600;font-size:14px;}
.symptom:hover{transform:rotateX(5deg) rotateY(10deg) scale(1.08);box-shadow:0 12px 36px rgba(0,0,0,0.6);}
.symptom.selected{background:#2eb82e;color:#fff;box-shadow:0 14px 36px rgba(46,184,46,0.7);}

/* --- BUTTONS --- */
.btn{padding:14px 22px;border-radius:14px;font-weight:700;cursor:pointer;transition:0.3s;display:inline-block;text-align:center;}
.btn.primary{background:#2eb82e;color:#fff;box-shadow:0 6px 20px rgba(46,184,46,0.5);}
.btn.primary:hover{transform:scale(1.05);}
.btn.secondary{background:rgba(255,255,255,0.1);color:#fff;border:1px solid #adebad;}
.btn.secondary:hover{transform:scale(1.03);}

/* --- RESULT --- */
.result{margin-top:18px;background:rgba(46,184,46,0.12);padding:18px;border-radius:18px;color:#fff;font-size:15px;white-space:pre-line;animation:fadeIn 0.8s;}

/* --- PRODUCTS --- */
.products .card{display:flex;gap:14px;align-items:center;background:rgba(255,255,255,0.05);padding:16px;border-radius:20px;transition:0.3s;transform-style:preserve-3d;}
.products .card:hover{transform:rotateY(5deg) scale(1.04);box-shadow:0 14px 36px rgba(0,0,0,0.7);}
.prod-info h4{margin:0;color:#adebad;}
.prod-info p{margin:6px 0 0 0;font-size:14px;color:#cce6cc;}
.order-row{display:flex;gap:10px;margin-top:8px;}
.order-input{flex:1;padding:8px;border-radius:12px;border:none;background:rgba(255,255,255,0.1);color:#fff;}

/* --- FLOATING CONTACT --- */
#wa-float{position:fixed;bottom:30px;right:30px;background:#2eb82e;padding:18px 22px;border-radius:50px;font-weight:700;box-shadow:0 8px 28px rgba(46,184,46,0.5);z-index:999;}
#wa-float:hover{transform:scale(1.08);transition:0.2s;}

/* --- ANIMATIONS --- */
@keyframes fadeIn{0%{opacity:0;}100%{opacity:1;}}
</style>
</head>
<body>

<header>
  <h1>Hassim-Nature 🌿</h1>
  <nav>
    <a href="#diagnostic">Diagnostic</a>
    <a href="#produits">Produits</a>
    <a href="#contact">Contact</a>
  </nav>
</header>

<div class="container">
  <!-- DIAGNOSTIC -->
  <section>
    <div class="panel" id="diagnostic">
      <h2>Diagnostic intelligent</h2>
      <input id="nom" type="text" placeholder="Nom complet">
      <input id="age" type="number" placeholder="Âge">
      <select id="sexe">
        <option value="">Sexe</option>
        <option>Homme</option>
        <option>Femme</option>
      </select>

      <h3>Sélectionnez vos symptômes :</h3>
      <div class="symptoms-grid" id="symptomsGrid"></div>

      <div style="margin-top:20px;">
        <button class="btn primary" id="btnAnalyze">Analyser</button>
        <button class="btn secondary" id="btnReset">Réinitialiser</button>
      </div>

      <div class="result" id="resultBox" style="display:none;"></div>
    </div>
  </section>

  <!-- PRODUITS & CONTACT -->
  <aside>
    <div class="panel" id="produits">
      <h2>Produits recommandés</h2>
      <div class="products" id="productsList"></div>
    </div>

    <div class="panel" id="contact">
      <h2>Contactez-nous</h2>
      <p>WhatsApp : +226 5720 2363</p>
      <a id="waLink" href="#" target="_blank" class="btn primary">Envoyer un message</a>
    </div>
  </aside>
</div>

<a href="https://wa.me/22657202363" id="wa-float" target="_blank">WhatsApp</a>

<script>
// --- Données ---
const WHATSAPP_NUMBER='22657202363';
const ALL_SYMPTOMS=["Fièvre","Toux","Fatigue","Maux de tête","Maux de gorge","Perte de goût","Courbatures","Nausée","Frissons","Faiblesse sexuelle","Ulcère","Hémorroïdes","Hypertension","Diabète","Essoufflement","Vertiges","Diarrhée","Constipation","Douleurs abdominales","Sueur nocturne"];
const DISEASES=[
{name:"Paludisme",symptoms:["Fièvre","Frissons","Maux de tête","Fatigue"],advice:"Consultez un médecin rapidement."},
{name:"Grippe",symptoms:["Fièvre","Toux","Maux de gorge","Courbatures","Fatigue"],advice:"Reposez-vous et hydratez-vous."},
{name:"COVID-19",symptoms:["Fièvre","Toux","Perte de goût","Fatigue","Essoufflement"],advice:"Faites un test COVID et isolez-vous."},
{name:"Faiblesse sexuelle",symptoms:["Faiblesse sexuelle"],advice:"Solutions naturelles disponibles."},
{name:"Ulcère",symptoms:["Ulcère","Douleurs abdominales","Nausée"],advice:"Contrôlez alimentation; consultez un médecin."},
{name:"Hémorroïdes",symptoms:["Hémorroïdes","Douleurs abdominales","Constipation"],advice:"Produits topiques et régulation du transit."}];
const PRODUCTS=[
{id:'homme-fort',name:"L’Homme Fort",price:'40 000 F',short:"Renforce vitalité masculine",tag:"Faiblesse sexuelle"},
{id:'decoction-ulcere',name:"Décoction Anti-Ulcère",price:'25 000 F',short:"Soutien naturel pour l'ulcère",tag:"Ulcère"},
{id:'tisane-laafi',name:"Tisane Nature",price:'12 000 F',short:"Renforce immunité",tag:"Immunité"},
{id:'poudre-hemoroide',name:"Poudre Anti-Hémorroïdes",price:'15 000 F',short:"Soulagement naturel",tag:"Hémorroïdes"}];

// --- Afficher symptômes ---
const symptomsGrid=document.getElementById('symptomsGrid');
ALL_SYMPTOMS.forEach(s=>{
  const div=document.createElement('div');
  div.className='symptom'; div.innerText=s;
  div.addEventListener('click',()=>{div.classList.toggle('selected');});
  symptomsGrid.appendChild(div);
});

// --- Produits ---
const productsList=document.getElementById('productsList');
PRODUCTS.forEach(p=>{
  const card=document.createElement('div'); card.className='card';
  card.innerHTML=`<div class="prod-info">
    <h4>${p.name}</h4>
    <p>${p.short} • <strong>${p.price}</strong></p>
    <div class="order-row">
      <input class="order-input" type="number" min="1" value="1" id="qty-${p.id}">
      <input class="order-input" type="text" placeholder="Message/Adresse" id="msg-${p.id}">
      <button class="btn primary" onclick="orderWhatsApp('${p.id}')">Commander</button>
    </div>
  </div>`;
  productsList.appendChild(card);
});

// --- Diagnostic ---
const btnAnalyze=document.getElementById('btnAnalyze');
const btnReset=document.getElementById('btnReset');
const resultBox=document.getElementById('resultBox');

btnAnalyze.addEventListener('click',()=>{
  const nom=document.getElementById('nom').value.trim();
  const age=document.getElementById('age').value.trim();
  const sexe=document.getElementById('sexe').value;
  const selected=Array.from(document.querySelectorAll('.symptom.selected')).map(s=>s.innerText);
  if(!nom||!age||!sexe||selected.length===0){alert("Veuillez remplir tous les champs et sélectionner au moins un symptôme."); return;}
  
  let matchedDiseases=DISEASES.filter(d=>d.symptoms.some(sym=>selected.includes(sym)));

  if(matchedDiseases.length===0){
    resultBox.style.display='block';
    resultBox.innerText=`Bonjour ${nom}, aucun diagnostic précis trouvé. Consultez un professionnel de santé.`;
    return;
  }

  let message=`Bonjour ${nom}, diagnostic :\n\n`;
  matchedDiseases.forEach(d=>{
    message+=`• ${d.name} : ${d.advice}\n`;
    const prod=PRODUCTS.find(p=>p.tag.toLowerCase()===d.name.toLowerCase()||selected.some(s=>p.tag.toLowerCase()===s.toLowerCase()));
    if(prod){message+=`  Produit : ${prod.name} (${prod.price})\n`;}
  });

  resultBox.style.display='block';
  resultBox.innerHTML = message+`<br><a href="https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(message+'\nJe souhaite commander.')}" target="_blank" class="btn primary" style="margin-top:10px;">Commander via WhatsApp</a>`;
});

btnReset.addEventListener('click',()=>{
  document.getElementById('nom').value='';
  document.getElementById('age').value='';
  document.getElementById('sexe').value='';
  document.querySelectorAll('.symptom.selected').forEach(s=>s.classList.remove('selected'));
  resultBox.style.display='none';
});

function orderWhatsApp(id){
  const qty=document.getElementById(`qty-${id}`).value||1;
  const msg=document.getElementById(`msg-${id}`).value||"";
  const prod=PRODUCTS.find(p=>p.id===id);
  const text=`Bonjour Hassim-Nature, je souhaite commander :\nProduit : ${prod.name}\nQuantité : ${qty}\nMessage : ${msg}`;
  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(text)}`,'_blank');
}
document.getElementById('waLink').href=`https://wa.me/${WHATSAPP_NUMBER}`;
</script>
</body>
</html>
