<script>
 import { onMount } from "svelte";

 let initialHealth = 100; // Startwert für Leben
  let initialCash = 500;   // Startwert für Cash

  let health = initialHealth;
  let cash = initialCash;
 
  let currentQuestionIndex = 0; // Index der aktuellen Frage
  let endGameMessage = ""; // Nachricht nach Abzug von Miete und Lebensmitteln
  let showMonthlyCostsMessage = false; // Steuerung der Zwischenansicht
  let gameFinished = false; // Steuerung der Endansicht
  let cashBeforeCosts = 0; // Zwischenspeicherung des Cash-Werts vor Abzug
  let healthBeforeCosts = 0; // Zwischenspeicherung des Health-Werts vor Abzug
  let gameover = false; // Steuerung wenn kein Leben
  

  // Fragen mit Ja-/Nein-Optionen
  let questions = [
    {
      question: "Du möchtest mehr Sport treiben. Beginnst du eine Fitnessstudio-Mitgliedschaft?",
      yes: { text: "Ja", cashChange: -40, healthChange: +10 },
      no: { text: "Nein", cashChange: 0, healthChange: -10 }
    },
    {
      question: "Du hast eine Erkältung und brauchst Medikamente gegen Halsschmerzen und Husten. Kaufst du sie in der Apotheke?",
      yes: { text: "Ja", cashChange: -30, healthChange: +10 },
      no: { text: "Nein", cashChange: -0, healthChange: -10 }
    },
    {
      question: "Deine Zähne brauchen dringend eine Prophylaxe-Behandlung. Gehst du zum Zahnarzt?",
      yes: { text: "Ja", cashChange: -85, healthChange: +20 },
      no: { text: "Nein", cashChange: -0, healthChange: -20 }
    },
    {
      question: "Deine Brille ist kaputt. Lässt du sie reparieren?",
      yes: {  text: "Ja", cashChange: -30, healthChange: +20 },
      no: { text: "Nein", cashChange: -0, healthChange: -15 }
    },
    {
      question: "Ein kalter Winter bricht ein, und du weißt, dass die Heizkostenabrechnung Ende des Jahres teuer wird. Du frierst aber schon jetzt. Drehst du trotzdem die Heizung auf?",
      yes: { text: "Ja", cashChange: -45, healthChange: +10 },
      no: { text: "Nein", cashChange: -0, healthChange: -8 }
    },
    {
      question: "Deine Wohnung hat Schimmel, aber dein Vermieter kümmert sich nicht darum. Lässt du ihn auf eigene Kosten entfernen?",
      yes: { text: "Ja", cashChange: -200, healthChange: +30 },
      no: { text: "Nein", cashChange: -0, healthChange: -30 }
    },
    {
      question: "Deine Waschmaschine ist kaputt gegangen. Kaufst du eine neue?",
      yes: { text: "Ja", cashChange: -100, healthChange: +15 },
      no: { text: "Nein", cashChange: -0, healthChange: -15 }
    },
    {
      question: "Deine Miete steigt um 50 Euro. Ziehst du in eine günstigere Wohnung um, obwohl du dann längere Wege in Kauf nehmen musst?",
      yes: { text: "Ja", cashChange: +50, healthChange: -15 },
      no: { text: "Nein", cashChange: -50, healthChange: -0 }
    },
    {
     question: "Dein Chef fragt, ob du Überstunden machst, für die du 60 Euro mehr verdienst. Willigst du ein?",
      yes: { text: "Ja", cashChange: +60, healthChange: -20 },
      no: { text: "Nein", cashChange: -0, healthChange: -0 }
    },
    {
      question: "Du möchtest im Sommer mit deinen Freund:innen in den Urlaub. Fährst du mit?",
      yes: { text: "Ja", cashChange: -100, healthChange: +100 },
      no: { text: "Nein", cashChange: 0, healthChange: -20 }
    },
    {
      question: "Du könntest am Wochenende bei einem Nebenjob extra Geld verdienen. Nimmst du ihn an?",
      yes: { text: "Ja", cashChange: +150, healthChange: -20 },
      no: { text: "Nein", cashChange: -0, healthChange: +15 }
    },
    {
      question: "Ein neuer Film läuft im Kino, den du unbedingt sehen willst. Gehst du mit einer Freundin?",
      yes: { text: "Ja", cashChange: -20, healthChange: +10 },
      no: { text: "Nein", cashChange: -0, healthChange: -10 }
    },
    {
      question: "Deine Freunde wollen in eine Bar und danach feiern. Kommst du mit?",
      yes: { text: "Ja", cashChange: -40, healthChange: +35 },
      no: { text: "Nein", cashChange: -0, healthChange: -15 }
    },
    {
      question: "Du bist unzufrieden mit deiner Frisur. Leistest du dir einen Friseurbesuch?",
      yes: { text: "Ja", cashChange: -30, healthChange: +15 },
      no: { text: "Nein", cashChange: -0, healthChange: -15 }
    },
     {
      question: "Du brauchst neue Winterschuhe. Kaufst du dir neue?",
      yes: { text: "Ja", cashChange: -50, healthChange: +20 },
      no: { text: "Nein", cashChange: -0, healthChange: -15 }
    },
    
  ];

  const monthlyCosts = 300; // Kosten für Miete und Lebensmittel

  function handleAnswer(option) {
    cash += option.cashChange;
    health += option.healthChange;
    currentQuestionIndex++; // Nächste Frage anzeigen

    // Überprüfen, ob alle Fragen beantwortet sind
    if (currentQuestionIndex === questions.length) {
      calculateMonthlyCosts(); // Nach dem letzten Schritt die Miete und Lebensmittel abziehen
    } else if (cash < 300) {
      proceedToEndScreen();
    } else if (health < 0) {
      proceedToEndLife();
    }
  }

 // Funktion, um die Reihenfolge der Fragen zu randomisieren
  function shuffleQuestions() {
    questions = questions.sort(() => Math.random() - 0.5);
    }

  function calculateMonthlyCosts() {
    cashBeforeCosts = cash; // Aktuellen Cash-Wert speichern
    healthBeforeCosts = health; // Aktuellen Health-Wert speichern
    cash -= monthlyCosts; // Kosten abziehen
    endGameMessage = `Deine Miet- und Lebensmittelkosten betragen ${monthlyCosts} für den Monat.`;
    showMonthlyCostsMessage = true; // Zwischenansicht aktivieren
   }

  function proceedToEndScreen() {
    showMonthlyCostsMessage = false; // Zwischenansicht deaktivieren
    gameover = false;
    gameFinished = true; // Endansicht aktivieren
  }
  function proceedToEndLife() {
    showMonthlyCostsMessage = false; // Zwischenansicht deaktivieren
    gameFinished = false; // Endansicht aktivieren
    gameover = true;
  }
  // Funktion zum Zurücksetzen
  function resetGame() {
    health = initialHealth;
    cash = initialCash;
    currentQuestionIndex = 0;
    showMonthlyCostsMessage = false;
    gameFinished = false;
    gameover = false;

    shuffleQuestions(); // Fragen neu mischen
  }
  // Beim Start des Spiels Fragen mischen
  onMount(() => {
    shuffleQuestions();
  });
