<script>
  const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;

  const AUTH_URL = `https://accounts.spotify.com/authorize?client_id=${Client_Id}&response_type=code&redirect_uri=http://localhost:5173/coverartgame-auth&scope=user-read-private%20user-read-email`;

  function login() {
    window.location = AUTH_URL;
  }
</script>

<section class="h-full">
  <div class="m-30 m-auto">
    <a href="www.dn.se">LINK</a>
    <p>Playlist:</p>
    <button on:click={login}>Log in with Spotify</button>
  </div>
</section>

<style>
</style>
