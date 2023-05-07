<script>
  import { browser } from "$app/environment";

  let accessToken = localStorage.getItem("access_token");

  async function generate_list() {
    const all_playlists = await get_playlists();
    const wrapped_lists_info = get_wrapped_lists_info(all_playlists); // Prev playlists_info
    const all_songs = await get_songs_from_lists(wrapped_lists_info);
    console.log("all", all_songs);
  }

  function get_wrapped_lists_info(all_playlists) {
    let playlists_info = [];
    all_playlists.forEach((playlist) => {
      if (
        playlist.name.includes("Your Top Songs") ||
        playlist.name.includes("Dina topplåtar")
      ) {
        playlists_info.push(playlist);
      }
    });
    return playlists_info;
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
    <button
      on:click={() => generate_list()}
      class="w-48 h-20 m-2 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
      ><strong>GENERATE LIST</strong></button
    >
  </div>
</div>