</script>

<style>

main {
  h1 {
    font-family: 'Bahnschrift SemiBold'; /* Schriftart setzen */
    font-size: 3,3rem; /* Schriftgröße */
    color: #36436F; /* Farbe */
    text-align: center; /* Zentriert */
    margin: 20px 0; /* Abstand oben und unten */
  }

  position:relative;
 }

  .question-container {
    margin: 1rem 0;
    padding: 1rem;
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: #f9f9f9;
  }

  .question {
    font-family: 'Bahnschrift SemiBold';
    font-size: 1.2rem;
    font-weight: bold;
    margin-bottom: 1rem;
  }

  .button {
    display: inline-block;
    margin: 0.5rem;
    padding: 0.75rem 1rem;
    border: none;
    border-radius: 5px;
    font-family: 'Bahnschrift SemiBold';
    font-size: 1rem;
    cursor: pointer;
  }

  .button-yes {
    background-color: #36436F;
    color: white;
  }

  .button-yes:hover {
    background-color: #192841;
  }

  .button-no {
    background-color:#B6000F;
    color: white;
  }

  .button-no:hover {
    background-color: #800E13;
  }

 .button-ok {
    background-color: #2196F3;
    color: white;
  }

  .button-ok:hover {
    background-color: #1E88E5;
  }
  .status {
    font-family: 'Bahnschrift SemiBold';
    margin-bottom: 1rem;
  }
   .message {
    font-size: 1.5rem;
    font-weight: bold;
    text-align: center;
    margin: 2rem 0;
  }
  .values-container {
    margin-bottom: 1rem;
    text-align: center;
    font-size: 1.2rem;
    color: black 
  } 
  img {
    position: absolute;
    z-index: 0;
    left: 0;
    top: 0;
    right: 0;
    filter: blur(4px);
    opacity: 0.5;
    transform: scale(1.3); /* Zoom um 20% */
    transform-origin: center; /* Mittelpunkt des Zooms */
    }
    .button-retry {
    background-color: #ff6f61;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    font-size: 1rem;
    cursor: pointer;
    margin-top: 20px;
  }
  .button-retry:hover {
    background-color: #e65a4e;
  }

