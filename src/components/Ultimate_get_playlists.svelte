<script>
  import { browser } from "$app/environment";

  let accessToken = localStorage.getItem("access_token");

  let playlists = [];

  async function generate_list() {
    const all_playlists = await getPlaylists();
    getWrappedLists(all_playlists);
    console.log("all", playlists);
  }

  function getWrappedLists(all_playlists) {
    console.log("lists in", all_playlists);
    all_playlists.forEach((playlist) => {
      if (playlist.name.includes("Your Top Songs")) {
        playlists.push(playlist);
      }
    });
  }

  async function getPlaylists() {
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
    console.log("data", data);
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
