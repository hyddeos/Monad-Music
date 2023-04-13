<script>
  import { onMount } from 'svelte';
  import { fade } from 'svelte/transition';

  let bgImage = "/bg.jpg";

  let x = 0;
  let y = 0;

  function updatePosition() {
    const maxX = window.innerWidth;
    const maxY = window.innerHeight;
    x = Math.floor(Math.random() * maxX + 100);
    y = Math.floor(Math.random() * maxY + 100);
  }

  onMount(() => {
    const interval = setInterval(() => {
      updatePosition();
    }, 4000);

    return () => clearInterval(interval);
  });

</script>




<section class="h-screen text-center">
  
  <img id="bg" src="{bgImage}" alt="consert background">
  <div class="bg-dark-900 w-full h-full fixed -z-30"></div>

  <div id="spotlight1" transition:fade style="transform: translate({x}px, {y}px"></div>  

    <section class="flex flex-row max-w-5xl m-auto pt-[20%] z-0">
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
    mix-blend-mode: color-dodge;
    opacity: 0.5;
    z-index: -1;
    position: absolute;
    width: 30rem;
    height: 30rem;
    background-color: blueviolet;
    filter: blur(1.5rem);
    border-radius: 100%;
    transition: transform 6s ease-out;
  }
  #bg {
    z-index: -2;
    opacity: 0.4;
    min-height: 100%;
    min-width: 1024px;    
    /* Set up proportionate scaling */
    width: 100%;
    height: auto;    
    /* Set up positioning */
    position: fixed;
    top: 0;
    left: 0;
  }
</style>
