<script>
  import { browser } from "$app/environment";
  import NotAuthed from "../../components/NotAuthed.svelte";

  let bgImage = "/bg.jpg";
  let tapeImage = "/tape.webp";

  const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;

  //
  // LAYOUT FOR GAME
  //
  let game_loaded = false;
  let playlist = "";
  let albumcover = "https://www.bengans.se/bilder/artiklar/560029.jpg";
  let album_guess = 99;
  let displayName = "";
  let albums = [];
  let album_numbers = [];
  let question = 1;
  let score_counter = 0;
  console.log("uni, ", albums);
  console.log("number counter, ", album_numbers.length);

  // Temp varibles
  let sample_albums = [
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

  // Filters albums so every Album-cover is Unique(ie remove duplets)
  function filterAlbums(playlist_items) {
    let temp_albums = [];
    let album_ids = [];
    playlist_items.forEach((playlist_item) => {
      if (!album_ids.includes(playlist_item.track.album.id)) {
        temp_albums.push(playlist_item);
        album_ids.push(playlist_item.track.album.id);
      }
    });
    return temp_albums;
  }

  function generateAlbumNumbers(albums) {
    let numbers = [];
    // If there is more then 10 unique albums
    if (albums.length > 10) {
      numbers = getUniqueRandomNumbers(10, 0, albums.length);
    } else {
      // Less then 11 unique albums
      numbers = getUniqueRandomNumbers(albums.length, 0, albums.length);
    }
    return numbers;
  }

  function getUniqueRandomNumbers(count, min, max) {
    let uniqueNumbers = [];
    while (uniqueNumbers.length < count) {
      let randomNumber = Math.floor(Math.random() * (max - min + 1)) + min;

      if (!uniqueNumbers.includes(randomNumber)) {
        uniqueNumbers.push(randomNumber);
      }
    }
    return uniqueNumbers;
  }

  let numbers = getUniqueRandomNumbers(10, 1, 100);
  console.log(numbers);

  // Check if logged in and api request
  async function getProfile() {
    if (!browser) return;

    let accessToken = localStorage.getItem("access_token");

    if (!accessToken) return;

    const response = await fetch("https://api.spotify.com/v1/me", {
      headers: {
        Authorization: "Bearer " + accessToken,
      },
    });

    const data = await response.json();
    console.log(data);

    displayName = data.display_name;
  }
  getProfile();

  async function getPlaylist(playlist_id) {
    if (!browser) return;

    let accessToken = localStorage.getItem("access_token");

    if (!accessToken) return;

    const response = await fetch(
      "https://api.spotify.com/v1/playlists/37i9dQZF1F0sijgNaJdgit/tracks",
      {
        headers: {
          Authorization: "Bearer " + accessToken,
        },
      }
    );

    const data = await response.json();
    console.log(data);
    //Setting up game
    albums = filterAlbums(data.items);
    album_numbers = generateAlbumNumbers(albums);
    game_loaded = true;
  }
</script>

<section id="main_content" class="flex">
  <img id="bg" src={bgImage} alt="consert background" />
  {#if displayName}
    <div class="m-30 mx-auto w-full">
      <p>Current Guess: {album_guess}</p>
      <p>Current PL: {playlist}</p>
      <p>Current display name:{displayName}</p>
      <p>Items: {albums}</p>
      {#if game_loaded}
        <h2 class="text-5xl text-center font-handwrite tracking-wider my-2">
          What Album Is This? <span class="text-2xl text-light-300 text-right"
            >({question}/{album_numbers.length})</span
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
        <div class="p-1 0 m-auto mt-40 w-1/3 rounded-md h-96 relative">
          <img id="tape" src={tapeImage} alt="music tape" />
          <h2
            class="text-4xl text-center text-dark-900 font-handwrite tracking-wider my-1 absolute inset-x-0 top-12"
          >
            Load Your Playlist!
          </h2>
          <h4
            class="text-center text-dark-700 text-xl font-thin my-3 absolute inset-x-0 top-20"
          >
            Spotify <span class="font-semibold">Playlist-URL</span> or
            <span class="font-semibold">Playlist-ID</span>
          </h4>
          <div class="absolute inset-x-0 bottom-32 m-auto text-center">
            <input
              type="text"
              placeholder="Insert Playlist here"
              class="p-2 text-dark-900 rounded-md h-10 w-60 border border-light-400 focus:border-sec-500 focus:ring-sec-500"
              bind:value={playlist}
            />
            <button
              on:click={() => getPlaylist()}
              class="w-28 h-9 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
              >Start Game</button
            >
          </div>
        </div>
      {/if}
    </div>
  {:else}
    <NotAuthed />
  {/if}
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
  #tape {
    z-index: -1;
    height: 100%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>
