<script>
  import { browser } from "$app/environment";
  import NotAuthed from "../components/NotAuthed.svelte";
  import {
    Email,
    Reddit,
    WhatsApp,
    Facebook,
    Twitter,
  } from "svelte-share-buttons-component";
  import DisplayTaste from "./Display_taste.svelte";

  const title = "My Ultimate Spotify Playlist";
  const desc =
    "This is my Ultimate Spotify playlist. Its based on all my previous years on spotify. Have a listen or make your own list over at https://music.eshtropy.se";
  let url = "";

  let error_message = "";
  let loading_list = 0; // 0 = not loading, 1 = loading, 2 = loaded
  let need_new_token = false;
  let total_years = 0;
  let total_songs = 0;
  let accessToken = "";

  let songs;

  if (browser) {
    accessToken = localStorage.getItem("access_token");
  }

  async function generate_list() {
    loading_list = true;
    error_message = "";
    const all_playlists = await get_playlists();
    const wrapped_lists_info = get_wrapped_lists_info(all_playlists);
    if (wrapped_lists_info) {
      const all_songs = await get_songs_from_lists(wrapped_lists_info);
      const yearly_top_songs = await get_top_songs(wrapped_lists_info); // Display Each years 5 top songs
      const counted_data = analyze_songs(all_songs);
      const reoccuring_songs = get_reoccuring_songs(counted_data.songs);
      display_analzyed_data(wrapped_lists_info.length, all_songs.length);
      songs = all_songs;
      loading_list = 2;
    } else {
      loading_list = false;
      console.error("Playlist already created or didt find new wrapped lists");
      error_message =
        "Playlist already created or we did not find any playlists to gather data from";
    }
  }

  function display_analzyed_data(years, songs) {
    total_years = years;
    total_songs = songs;
  }

  function get_reoccuring_songs(all_songs) {
    let songs = [];
    all_songs.forEach((song) => {
      if (song.count > 1) {
        songs.push(song.id);
      }
    });
    return songs;
  }

  async function get_top_songs(playlists) {
    let top_songs = [];
    for (const playlist of playlists) {
      const songs = await get_songs(playlist.id);
      top_songs = top_songs.concat(songs.slice(0, 5));
    }
    return top_songs;
  }

  function analyze_songs(all_songs) {
    const data = {
      artists: {},
      songs: {},
      albums: {},
    };
    all_songs.forEach((song) => {
      const { title_id: songId, album_id: albumId, artist_id: artistId } = song;
      data.songs[songId] = (data.songs[songId] || 0) + 1;
      data.albums[albumId] = (data.albums[albumId] || 0) + 1;
      data.artists[artistId] = (data.artists[artistId] || 0) + 1;
    });
    const sortDescending = (a, b) => b.count - a.count;
    const sortedData = {
      songs: Object.entries(data.songs)
        .map(([id, count]) => ({ id, count }))
        .sort(sortDescending),
      albums: Object.entries(data.albums)
        .map(([id, count]) => ({ id, count }))
        .sort(sortDescending),
      artists: Object.entries(data.artists)
        .map(([id, count]) => ({ id, count }))
        .sort(sortDescending),
    };
    console.log("sorted", sortedData);
    return sortedData;
  }

  function get_wrapped_lists_info(all_playlists) {
    let playlists_info = [];
    let ultimate_list_duplet = false;
    all_playlists.forEach((playlist) => {
      if (
        playlist.name.includes("Your Top Songs") ||
        playlist.name.includes("Dina topplåtar")
      ) {
        playlists_info.push(playlist);
      }
    });
    if (ultimate_list_duplet) {
      playlists_info = [];
      return;
    } else {
      return playlists_info;
    }
  }

  async function get_songs_from_lists(playlists) {
    let songs = [];
    for (const playlist of playlists) {
      songs = songs.concat(await get_songs(playlist.id));
    }
    return songs;
  }

  async function get_songs(playlist_id) {
    const response = await fetch(
      `https://api.spotify.com/v1/playlists/${playlist_id}/tracks`,
      {
        headers: {
          Authorization: "Bearer " + accessToken,
        },
      }
    );
    const data = await response.json();
    const songs = data.items.map((song, index) => ({
      artist: song.track.artists[0].name,
      artist_id: song.track.artists[0].id,
      album: song.track.album.name,
      album_id: song.track.album.id,
      title: song.track.name,
      title_id: song.track.id,
      place_on_list: index + 1,
      playlist_id: playlist_id,
    }));
    return songs;
  }

  async function get_playlists() {
    if (!browser) return;
    if (!accessToken) return;

    const response = await fetch(
      "https://api.spotify.com/v1/me/playlists?limit=50",
      {
        headers: {
          Authorization: "Bearer " + accessToken,
        },
      }
    );
    const data = await response.json();
    try {
      if (data.error) {
        if (data.error.status == 401) {
          need_new_token = true;
          loading_list = 0;
        } else {
          error_message = "Werid, some error occured.";
          loading_list = 0;
        }
      }
    } catch (error) {
      console.error(error);
    }
    return data.items;
  }
</script>

{#if need_new_token}
  <NotAuthed />
{:else}
  <div class="m-2 mt-2 bg-dark-900 p-6 rounded border-2 border-dark-700">
    <div>
      <h3 class="text-3xl text-center font-handwrite tracking-wider my-2">
        Find out more about your listening history
      </h3>
      <h2 class="text-5xl text-center font-heading tracking-wider my-2">
        ANALYZE MUSIC TASTE
      </h2>
      <h5 class=" text-center tracking-wider my-2">
        Summerizes all your years on Spotify and analzes your music taste
        indepth
      </h5>
    </div>
    <div class="flex justify-center items-center m-auto">
      {#if loading_list == 1}
        <button
          class="w-48 h-20 m-2 bg-dark-500 rounded text-center text-ellipsis overflow-hidden hover:bg-dark-500"
          ><strong>Loading...</strong></button
        >
      {:else}
        <button
          on:click={() => generate_list()}
          class="w-48 h-20 m-2 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
          ><strong>ANALYZE ME</strong></button
        >
      {/if}
    </div>
    {#if error_message}
      <p class="text-[#c94242] text-xl m-auto text-center">
        {error_message}
      </p>
    {:else if loading_list == 2}
      <p class="font-heading text-xl font-bold m-auto text-center">
        Your Music
      </p>
      <p class="text-m m-auto text-center">
        Years Analyzed: <strong class="text-sec-400">{total_years}</strong> Songs Analyzed: <strong class="text-sec-400">{total_songs}</strong>
      </p>       
    {/if}
    <DisplayTaste />
    <p />
  </div>
{/if}
