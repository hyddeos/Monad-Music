<script>

  let access_token = localStorage.getItem("access_token");

</script>

<div class="m-auto pt-[15%] z-10 max-w-xl">
  <div
    class="bg-dark-900 p-6 flex flex-col items-center rounded border-2 border-dark-700"
  >
    <h2
      class="text-4xl text-center text-light-200 font-bold tracking-wider my-1"
    >
      {access_token ? "Again, Spotify Authorization" : "First, Spotify Authorization"}
    </h2>
    <p class="text-center m-4">
      { access_token ? "Your connection to Spotify has expired" : "We need to connect to your Spotify account"  }
    </p>
    <a href="/auth">
      <button
        class="m-4 w-28 h-9 bg-prim-500 rounded text-center text-ellipsis overflow-hidden hover:bg-prim-400"
        >{ access_token ? "Reconnect" : "Connect"  }</button
      ></a
    >
    <p class="text-center m-4">
      You might have to do this again if the access token times out.
      After one hour we will need to redo this step if your still here, see you then.
    </p>
  </div>
</div>
