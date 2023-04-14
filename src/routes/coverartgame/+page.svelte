<script>
  const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;
</script>

<section class="h-screen text-center">
  <div class="bg-dark-900 w-full h-full fixed -z-30">
    <div class="my-40" />
    <p>Test: {Client_Id}</p>
  </div>
</section>

<style>
</style>
