<script>
  import Slider from "@bulatdashiev/svelte-slider";
  import { browser } from "$app/environment";

  import CoverGame from "../../components/Cover_game.svelte";
  import CoverGameOver from "../../components/Cover_game_over.svelte";
  import NotAuthed from "../../components/NotAuthed.svelte";

  let bgImage = "/bg.jpg";
  let tapeImage = "/tape.webp";

  let game_state = 0; // 0 = Not loaded, 1 = In progress, 2 = Game Finnished
  let need_new_token = false;
  let playlist_input = "";
  let value = [0]; // hard level
  let questions = [];
  let total_score;
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

    if (playlist_input.length == 0) {
      error_message =
        "Can´t be empty, Type a vaild Spotify Playlist link or ID";
    } else if (playlist_input.includes("spotify.com")) {
      const regex = /playlist\/([\w-]+)/;
      const match = playlist_input.match(regex);
      if (match) {
        playlist_id = match[1];
      } else {
        error_message = "There is something wrong with that URL";
      }
    } else if (playlist_input.length > 20 && playlist_input.length < 25) {
      const regex = /^[a-zA-Z0-9]*$/gm;
      let m;
      if ((m = regex.exec(playlist_input)) !== null) {
        m.forEach((match, groupIndex) => {
          playlist_id = m;
        });
      } else {
        error_message =
          "You need to insert a vaild Spotify Playlist link or ID";
      }
    } else {
      error_message = "You need to insert a vaild Spotify Playlist link or ID";
    }

    return playlist_id;
  }

  // GET PLAYLIST FROM API
  async function getPlaylist(playlist_input) {
    if (!browser) return;
    if (!input_check(playlist_input)) return;
    let playlist_id = input_check(playlist_input);

    error_message = "";

    let accessToken = localStorage.getItem("access_token");

    if (!accessToken) {
      need_new_token = true;
      return;
    }

    // Get playlist ID from url
    let playlist_url = "";
    playlist_url = `https://api.spotify.com/v1/playlists/${playlist_id}/tracks`;

    const response = await fetch(playlist_url, {
      headers: {
        Authorization: "Bearer " + accessToken,
      },
    });
    const data = await response.json();
    try {
      if (data.error) {
        if (data.error.status == 401) {
          need_new_token = true;
        } else {
          error_message = "There seems to be something wrong the the url or id";
        }
      }
    } catch (error) {
      console.error(error);
    }
    //Setting up game
    questions = generate_questions(data.items);
    game_state = 1; // 1 = Inprogress
  }
</script>

<section id="main_content mt-2" class="flex">
  <img id="bg" src={bgImage} alt="consert background" />
  <div class="mt-30 mx-auto w-full">
    {#if need_new_token}
      <NotAuthed />
    {:else if game_state == 1}
      <CoverGame
        {questions}
        hard_level={value}
        bind:game_over={game_state}
        bind:score_counter={total_score}
      />
    {:else if game_state == 2}
      <CoverGameOver score={total_score} questions={questions.length} />
    {:else}
      <div
        class="m-2 mt-8 md:h-96 py-6 md:py-0 relative bg-dark-900 md:bg-opacity-0 flex flex-col items-center rounded border-2 border-dark-700 md:border-0"
      >
        <img
          class="hidden md:block"
          id="tape"
          src={tapeImage}
          alt="music tape"
        />
        <h2
          class="text-4xl text-center text-light-200 md:text-dark-900 font-handwrite tracking-wider my-1 md:absolute inset-x-0 top-12"
        >
          Load Your Playlist!
        </h2>
        <h4
          class="text-center text-light-400 md:text-dark-700 text-xl font-thin my-3 md:absolute inset-x-0 top-20"
        >
          Spotify <span class="font-semibold">Playlist-URL</span> or
          <span class="font-semibold">Playlist-ID</span>
        </h4>
        <div class="md:absolute md:inset-x-0 md:bottom-32 m-auto text-center">
          <input
            type="text"
            placeholder="Insert Playlist here"
            class="p-2 m-2 md:m-0 text-dark-900 rounded-md h-10 w-60 border border-light-400 focus:border-sec-500 focus:ring-sec-500"
            bind:value={playlist_input}
          />
          {#if error_message}
            <p
              class="text-[#c94242] text-lg font-bold md:absolute md:inset-x-0 md:bottom-12 md:bg-dark-900 md:max-w-lg md:rounded md:border-2 md:border-[#c94242] m-auto text-center p-2"
            >
              {error_message}
            </p>
          {/if}
          <button
            on:click={() => getPlaylist(playlist_input)}
            class="w-28 h-9 bg-prim-500 rounded text-center text-light-100 text-ellipsis overflow-hidden hover:bg-prim-400"
            >Start Game</button
          >
        </div>
      </div>
      <div
        class="max-w-lg m-2 md:m-auto bg-dark-900 p-8 flex flex-col items-center rounded border-2 border-dark-700"
      >
        <h2
          class="text-xl text-center text-light-200 font-handwrite tracking-wider my-1"
        >
          Too Easy?
        </h2>
        <h2
          class="text-3xl text-center text-light-200 font-heading tracking-wider my-1"
        >
          TRY HARD MODE!
        </h2>
        <p class="mt-2">
          Hard Mode Level: <span class="text-sec-500 text-xl font-bold">
            {value[0] == 0 ? "OFF" : value[0]}
          </span>
        </p>

        <Slider min="0" max="5" bind:value>
          <span style="font-size: 40px;">
            {#if value == 0}
              &#128526;
            {:else if value == 1}
              &#128527;
            {:else if value == 2}
              &#128539;
            {:else if value == 3}
              &#128558;
            {:else if value == 4}
              &#128561;
            {:else if value == 5}
              &#128565;
            {/if}
          </span>
        </Slider>
        <p class="text-center m-2">
          At higher levels you will see less of the album cover.
        </p>
      </div>
    {/if}
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
  #tape {
    z-index: -1;
    height: 100%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>
