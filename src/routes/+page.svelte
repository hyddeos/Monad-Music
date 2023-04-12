<script>
  	$: innerHeight = 0
	  $: innerWidth = 0

    $: t = makeNewHeight();
    $: s = makeNewWidth();

    const incr = () => (makeNewHeight(), makeNewWidth)

function makeNewHeight(innerHeight){
  return Math.floor(Math.random() * innerHeight); 
}
function makeNewWidth(){      
    var newW = Math.floor(Math.random() * innerWidth);    
    return newW;   
}

</script>

  <svelte:window bind:innerWidth bind:innerHeight />


<section class="bg-prim-900 h-screen text-center">
  <button on:click={random}>
    random
  </button>
  <div id="spotlight1"></div>
  <p>
    Inner Width: {innerWidth}, Random: {t}
  </p>
  <p>
    Inner Height: {innerHeight}, Random: {s}
  </p>
    <section class="flex flex-row max-w-5xl m-auto pt-[20%]">
      <div class="basis-1/3 h-40 m-auto border-r-2 border-prim-400">
        <img src="https://flowbite.com/docs/images/logo.svg"
          class="h-32"
          alt="Flowbite Logo"
        />
      </div>
      <div class="basis-2/3 h-40">
        <h1 class="text-8xl text-right">
          Header
          <h1>
            <h3
              class="text-4xl text-right font-ingress font-thin italic tracking-wider">
              - Lust Lera Leva
            </h3>
          </h1>
        </h1>
      </div>
    </section>
</section>

<style>
  #spotlight1 {
    position: absolute;
    width: 20rem;
    height: 20rem;
    background-color: blueviolet;
    border-radius: 100%;
  }

</style>
