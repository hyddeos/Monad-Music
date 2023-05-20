<script>
  import UltimateGetPlaylists from "../../components/Ultimate_get_playlists.svelte";

  const redirectUri = import.meta.env.VITE_REDIRECT_URL;
  const client_id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;

  let bgImage = "/bg.jpg";

  let playlists = [];
  console.log("redir", redirectUri);
  console.log("cli", client_id);
</script>

<section id="main_content" class="flex">
  <img id="bg" src={bgImage} alt="consert background" />
  <div class="m-auto pt-[15%]">
    <UltimateGetPlaylists />
  </div>
</section>

<style>
  #bg {
    z-index: -2;
    opacity: 0.3;
    min-height: 100%;
    min-width: 1024px;
    width: 100%;
    height: auto;
    position: fixed;
    top: 0;
    left: 0;
  }
</style>
