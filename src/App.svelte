<script>
  // SVELTE IMPORTS //
  import { fade, scale } from "svelte/transition";
  import { onMount } from "svelte";
  $: testing = console.log(speech.getNumber);

  $: number = speech.transcript;

  $: end = false;

  window.SpeechRecognition =
    window.SpeechRecognition || window.webkitSpeechRecognition;

  // SPEECH OBJECT //
  let speech = {
    recon: new SpeechRecognition(),
    getNumber: Math.floor(Math.random() * 100 + 1),
    transcript: "",
    spoke: false,
    notNumber: false,
    // METHODS //
    speak: e => {
      let msg = e.results[0][0].transcript;
      speech.transcript = +msg;
      speech.spoke = true;
      speech.check(msg);
    },
    check: msg => {
      if (Number.isNaN(speech.transcript)) {
        speech.notNumber = true;
        speech.spoke = false;
        if ((speech.notNumber = true)) {
          setTimeout(() => {
            speech.notNumber = false;
          }, 2300);
        }

        speech.transcript = "That isn't a valid number";
        return;
      }
      if (speech.getNumber === speech.transcript) {
        setTimeout(() => {
          speech.recon.addEventListener("end", () => speech.recon.abort());
        }, 1000);
      }
    },
    reset: () => {
      location.reload();
    }
  };

  // INITIALIZATION FUNCTION //
  function start() {
    speech.recon.start();
    speech.recon.addEventListener("result", speech.speak);
    speech.recon.addEventListener("end", () => speech.recon.start());
  }
</script>

<style>
  main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    color: white;
    width: 35vw;
  }

  .notice {
    margin-top: 1rem;
  }

  .container i {
    font-size: 15rem;
    color: rgb(210, 161, 214);
    margin-bottom: 1rem;
  }

  h2 {
    margin-top: 1rem;
  }

  .play-again {
    padding: 1rem 2rem;
    border: none;
    background-color: orangered;
    border-radius: 10px;
    margin-top: 1rem;
    color: white;
    font-size: 1.3rem;
  }

  .msg {
    font-size: 1.5rem;
    margin-top: 2rem;
  }

  .msg div {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .box {
    text-align: center;
    border: 5px solid #fdfdfd;
    padding: 0.4rem;
    font-size: 2rem;
    margin-top: 1rem;
    width: 15vw;
  }
</style>

<svelte:head>
  <title>Speak Number Guessing Game</title>
</svelte:head>
<svelte:window use:start />

<main>
  <div class="container">
    <i class="fas fa-microphone" />
  </div>

  <h2>Speak a number into your microphone.</h2>

  <div class="msg">
    <!-- IF NOT A NUMBER -->
    {#if speech.notNumber}
      <h2>{number}</h2>
    {/if}
    <!-- IF YOU SPOKE -->
    {#if speech.spoke}
      <div>
        You said:
        <span class="box">{number}</span>
      </div>
    {/if}
    <!-- IF THE NUMBER IS GREATER THAN 100 AND LESS THAN 100 -->
    {#if (speech.spoke && speech.transcript > 100) || (speech.spoke && speech.transcript < 1)}
      <div in:fade out:scale class="notice">
        Number must be between 1 and 100
      </div>
    {/if}
    <!-- IF YOU GOT THE RIGHT ANSWER -->
    {#if speech.transcript === speech.getNumber}
      <h2 in:fade={{ delay: 1000 }} out:scale>
        Congrats, you guess the correct number. It was {number}
      </h2>
      <button on:click={speech.reset()} class="play-again">Play again?</button>
      <!-- ELSE IF IT'S LOWER THAN THE NUMBER -->
    {:else if speech.spoke && speech.transcript > speech.getNumber}
      <h2 in:fade={{ delay: 800 }} out:scale>Go Lower</h2>
      <!-- ELSE IF IT'S HIGHER THAN THE NUMBER -->
    {:else if speech.spoke && speech.transcript < speech.getNumber}
      <h2 in:fade={{ delay: 800 }} out:scale>Go Higher</h2>
    {/if}
  </div>
</main>
