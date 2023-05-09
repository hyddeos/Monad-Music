<script>
  export let questions;
  export let hard_level;
  export let game_over = 1;
  export let score_counter = 0;

  hard_level++; // To avoid 0 value

  let active_guess = 99;
  let current_question = 0;

  function cssVariables(node, variables) {
    setCssVariables(node, variables);

    return {
      update(variables) {
        setCssVariables(node, variables);
      },
    };
  }
  function setCssVariables(node, variables) {
    for (const name in variables) {
      node.style.setProperty(`--${name}`, variables[name]);
    }
  }

  function submit_guess(album_id, right_answer) {
    active_guess = album_id;
    let guess_btn = document.getElementById(`btn${active_guess}`);
    let right_btn = document.getElementById(`btn${right_answer}`);

    // Add points
    if (active_guess == right_answer) {
      score_counter++;
      guess_btn.style.backgroundColor = "#42c968"; // Right Anwser
    } else {
      guess_btn.style.backgroundColor = "#c94242"; // The Wrong guess
      right_btn.style.backgroundColor = "#42c968"; // What was the right Anwser
    }
    setTimeout(() => {
      wait(1500);
      guess_btn.style.backgroundColor = "";
      right_btn.style.backgroundColor = "";
      current_question++;
    }, 0);
    // Check if game is over
    if (current_question + 1 == questions.length) {
      game_over = 2;
    }
  }

  function wait(ms) {
    var start = new Date().getTime();
    var end = start;
    while (end < start + ms) {
      end = new Date().getTime();
    }
  }
</script>

<h2 class="text-5xl text-center font-handwrite tracking-wider my-2">
  What Album Is This? <span class="text-2xl text-light-300 text-right"
    >({current_question + 1}/{questions.length})</span
  >
</h2>
<div class="m-auto" id="image_holder">
  <img
    use:cssVariables={{ hard_level }}
    id="hard_level"
    class=""
    src={questions[current_question].cover}
    alt="Guess this album cover"
  />
</div>

<div class="w-full flex flex-wrap justify-center px-2">
  {#each questions[current_question].albums[0] as album}
    <div class="p-2">
      <button
        id="btn{album[0].album_id}"
        on:click={() =>
          submit_guess(album[0].album_id, questions[current_question].answer)}
        class="w-80 h-40 rounded bg-sec-800 text-center text-ellipsis overflow-hidden hover:bg-dark-100 hover:text-dark-700"
      >
        <h3 class="font-bold text-xl p-1">
          {album[0].album}
          <h3>
            <h4 class="font-thin p-1">{album[0].artist}</h4>
          </h3>
        </h3></button
      >
    </div>
  {/each}
</div>
<div class="m-auto text-center" />

<style>
  #image_holder {
    width: 640px;
    height: 640px;
    overflow: hidden;
  }
  #hard_level {
    -moz-transform: scale(var(--hard_level));
    -o-transform: scale(var(--hard_level));
    -ms-transform: scale(var(--hard_level));
    transform: scale(var(--hard_level));
  }
</style>
