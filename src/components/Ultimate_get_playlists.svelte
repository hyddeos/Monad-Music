<script>
  import { browser } from "$app/environment";
  let error_message = "";
  let list_created_succesfully = false;
  let loading_list = false;

  let accessToken = "";
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
      const counted_data = analyze_songs(all_songs);
      const reoccuring_songs = get_reoccuring_songs(counted_data.songs);
      // -- add more filters here --
      await create_playlist(reoccuring_songs);
    } else {
      loading_list = false;
      console.error("Playlist already created or didt find new wrapped lists");
      error_message =
        "Playlist already created or we did not find any playlists to gather data from";
    }
  }

  async function create_playlist(songs) {
    if (!browser) return;
    if (!accessToken) return;

    const playlistResponse = await fetch(
      `https://api.spotify.com/v1/me/playlists`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${accessToken}`,
        },
        body: JSON.stringify({
          name: "My Ultimate List -- By ESH",
          description:
            "This list is based on songs that you have played a lot over the years aswell as your most played songs from each year",
          public: false,
        }),
      }
    );
    const playlistData = await playlistResponse.json();
    const playlistId = playlistData.id;

    // Add tracks to the playlist;
    const trackUris = songs.map((id) => `spotify:track:${id}`);
    const addTracksResponse = await fetch(
      `https://api.spotify.com/v1/playlists/${playlistId}/tracks`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${accessToken}`,
        },
        body: JSON.stringify({
          uris: trackUris,
        }),
      }
    );
    const addedTracksData = await addTracksResponse.json();
    list_created_succesfully = true;
    loading_list = false;

    console.log("Playlist created:", playlistData);
    console.log("Tracks added:", addedTracksData);
  }

  function get_reoccuring_songs(all_songs) {
    console.log("all", all_songs);
    let songs = [];
    all_songs.forEach((song) => {
      if (song.count > 1) {
        songs.push(song.id);
      }
    });
    return songs;
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
    return sortedData;
  }

  function get_wrapped_lists_info(all_playlists) {
    let playlists_info = [];
    let ultimate_list_duplet = false;
    all_playlists.forEach((playlist) => {
      if (playlist.name.includes("My Ultimate List -- By ESH")) {
        ultimate_list_duplet = true;
      } else if (
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
    return data.items;
  }
</script>

<div class="bg-dark-900 p-6 rounded border-2 border-dark-700">
  <div>
    <h3 class="text-3xl text-center font-handwrite tracking-wider my-2">
      Generate Your
    </h3>
    <h2 class="text-5xl text-center font-heading tracking-wider my-2">
      ULTIMATE PLAYLIST
    </h2>
    <h5 class=" text-center tracking-wider my-2">
      Get a <strong>Ultimate Playlist</strong> based on all your
      <strong>Top Songs</strong> from all previous years!
    </h5>
  </div>
  <div class="flex justify-center items-center m-auto">
    {#if loading_list}
      <button
        class="w-48 h-20 m-2 bg-dark-500 rounded text-center text-ellipsis overflow-hidden hover:bg-dark-500"
        ><strong>Loading...</strong></button
      >
    {:else}
      <button
        on:click={() => generate_list()}
        class="w-48 h-20 m-2 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
        ><strong>GENERATE LIST</strong></button
      >
    {/if}
  </div>
  {#if error_message}
    <p class="text-[#c94242] text-xl m-auto text-center">
      {error_message}
    </p>
  {:else if list_created_succesfully}
    <p class="text-[#42c968] text-xl font-bold m-auto text-center">
      List created succesfully
    </p>
    <p class="text-l m-auto text-center">
      Check out your new playlist on Spotify called:
    </p>
    <p class="text-xl font-bold m-auto text-center">
      <strong>My Ultimate Playlist -- By ESH</strong>
    </p>
  {/if}
  <p />
</div>
