<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>TradingBot Premium</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script src="https://paydunya.com/assets/js/paydunya.checkout.min.js"></script>
<style>
  /* Reset & body */
  * { box-sizing: border-box; }
  body { margin:0; font-family: 'Segoe UI', sans-serif; background: linear-gradient(135deg,#0f172a,#1e293b); display:flex; justify-content:center; align-items:center; height:100vh; }

  /* Container large et responsive */
  .container { width:90%; max-width:700px; perspective:1500px; }

  /* Card 3D large */
  .card { width:100%; min-height:500px; background:#111827; border-radius:20px; box-shadow: 0 30px 50px rgba(0,0,0,0.7); transform-style: preserve-3d; transition: transform 1s; position:relative; }
  .card.flip { transform: rotateY(180deg); }

  /* Front & Back */
  .side { position:absolute; width:100%; height:100%; backface-visibility:hidden; padding:40px; border-radius:20px; color:#fff; display:flex; flex-direction:column; justify-content:center; align-items:center; }
  .front { }
  .back { transform: rotateY(180deg); }

  /* Titres et inputs */
  h2 { margin:20px 0; font-size:28px; }
  input { width:100%; padding:15px; margin:10px 0; border-radius:10px; border:none; font-size:16px; background:#1e293b; color:#fff; }
  button { width:100%; padding:15px; margin:10px 0; border-radius:10px; border:none; font-size:18px; background:#22c55e; color:#fff; cursor:pointer; transition:0.3s; }
  button:hover { background:#16a34a; }
  .toggle { margin-top:15px; font-size:16px; cursor:pointer; color:#38bdf8; text-decoration:underline; }

  /* Paiement box */
  #paiementBox { margin-top:20px; padding:20px; border-radius:15px; background:#1e293b; width:100%; text-align:center; display:none; }

  /* Responsive */
  @media(max-width:480px){ .card { padding:20px; min-height:450px; } h2{font-size:22px;} input,button{font-size:16px;} }
</style>
</head>
<body>

<div class="container">
  <div class="card" id="card">
    <!-- Front: Inscription -->
    <div class="side front">
      <h2>Créer un compte</h2>
      <input id="emailSignup" type="email" placeholder="Adresse email" required>
      <input id="passwordSignup" type="password" placeholder="Mot de passe" required>
      <button onclick="signup()">S’inscrire</button>
      <div class="toggle" onclick="flipCard()">Déjà inscrit ? Connectez-vous</div>
    </div>

    <!-- Back: Connexion -->
    <div class="side back">
      <h2>Connexion</h2>
      <input id="emailLogin" type="email" placeholder="Adresse email" required>
      <input id="passwordLogin" type="password" placeholder="Mot de passe" required>
      <button onclick="login()">Se connecter</button>
      <div class="toggle" onclick="flipCard()">Pas de compte ? S’inscrire</div>

      <!-- Paiement -->
      <div id="paiementBox">
        <h3>Abonnement Premium</h3>
        <p>5000 FCFA / mois</p>
        <button onclick="payer()">Payer avec PayDunya</button>
      </div>
    </div>
  </div>
</div>

<script>
  // Supabase config
  const supabaseUrl = "https://kiayqqfnlsgykkdmjlxj.supabase.co";
  const supabaseKey = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImtpYXlxcWZubHNneWtrZG1qbHhqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTcxODg2MDcsImV4cCI6MjA3Mjc2NDYwN30.WJnE3D6w2ev06AfUTGyMCkwlvzhD0xn8lgDIt-0krNI";
  const supabase = supabase.createClient(supabaseUrl, supabaseKey);
  let currentUser = null;

  function flipCard(){ document.getElementById("card").classList.toggle("flip"); }

  async function signup(){
    const email = document.getElementById("emailSignup").value;
    const password = document.getElementById("passwordSignup").value;
    if(!email||!password){ alert("⚠️ Remplis tous les champs"); return; }
    let {error} = await supabase.auth.signUp({email,password});
    if(error) alert("❌ "+error.message);
    else alert("✅ Compte créé ! Vérifie ton email.");
  }

  async function login(){
    const email = document.getElementById("emailLogin").value;
    const password = document.getElementById("passwordLogin").value;
    if(!email||!password){ alert("⚠️ Remplis tous les champs"); return; }
    let {data,error} = await supabase.auth.signInWithPassword({email,password});
    if(error) alert("❌ "+error.message);
    else{
      currentUser = data.user;
      document.getElementById("paiementBox").style.display="block";
      alert("✅ Bienvenue "+currentUser.email);
    }
  }

  function payer(){
    if(!currentUser){ alert("⚠️ Connectez-vous d'abord !"); return; }
    const refPaiement = "txn_"+Date.now();
    PayDunya.setup({
      public_key:"live_public_EOWGkc2jnlsjlUIKCtjd220PRiD",
      private_key:"live_private_wMW946zVj40qHU8vcMBs4kEZm8p",
      token:"uGZeB2zxgxNyKsMN4rcr",
      mode:"live"
    });
    PayDunya.checkout({
      amount:5000,
      currency:"XOF",
      description:"Abonnement 1 mois TradingBot"
    }, async function(response){
      if(response.status==="completed"){
        alert("✅ Paiement réussi !");
        await supabase.from("paiements").insert([{user_id:currentUser.id,montant:5000,devise:"XOF",methode:"PayDunya",reference:refPaiement,statut:"valide",expire_le:new Date(Date.now()+30*24*60*60*1000)}]);
        await supabase.from("profils").upsert([{id:currentUser.id,abonnement:"premium",abonnement_expire:new Date(Date.now()+30*24*60*60*1000)}]);
      }else{
        alert("❌ Paiement annulé ou échoué.");
        await supabase.from("paiements").insert([{user_id:currentUser.id,montant:5000,devise:"XOF",methode:"PayDunya",reference:refPaiement,statut:"echoue"}]);
      }
    });
  }
</script>

</body>
</html>
