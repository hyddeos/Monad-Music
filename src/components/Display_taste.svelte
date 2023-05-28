<script>
  export let data;
  console.log("dataaaa", data);
</script>

<div class="text-left">
  <h2 class="font-heading text-2xl">TOP SONGS</h2>
  <h4 class="">Songs that has been on your top-list for most years</h4>
  {#each data.songs as song, index}
    <div class="flex justify mt-2">
      <div class="w-1/12 font-bold text-sec-400 text-xl">{index + 1}.</div>
      <div class="w-1/2">
        <h4 class="font-bold text-left">{song.title}</h4>
        <h5 class="font-bold text-left text-sm italic">{song.artist}</h5>
      </div>
      <div class="w-1/2" />
    </div>
  {/each}
</div>