</style>

<img src="https://www.hamburg.de/resource/image/374156/landscape_ratio16x9/1240/697/618e2d4cf4d3f767e274da0defce67e/48592D3AF663D4D0A93FE3D38384DF5C/luftaufnahme-backsteinwohngebaeude-bild.jpg">
<main>
  <h1>Survive Hamburg</h1>
    
 {#if gameFinished}
    <!-- Endansicht -->
    <p><strong>Spiel beendet! Deine Ergebnisse:</strong></p>
    <div class="status">
      <p><strong>❤️ Leben:</strong> {health} / {initialHealth}</p>
      <p><strong>💰 Cash:</strong> {cash} / {initialCash}</p>
       <p style="color: red; font-weight: bold;">Dein Cash ist unter 300 gefallen. Du kannst die Miete und Lebenshaltungskosten nicht mehr zahlen!</p> 
       <button class="button button-retry" on:click={resetGame}>Probier's nochmal</button> 
    </div>


  {:else if showMonthlyCostsMessage}
    <!-- Zwischenansicht mit den Mietkosten -->
    <div>
      <div class="values-container">
        <p><strong>Vor Abzug:</strong></p>
       <p><strong>❤️:</strong> {healthBeforeCosts} / {initialHealth}</p>
          <p><strong>💰:</strong> {cashBeforeCosts} / {initialCash}</p>
      </div>
      <div class="message">
        <p>{endGameMessage}</p>
      </div>
      <div class="values-container">
        <p><strong>Nach Abzug:</strong></p>
       <p><strong>❤️ Leben:</strong> {health} / {initialHealth}</p>
       <p><strong>💰 Cash:</strong> {cash} / {initialCash}</p>
      </div>
      <button class="button button-retry" on:click={resetGame}>Probier's nochmal</button>
    </div>
  
  {:else if gameover}
  <p><strong>Spiel beendet! Deine Ergebnisse:</strong></p>
    <div class="status">
      <p><strong>❤️ Leben:</strong> {health} / {initialHealth}</p>
      <p><strong>💰 Cash:</strong> {cash} / {initialCash}</p>
       <p style="color: red; font-weight: bold;">GameOver! Du hast kein Leben mehr!</p> 
       <button class="button button-retry" on:click={resetGame}>Probier's nochmal</button> 
    </div>

  {:else if currentQuestionIndex < questions.length}
    <!-- Frage-Seite mit Leben und Cash -->
    <div class="status">
       <p><strong>❤️:</strong> {health} / {initialHealth}</p>
       <p><strong>💰:</strong> {cash} / {initialCash}</p>
    </div>
    <div class="question-container">
      <div class="question">
        {questions[currentQuestionIndex].question}
      </div>
      <button 
        class="button button-yes" 
        on:click={() => handleAnswer(questions[currentQuestionIndex].yes)}>
        {questions[currentQuestionIndex].yes.text}
      </button>
      <button 
        class="button button-no" 
        on:click={() => handleAnswer(questions[currentQuestionIndex].no)}>
        {questions[currentQuestionIndex].no.text}
      </button>
    </div>
  {/if}
</main>


