<script>
  const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;

  const AUTH_URL = `https://accounts.spotify.com/authorize?client_id=${Client_Id}&response_type=code&redirect_uri=http://localhost:5173/coverartgame-auth&scope=user-read-private%20user-read-email`;

  function login() {
    window.location = AUTH_URL;
  }

  //
  // LAYOUT FOR GAME
  //
  let game_loaded = false;
  let playlist = "";
  let albumcover = "https://www.bengans.se/bilder/artiklar/560029.jpg";
  let album_guess = 99;

  // Temp varibles
  let albums = [
    { id: 0, artist: "Cult of Luna", album: "Somewhere Along The Highway" },
    { id: 1, artist: "Amenra", album: "Mass III" },
    {
      id: 2,
      artist: "Godspeed You! Black Emperor",
      album: "Lift your skinny fist like antennas to the sky",
    },
    { id: 3, artist: "Fall of Efrafa", album: "Elil" },
    { id: 4, artist: "Show Me A Dinosour", album: "Reke" },
  ];

  function set_active_guess(album_id) {
    album_guess = album_id;
  }

  async function getProfile() {
    let accessToken = localStorage.getItem("access_token");

    if (!accessToken) return;

    const response = await fetch("https://api.spotify.com/v1/me", {
      headers: {
        Authorization: "Bearer " + accessToken,
      },
    });

    const data = await response.json();
    console.log(data);
  }

  getProfile();
</script>

<section id="main_content" class="flex">
  <div class="m-30 mx-auto w-full">
    <button on:click={login}>Log in with Spotify</button>
    <p>Current Guess: {album_guess}</p>
    <p>Current PL: {playlist}</p>

    {#if game_loaded}
      <h2 class="text-5xl text-center font-handwrite tracking-wider">
        What Album Is This? <span class="text-2xl text-light-300 text-right"
          >(1/10)</span
        >
      </h2>
      <img
        class="m-auto p-5 w-1/5"
        src={albumcover}
        alt="Guess this album cover"
      />
      <div class="w-full flex flex-wrap justify-center px-2">
        {#each albums as album}
          <div class="p-2">
            <button
              on:click={() => set_active_guess(album.id)}
              class="w-80 h-40 rounded {album_guess === album.id
                ? 'bg-dark-200 text-dark-700'
                : 'bg-dark-700'} text-center text-ellipsis overflow-hidden hover:bg-dark-100 hover:text-dark-700"
            >
              <h3 class="font-bold text-xl p-1">
                {album.album}
                <h3>
                  <h4 class="font-thin p-1">{album.artist}</h4>
                </h3>
              </h3></button
            >
          </div>
        {/each}
      </div>
      <div class="m-auto text-center">
        <button
          class="w-40 h-20 rounded {album_guess !== 99
            ? 'bg-prim-500 text-dark-700'
            : 'invisible'}  hover:bg-dark-100">Submit</button
        >
      </div>
    {:else}
      <h2 class="text-5xl text-center font-handwrite tracking-wider">
        Load Your Playlist!
      </h2>
      <p>Game not loaded</p>
      <input bind:value={playlist} />
    {/if}
  </div>
</section>

<style>
</style>
