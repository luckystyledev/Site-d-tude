<html lang="fr">
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta charset="UTF-8">
  <title>📃 Mon Mini Logiciel</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      /*background-image: url("moni3.png"); background-size: cover;*/
      background-color: skyblue;
      min-height: 100vh;
    }
    h1 {
      text-align: center;
    }
    .container {
      max-width: 600px;
      margin: auto;
      padding: 20px;
      background: linear-gradient(30deg, skyblue, pink);
      border: 5px groove whitesmoke;
      box-shadow: 0 4px 50px black;
      border-radius: 10px;
    }
    input, button, select {
      padding: 10px;
      margin: 5px 0;
      border: 1px solid #ddd;
      border-radius: 5px;
      font-size: 14px;
      box-sizing: border-box;
    }

    /* placer selectionnez et selectionnez2 côte à côte (respecte le positionnement d'origine) */
    .select-row {
      display: flex;
      gap: 10px;
      margin-bottom: 8px;
    }
    #selectionnez { width: 50%; }
    #selectionnez2 { width: 50%; }

    /* inputs */
    #input1,#input2 { width: 100%; display: block; }

    button {
      background: #4CAF50;
      color: white;
      cursor: pointer;
    }
    button:hover {
      background: #45a049;
    }
    #reponse {
      margin-top: 20px;
      padding: 10px;
      background: #f0f0f0;
      border-radius: 5px;
      overflow: auto;
      min-height: 50px;
      overflow: auto;
    }

    /* petits styles utilitaires */
    .lieu {
      display: inline;
    }
    #imp {
      height: 37px;
      border-radius: 10px;
      padding: 10px;
      width: 25%;
    }
    #i_d {
      height: 37px;
      border-radius: 10px;
      padding: 6px;
      width: 40%;
      display: inline-flex;
      border: 1px solid black;
    }
    .d_oa{
      width: 50%;
      height: 10px;
      display: inline-block;
    }
    #i_oa{
      padding: 3px;
      width: 100%;
    }

    /* Style spécifique pour les rappels */
    .rappel-card {
      background: white;
      border-left: 5px solid #4CAF50;
      padding: 10px;
      margin: 10px 0;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.12);
      overflow: auto;
    }
    .rappel-card h4 { margin: 0; color: #333; }
    .rappel-card p { margin: 6px 0 0; color: #555; }

    /* Style spécifique pour les infos */
    .info-card {
      background: white;
      border-left: 5px solid dodgerblue;
      padding: 10px;
      margin: 10px 0;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.12);
      overflow: auto;
    }
    .info-card h4 { margin: 0; color: #222; }
    .info-card p { margin: 6px 0 0; color: #444; }

    /* Style spécifique pour les tâches */
    .tache-card {
      background: white;
      border-left: 5px solid #FF9800; /* orange pour différencier */
      padding: 10px;
      margin: 10px 0;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.12);
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 10px;
      overflow: auto;
    }
    .tache-left {
      flex: 1;
    }
    .tache-meta {
      font-size: 12px;
      color: #666;
      text-align: right;
      min-width: 120px;
      overflow: auto;
    }
    .small-btn {
      padding: 6px 8px;
      border-radius: 6px;
      background: #e74c3c;
      color: white;
      border: none;
      cursor: pointer;
      font-size: 12px;
    }
    .small-btn:hover { opacity: 0.9; }

    /* message vide */
    .empty { text-align: center; color: #888; font-style: italic; padding: 8px; }
  </style>
</head>
<body>
  <div id="cont" class="container">
    <h1 id="titre">📃 Gestion de informations</h1>

    <!-- Sélecteurs côte à côte -->
    <div class="select-row">
      <select id="selectionnez">
        <option value="ajouter">Ajouter</option>
        <option value="recherche">Rechercher</option>
        <option value="retirer">Retirer</option>
      </select>

      <select id="selectionnez2">
        <option id="inf" value="info">info</option>
        <option id="tache1" value="taches">taches</option>
        <option value="rappel">rappel</option>
      </select>
    </div>

    <input type="text" id="input1" placeholder="ID (clé)">
    <div class="lieu" id="lieu"></div>
    <input type="text" id="input2" placeholder="Phrase (valeur)">

    <button style="display: inline;" onclick="execution()">Exécuter</button>
    <button style="display: inline;" onclick="affiche()">Afficher toute la liste</button> <div id="d_oa" class="d_oa"></div>

    
    <div id="reponse"></div>
  </div>

  <script>
    /* ---------------------------
       Utilitaires pour gérer éléments dynamiques (imp, i_d, i_oa)
       --------------------------- */
    function removeDynamicElements() {
      const imp = document.getElementById('imp');
      if (imp) imp.remove();
      const i_d = document.getElementById('i_d');
      if (i_d) i_d.remove();
      const i_oa = document.getElementById('i_oa');
      if (i_oa) i_oa.remove();
    }

    function createTacheControls() {
      // n'ajoute que si pas déjà présent
      if (!document.getElementById('imp')) {
        const s_i = document.createElement("select");
        s_i.style.display = "inline";
        s_i.id = "imp";
        const opts = [
          {text: "faible", value: "faible"},
          {text: "moyenne", value: "moyenne"},
          {text: "intermediaire", value: "intermediaire"},
          {text: "primordial", value: "primordial"}
        ];
        opts.forEach(o => {
          const el = document.createElement("option");
          el.textContent = o.text;
          el.value = o.value;
          s_i.appendChild(el);
        });
        // ajoute avant le champ input2 (dans la div lieu)
        const lieu = document.getElementById('lieu');
        lieu.appendChild(s_i);
      }

      if (!document.getElementById('i_d')) {
        const m_d_i = document.createElement('input');
        m_d_i.id = "i_d";
        m_d_i.type = "date";
        m_d_i.style.marginLeft = "8px";
        m_d_i.style.display = "inline-block";
        m_d_i.style.display = "inline-flex";
        m_d_i.style.flexDirection = "row";
        m_d_i.style.padding = "0px";
        m_d_i.style.width = "20%";
        document.getElementById('lieu').appendChild(m_d_i);
      }

      // creer select d'option ADT/ADTA/ADTH/ADTD si absent
      if (!document.getElementById('i_oa')) {
        const d_a = document.getElementById('d_oa');
        const s_oa = document.createElement('select');
        s_oa.id = "i_oa";
       //s_oa.style.display = "block";
        s_oa.style.width="100px"
        s_oa.style.height="35px"
        s_oa.style.borderRadius = "5px";
        s_oa.style.padding="5px";
        s_oa.style.background = " #4CAF50";
        s_oa.style.color = "whitesmoke";
        //s_oa.style.marginTop = "px";
        const options = [
          {text: "ADT", value: "ADT"},
          {text: "ADTA", value: "ADTA"},
          {text: "ADTH", value: "ADTH"},
          {text: "ADTD", value: "ADTD"}
        ];
        options.forEach(o => {
          const el = document.createElement('option');
          el.textContent = o.text;
          el.value = o.value;
          s_oa.appendChild(el);
        });
        d_a.appendChild(s_oa);
      }
    }

    // Eviter d'empiler les controles si on clique plusieurs fois sur "taches"
    document.getElementById('selectionnez2').addEventListener("change", function(){
      const valeur_op = this.value;
      if (valeur_op === "taches") {
        document.getElementById("titre").textContent = "📃 Gestion des tâches";
        let c_i = document.getElementById('input1')
        c_i.style.display = 'inline';
        c_i.style.width = '50%'
        createTacheControls();
        afficherTaches(); // affichage direct
      } else if (valeur_op === "info") {
        document.getElementById("titre").textContent = "📃 Gestion de informations";
        let c_i = document.getElementById('input1')
        c_i.style.width = '100%'
        // supprimer controles spécifiques aux taches
        removeDynamicElements();
        const reponses = document.getElementById('reponse');
        reponses.innerHTML = "";
      } else if (valeur_op === "rappel") {
        let c_i = document.getElementById('input1')
        c_i.style.width = '100%'
        document.getElementById("titre").textContent = "⏰ Gestion des rappels";
        removeDynamicElements();
        afficherRappels();
      }
    });

    /* ---------------------------
       Fonctions d'exécution (ajout / recherche / retirer)
       --------------------------- */
    function execution() {
      const selection = document.getElementById('selectionnez').value;
      const selection2 = document.getElementById('selectionnez2').value;
      const id = document.getElementById('input1').value.trim();
      const phrase = document.getElementById('input2').value.trim();
      const reponses = document.getElementById('reponse');

      // date & importance si présents
      const ma_date_el = document.getElementById('i_d');
      const date_valeur = ma_date_el ? ma_date_el.value : "";
      let s_im_el = document.getElementById('imp');
      const s_im = s_im_el ? s_im_el.value : "";

      // INFO
      if (selection2 === "info") {
        let list = JSON.parse(localStorage.getItem('ma_liste3')) || {};
        if (selection === "ajouter") {
          if (!id || !phrase) {
            reponses.innerHTML = "❌ Veuillez remplir ID et Phrase.";
            return;
          }
          list[id] = phrase;
          localStorage.setItem('ma_liste3', JSON.stringify(list));
          afficherInfos();
          // message bref
          reponses.innerHTML = "✅ Phrase enregistrée avec l'id: <b>" + id + "</b>";
          return;
        }
        else if (selection === "recherche") {
          if (list[id]) {
            reponses.innerHTML = `
              <div class="info-card">
                <h4>🔎 Résultat pour <b>${id}</b></h4>
                <p>${list[id]}</p>
              </div>
            `;
          } else {
            reponses.innerHTML = "❌ Aucun résultat trouvé pour l'id: " + id;
          }
          return;
        }
        else if (selection === "retirer") {
          if (list[id]) {
            delete list[id];
            localStorage.setItem('ma_liste3', JSON.stringify(list));
            afficherInfos();
            reponses.innerHTML = "🗑️ Phrase supprimée pour l'id: " + id;
          } else {
            reponses.innerHTML = "❌ Impossible de supprimer, l'id n'existe pas.";
          }
          return;
        }
      }

      // TACHES
      if (selection2 === "taches") {
        let taches_base = JSON.parse(localStorage.getItem("base_taches")) || [];
        if (selection === "ajouter") {
          if (!id || !phrase || !date_valeur) {
            reponses.textContent = "⚠️ Remplis ID, Phrase et Date pour ajouter la tâche.";
            return;
          }
          taches_base.push({importance: s_im, nom: id, description: phrase, date: date_valeur});
          localStorage.setItem("base_taches", JSON.stringify(taches_base));
          afficherTaches();
          reponses.textContent = "✅ La tâche " + id + " est enregistrée.";
          // nettoie les champs
          document.getElementById('input1').value = "";
          document.getElementById('input2').value = "";
          return;
        }
        else if (selection === "recherche") {
          // recherche par nom
          const resultat = taches_base.filter(t => t.nom === id);
          if (resultat.length === 0) {
            reponses.textContent = "🔎 Aucun résultat pour la tâche: " + id;
          } else {
            // afficher en cartes
            reponses.innerHTML = resultat.map(t => `
              <div class="tache-card">
                <div class="tache-left">
                  <h4>📝 ${t.nom}</h4>
                  <p>${t.description}</p>
                </div>
                <div class="tache-meta">
                  <div>${t.importance}</div>
                  <div>${t.date}</div>
                </div>
              </div>
            `).join("");
          }
          return;
        }
        else if (selection === "retirer") {
          const initialLen = taches_base.length;
          const resultat4 = taches_base.filter(t4 => t4.nom !== id);
          if (resultat4.length < initialLen) {
            localStorage.setItem("base_taches", JSON.stringify(resultat4));
            afficherTaches();
            reponses.innerHTML = "🗑️ La tâche <b>" + id + "</b> a été supprimée.";
          } else {
            reponses.innerHTML = "❌ La tâche que vous voulez supprimer n'existe pas.";
          }
          return;
        }
      }

      // RAPPEL
      if (selection2 === "rappel") {
        if (selection === "ajouter") {
          if (!id || !phrase) {
            reponses.textContent = "⚠️ Veuillez remplir tous les champs pour ajouter un rappel.";
            return;
          }
          let rappels = JSON.parse(localStorage.getItem("base_rappel")) || [];
          // ajoute un timestamp pour ordre (optionnel mais utile)
          const now = new Date().toISOString();
          rappels.push({titre: id, contenu: phrase, created: now});
          localStorage.setItem("base_rappel", JSON.stringify(rappels));
          document.getElementById('input1').value = "";
          document.getElementById('input2').value = "";
          afficherRappels();
          reponses.textContent = "✅ Rappel ajouté.";
          return;
        }
        else if (selection === "recherche") {
          let rappels = JSON.parse(localStorage.getItem("base_rappel")) || [];
          const found = rappels.filter(r => r.titre === id);
          if (found.length === 0) {
            reponses.textContent = "🔎 Aucun rappel trouvé pour: " + id;
          } else {
            reponses.innerHTML = found.map(r => `
              <div class="rappel-card">
                <h4>📌 ${r.titre}</h4>
                <p>${r.contenu}</p>
              </div>
            `).join("");
          }
          return;
        }
        else if (selection === "retirer") {
          let rappels = JSON.parse(localStorage.getItem("base_rappel")) || [];
          const newR = rappels.filter(r => r.titre !== id);
          if (newR.length < rappels.length) {
            localStorage.setItem("base_rappel", JSON.stringify(newR));
            afficherRappels();
            reponses.textContent = "🗑️ Rappel supprimé: " + id;
          } else {
            reponses.textContent = "❌ Rappel introuvable.";
          }
          return;
        }
      }
    }

    /* ---------------------------
       Fonctions d'affichage pour chaque type (utilisées partout)
       --------------------------- */

    function afficherInfos() {
      const reponses = document.getElementById('reponse');
      const list = JSON.parse(localStorage.getItem('ma_liste3')) || {};
      if (Object.keys(list).length === 0) {
        reponses.innerHTML = '<p class="empty">📭 La liste est vide.</p>';
        return;
      }
      let affichage = "<h3>📃 Liste des infos :</h3>";
      for (let id in list) {
        affichage += `
          <div class="info-card">
            <h4>📌 ${id}</h4>
            <p>${list[id]}</p>
          </div>
        `;
      }
      reponses.innerHTML = affichage;
    }

    function afficherTaches() {
      const reponses = document.getElementById('reponse');
      const taches_base = JSON.parse(localStorage.getItem("base_taches")) || [];
      if (taches_base.length === 0) {
        reponses.innerHTML = '<p class="empty">📭 Aucune tâche enregistrée.</p>';
        return;
      }

      // Si filtre ADT/ADTA/ADTH/ADTD présent, applique-le
      let op_a_el = document.getElementById('i_oa');
      let op_a = op_a_el ? op_a_el.value : "ADT";

      // fonctions date
      const pad = n => (n < 10 ? '0' + n : n);
      const todayISO = (() => {
        const d = new Date();
        return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
      })();
      const yesterdayISO = (() => {
        const d = new Date(); d.setDate(d.getDate()-1);
        return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
      })();
      const tomorrowISO = (() => {
        const d = new Date(); d.setDate(d.getDate()+1);
        return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
      })();

      let toShow = taches_base.slice(); // copie
      if (op_a === "ADTA") {
        toShow = taches_base.filter(t => t.date && t.date.includes(todayISO));
      } else if (op_a === "ADTH") {
        toShow = taches_base.filter(t => t.date && t.date.includes(yesterdayISO));
      } else if (op_a === "ADTD") {
        toShow = taches_base.filter(t => t.date && t.date.includes(tomorrowISO));
      } // ADT (tout) laisse tel quel

      if (toShow.length === 0) {
        reponses.innerHTML = '<p class="empty">📭 Aucune tâche pour ce filtre.</p>';
        return;
      }

      let affichage = "<h3>📋 Liste des tâches :</h3>";
      toShow.forEach((tache, idx) => {
        affichage += `
          <div class="tache-card">
            <div class="tache-left">
              <h4>📝 ${tache.nom}</h4>
              <p>${tache.description}</p>
            </div>
            <div class="tache-meta">
              <div><strong>${tache.importance || ''}</strong></div>
              <div>${tache.date || ''}</div>
              <div style="margin-top:6px">
                <button class="small-btn" onclick="supprimerTache('${tache.nom.replace(/'/g, "\\'")}')">Suppr.</button>
              </div>
            </div>
          </div>
        `;
      });
      reponses.innerHTML = affichage;
    }

    function afficherRappels() {
      const reponses = document.getElementById('reponse');
      const rappels = JSON.parse(localStorage.getItem("base_rappel")) || [];
      if (rappels.length === 0) {
        reponses.innerHTML = '<p class="empty">📭 Aucun rappel enregistré.</p>';
        return;
      }
      let affichage = "<h3>📋 Liste des rappels :</h3>";
      rappels.forEach((r, idx) => {
        affichage += `
          <div class="rappel-card">
            <h4>📌 ${r.titre}</h4>
            <p>${r.contenu}</p>
            <div style="font-size:11px;color:#888;margin-top:6px">${r.created ? new Date(r.created).toLocaleString() : ''}</div>
          </div>
        `;
      });
      reponses.innerHTML = affichage;
    }

    /* ---------------------------
       Affichage global relié au bouton "Afficher toute la liste"
       --------------------------- */
    function affiche() {
      const selection2 = document.getElementById('selectionnez2').value;
      if (selection2 === "info") afficherInfos();
      else if (selection2 === "taches") afficherTaches();
      else if (selection2 === "rappel") afficherRappels();
    }

    /* ---------------------------
       Fonctions utilitaires exposées (ex : suppression depuis carte)
       --------------------------- */
    function supprimerTache(nom) {
      let taches_base = JSON.parse(localStorage.getItem("base_taches")) || [];
      const newList = taches_base.filter(t => t.nom !== nom);
      localStorage.setItem("base_taches", JSON.stringify(newList));
      afficherTaches();
      // petit message
      const reponses = document.getElementById('reponse');
      reponses.insertAdjacentHTML('afterbegin', `<div style="color:green">🗑️ Tâche supprimée : ${nom}</div>`);
    }

    /* ---------------------------
       Au chargement : affiche l'état actuel selon selection
       --------------------------- */
    (function init() {
      // si selectionnez2 est taches initialement, créer controles
      if (document.getElementById('selectionnez2').value === 'taches') {
        createTacheControls();
      }
      // afficher le contenu correspondant
      affiche();
    })();
  </script>
</body>
</html>
