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
  </div>
</div>
