<script>
  import axios from "axios";
  import { page } from "$app/stores";
  const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;

  const code = $page.url.searchParams.get("code");
  console.log("Code:", code);

  async function getToken(code) {
    const tokenResponse = await axios.post(
      "https://accounts.spotify.com/api/token",
      {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded",
          Authorization:
            "Basic YjExYzcyZDFiYjFkNDc5OWI5NjZkMjRhMDFhNDNkOTU6NzM1NjZiMzhlOGVjNDUzYzg5NDkzODc3ODA0M2FmNGQK",
        },
        form: {
          grant_type: "client_credentials",
        },
      }
    );
    // console.log("Return:", tokenResponse.data.access_token);
    return tokenResponse.data.access_token;
  }

  // then you can call the function like this:
  const token = getToken(code)
    .then((token) => console.log("tokennnnn:", token))
    .catch((err) => console.log("errrrrrr", err.message));
</script>

<section class="h-full">
  <div class="m-30 m-auto">
    <p>Code: {code}</p>
  </div>
</section>

<style>
</style>
