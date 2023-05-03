<script>
  import { browser } from "$app/environment";
  import { goto } from "$app/navigation";
  import CoverGame from "../../components/Cover_game.svelte";
  import CoverGameOver from "../../components/Cover_game_over.svelte";
  import NotAuthed from "../../components/NotAuthed.svelte";

  // AUTH
  let accessToken = "";
  function run_auth_check() {
    if (!browser) return;
      accessToken = localStorage.getItem("access_token");
      console.log("token page:", accessToken)
    if (!accessToken) return;
  }
  run_auth_check();
  
  let bgImage = "/bg.jpg";
  let tapeImage = "/tape.webp";

  let game_state = 0; // 0 = Not loaded, 1 = In progress, 2 = Game Finnished
  let playlist_input = "";
  let displayName = "";
  let questions = [];
  let score_counter = 0;
  let error_message = "";

  function generate_questions(playlist_data) {
    let albums = filterAlbums(playlist_data); // Gets all Unique albums
    // Gets Random numbers for albums(right awswer)
    // Get All the album-covers, Get random wrong answers(alternatives)
    let album_numbers = generateAlbumNumbers(albums);
    let question_holder = [];
    for (let i = 0; i < album_numbers.length; i++) {
      // Generate numbers for wrong answser and make sure the right answer is not there
      let alternatives_numbers = generateAlbumNumbers(albums);
      while (alternatives_numbers.includes(album_numbers[i])) {
        alternatives_numbers = generateAlbumNumbers(albums);
      }

      alternatives_numbers = alternatives_numbers.slice(0, 4); // Just take the first 4 as alternative guesses
      alternatives_numbers.push(album_numbers[i]); // Add right one back in and shuffle the list
      alternatives_numbers = shuffleList(alternatives_numbers);

      let alternatives = [];
      alternatives_numbers.forEach((number) => {
        alternatives.push([
          {
            artist: albums[number].track.artists[0].name,
            album: albums[number].track.album.name,
            album_id: number,
          },
        ]);
      });
      // Add all the date to the questions
      question_holder.push({
        question_id: i + 1,
        answer: album_numbers[i],
        cover: albums[album_numbers[i]].track.album.images[0].url,
        albums: [alternatives],
      });
    }
    return question_holder;
  }

  // Filters albums so every Album-cover is Unique(I.E remove duplets)
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
      numbers = getUniqueRandomNumbers(10, 0, albums.length - 1);
    } else {
      // Less then 11 unique albums
      numbers = getUniqueRandomNumbers(albums.length, 0, albums.length - 1);
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

  function shuffleList(array) {
    for (var i = array.length - 1; i > 0; i--) {
      var j = Math.floor(Math.random() * (i + 1));
      var temp = array[i];
      array[i] = array[j];
      array[j] = temp;
    }
    return array;
  }

  function input_check(playlist_input) {
    let playlist_id = "";

    if (playlist_input.length == 0){
      error_message = "Can´t be empty, Type a vaild Spotify Playlist link or ID";
    }
    else if (playlist_input.includes("spotify")){
      const regex = /\w+$/;
      let m;
      if ((m = regex.exec(playlist_input)) !== null) {
        m.forEach((match, groupIndex) => {
            playlist_id = m;            
        });
      }
      else {
        error_message = "You need to insert a vaild Spotify Playlist link or ID";
      }
    }
    else if (playlist_input.length > 20 && playlist_input.length < 25) {
      const regex = /^[a-zA-Z0-9]*$/gm;
      let m;
      if ((m = regex.exec(playlist_input)) !== null) {
        m.forEach((match, groupIndex) => {
            playlist_id = m;            
        });
      }
      else {
        error_message = "You need to insert a vaild Spotify Playlist link or ID";
      }
    }
    else{
      error_message = "You need to insert a vaild Spotify Playlist link or ID";
    }

    return playlist_id;
  }

  // GET DISPLAYNAME FOR AUTH.. Remove later
  /*
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
  */



  // GET PLAYLIST FROM API
  async function getPlaylist(playlist_input) {

    if (!browser) return;
    if (!input_check(playlist_input)) return;
    let playlist_id = input_check(playlist_input);
    
    error_message = "";

    let accessToken = localStorage.getItem("access_token");

    if (!accessToken) return;

    // Get playlist ID from url
    let playlist_url = "";
      playlist_url = `https://api.spotify.com/v1/playlists/${playlist_id}/tracks`;

    const response = await fetch(
      playlist_url,
      {
        headers: {
          Authorization: "Bearer " + accessToken,
        },
      }
    );

    const data = await response.json();
    console.log("reponse", data);
    if (data.error.status === 401) {
      goto('/auth')
      console.log("Token expired...Fetching you a new one");
    }
    //Setting up game
    questions = generate_questions(data.items);
    game_state = 1; // 1 = Inprogress
    console.log("questions:", questions);
  }
</script>

<section id="main_content" class="flex">
  <img id="bg" src={bgImage} alt="consert background" />
  {#if accessToken}
    <div class="m-30 mx-auto w-full">
      <p>Test url: https://open.spotify.com/playlist/37i9dQZF1EjgFGmwQdFGA3</p>
      {#if game_state == 1}
        <CoverGame {questions} bind:game_over={game_state}/>
      {:else if game_state == 2}
        <CoverGameOver score={score_counter} questions={questions.length} />
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
              bind:value={playlist_input}
            />
            <button
              on:click={() => getPlaylist(playlist_input)}
              class="w-28 h-9 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
              >Start Game</button
            >
            <p class="text-[#c94242]">{error_message}</p>
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
