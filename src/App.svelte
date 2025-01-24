<script>
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

  // Fragen mit Ja-/Nein-Optionen
  let questions = [
    {
      question: "Du möchtest mehr Sport treiben. Beginnst du eine Fitnessstudio Mitgliedschaft?",
      yes: { text: "Ja", cashChange: -10, healthChange: +15 },
      no: { text: "Nein", cashChange: 0, healthChange: -15 }
    },
    {
      question: "Es ist eine Woche sehr kalt draußen und das spürt man auch in deiner Wohnung. Machst du die Heizung an?",
      yes: { text: "Ja", cashChange: -13, healthChange: +15 },
      no: { text: "Nein", cashChange: 0, healthChange: -15 }
    },
    {
      question: "Oh Nein! In deiner Wohnung bildet sich Schimmel an den Wänden! Lässt du ihn entfernen?",
      yes: { cashChange: -60, healthChange: +30 },
      no: { cashChange: 0, healthChange: -30 }
    },
    {
      question: "Ein neuer Film ist draußen, den du unbedingt sehen möchtest. Gehst du mit einer Freundin ins Kino?",
      yes: { text: "-5 Cash, +5 Leben", cashChange: -5, healthChange: +5 },
      no: { text: "+0 Cash, -5 Leben", cashChange: 0, healthChange: -5 }
    },
    {
      question: "Dein Chef fragt, ob du für geringfügig mehr Lohn ein paar Überstunden machst. Willigst du ein?",
      yes: { text: "+20 Cash, -15 Leben", cashChange: +20, healthChange: -15 },
      no: { text: "+0 Cash, +0 Leben", cashChange: 0, healthChange: +0 }
    },
    {
      question: "Du hast seit Tagen starke Kopfschmerzen. Besorgst du dir Schmerzmittel?",
      yes: { text: "-5 Cash, +10 Leben", cashChange: -5, healthChange: +10 },
      no: { text: "+0 Cash, -10 Leben", cashChange: 0, healthChange: -10 }
    },
    {
      question: "Deine Freunde wollen zusammen in eine Bar und danach Feiern gehen. Kommst du mit?",
      yes: { text: "-15 Cash, +5 Leben", cashChange: -15, healthChange: +5 },
      no: { text: "+0 Cash, -5 Leben", cashChange: 0, healthChange: -5 }
    },
    {
      question: "Zu deiner Arbeit musst du jeden Tag auch in der Kälte Rad fahren. Holst du dir das Deutschlandticket?",
      yes: { text: "-20 Cash, +5 Leben", cashChange: -20, healthChange: +5 },
      no: { text: "+0 Cash, -5 Leben", cashChange: 0, healthChange: -5 }
    },
    {
     question: "Dich hat eine fiese Erkältung erwischt und du brauchst Medikamente gegen Halsschmerzen und Husten. Gehst du zur Apotheke und kaufst dir welche? ",
      yes: { text: "-10 Cash, +10 Leben", cashChange: -10, healthChange: +10 },
      no: { text: "+0 Cash, -10 Leben", cashChange: 0, healthChange: -10 }
    },
    {
      question: "Deine Waschmaschine ist kaputt gegangen. Kaufst du dir per Kleinanzeigen eine neue?",
      yes: { text: "-30 Cash, +5 Leben", cashChange: -30, healthChange: +5 },
      no: { text: "+0 Cash, -5 Leben", cashChange: 0, healthChange: -5 }
    },
    {
      question: "Du könntest deine Winterjacke im Internet verkaufen. Verkaufst du sie?",
      yes: { text: "+15 Cash, -15 Leben", cashChange: +15, healthChange: -15 },
      no: { text: "+0 Cash, +0 Leben", cashChange: 0, healthChange: +0 }
    },
    {
      question: "Du würdest gerne mal eine neue Frisur ausprobieren. Leistest du dir einen Friseurbesuch? ",
      yes: { text: "-15 Cash, +5 Leben", cashChange: -15, healthChange: +5 },
      no: { text: "+0 Cash, -0 Leben", cashChange: 0, healthChange: -0 }
    },
    {
      question: "Dein Zahnarzt empfiehlt dir dringend eine professionelle Zahnreinigung. Bezahlst du eine Prophylaxe Behandlung? ?",
      yes: { text: "-28 Cash, +20 Leben", cashChange: -28, healthChange: +20 },
      no: { text: "+0 Cash, -10 Leben", cashChange: 0, healthChange: -10 }
    },
    {
      question: "Du brauchst neue Winterschuhe, da deine Alten ein großes Loch haben. Kaufst du dir Neue?",
      yes: { text: "-20 Cash, +15 Leben", cashChange: -20, healthChange: +15 },
      no: { text: "+0 Cash, -15 Leben", cashChange: 0, healthChange: -15 }
    },
     {
      question: "Du hast seit Monaten Probleme mit deinem Rücken. Gehst du zur Physio?",
      yes: { text: "-25 Cash, +30 Leben", cashChange: -25, healthChange: +30 },
      no: { text: "+0 Cash, -20 Leben", cashChange: 0, healthChange: -20 }
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
    }
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
    gameFinished = true; // Endansicht aktivieren
  }

</script>

<style>

main {
  h1 {
    font-family: 'Bahnschrift SemiBold'; /* Schriftart setzen */
    font-size: 3,3rem; /* Schriftgröße */
    color: black; /* Farbe */
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
    background-color: #4caf50;
    color: white;
  }

  .button-yes:hover {
    background-color: #45a049;
  }

  .button-no {
    background-color: #f44336;
    color: white;
  }

  .button-no:hover {
    background-color: #e41e2e;
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
  } 
  img {
    position: absolute;
    z-index: 0;
    left: 0;
    top: 0;
    right: -1;
    filter: blur(4px);
    opacity: 0.5;
    }

</style>

<img src="https://www.hamburg.de/resource/image/374156/landscape_ratio16x9/1240/697/618e2d4cf4d3f767e274da0defce67e/48592D3AF663D4D0A93FE3D38384DF5C/luftaufnahme-backsteinwohngebaeude-bild.jpg">
<main>
  <h1>Entscheidungsspiel</h1>
  {#if currentQuestionIndex < questions.length}
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
      <button class="button button-ok" on:click={proceedToEndScreen}>
        Okay
      </button>
    </div>
  {:else if gameFinished}
    <!-- Endansicht -->
    <p><strong>Spiel beendet! Deine Ergebnisse:</strong></p>
    <div class="status">
      <p><strong>❤️ Leben:</strong> {health} / {initialHealth}</p>
      <p><strong>💰 Cash:</strong> {cash} / {initialCash}</p>
    </div>
  
    {#if health <= 0}
      <p style="color: red; font-weight: bold;">Game Over! Du hast kein Leben mehr.</p>
    {/if}
    {#if cash < 0}
      <p style="color: red; font-weight: bold;">Du bist pleite!</p>
    {/if}
  {/if}
</main>


