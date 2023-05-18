<script>
  export let score;
  export let questions;
</script>

<div class="m-auto mt-40 z-10 flex justify-center">
  <div
    class="max-w-xl bg-dark-900 px-40 py-10 flex flex-col items-center rounded border-2 border-dark-700"
  >
    <h2 class="text-5xl text-center font-handwrite tracking-wider my-2">
      Results <span class="text-2xl text-light-300 text-right" />
    </h2>
    <h3 class="text-3xl text-center tracking-wider my-2 font-heading">
      You got
    </h3>
    <h2
      class="text-5xl text-center text-sec-500 tracking-wider my-2 font-heading"
    >
      {score} <span class="text-3xl text-light-300">of</span>
      {questions}
    </h2>
    <h3 class="text-3xl text-center tracking-wider my-2 font-heading">
      Albums Right
    </h3>
    <h5 class="py-2">Want to try another playlist?</h5>
    <a href="/coverartgame" data-sveltekit-reload>
      <button
        class="w-48 h-20 m-2 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
        ><strong>NEW GAME</strong></button
      >
    </a>
  </div>
</div>
