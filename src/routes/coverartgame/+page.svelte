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
  let albumcover = "https://www.bengans.se/bilder/artiklar/560029.jpg"
  let album_guess = 0 ;

  // Temp varibles
  let album_1 = "Cult of Luna - Vertikal"
  let album_2 = "Amenra - Mass III"
  let album_3 = "Godspeed You! Black Emperor - Lift Your skinny fist"
  let album_4 = "Abba - Mamma Mia"
  let album_5 = "Fall of Efrafa - Elil" 
  let albums = [
		{ id: 1, artist: 'Cult of Luna', album: 'Somewhere Alone The Highway' },
		{ id: 2, artist: 'Amenra', album: 'Mass III' },
		{ id: 3, artist: 'Godspeed You! Black Emperor', album: 'Lift your skinny fist like antennas to the sky' },
		{ id: 4, artist: 'Fall of Efrafa', album: 'Elil' },
		{ id: 6, artist: 'Show Me A Dinosour', album: 'Reke' },
	];

  function set_active_guess(album_id) {
    album_guess = album_id;
    console.log("Current Guess:", album_guess)
  }

</script>

<section class="flex">
  <div class="m-30 mx-auto w-full">
    <button on:click={login}>Log in with Spotify</button>
    <p>Current Guess: {album_guess}</p>
    <h2 class="text-4xl text-center font-handwrite tracking-wider">What Album Is This? <span class="text-2xl text-light-300 text-right">(1/10)</span></h2>
    <img class="m-auto p-5" src={albumcover} alt="Guess this album cover">
    <div class="border border-sec-500 w-full flex justify-center">
      <button on:click={() =>  set_active_guess(album_1)} class="w-1/5 bg-dark-700 text-center mx-3 h-40">
        {album_1}
      </button>
      <button on:click={() =>  set_active_guess(album_2)} class="w-1/5 bg-dark-700 text-center mx-3">
        {album_2}
      </button>
      <button on:click={() =>  set_active_guess(album_3)} class="w-1/5 bg-dark-700 text-center mx-3">
        {album_3}
      </button>
      <button on:click={() =>  set_active_guess(album_4)} class="w-1/5 bg-dark-700 text-center mx-3">
        {album_4}
      </button>
      <button on:click={() => set_active_guess(album_5)} class="w-1/5 bg-dark-700 text-center mx-3">
        {album_5}
      </button>
    </div>

  </div>
</section>

<style>
</style>
