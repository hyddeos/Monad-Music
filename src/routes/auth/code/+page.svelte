<script>
  import { browser } from "$app/environment";
  import { goto } from "$app/navigation";
  import { claim_svg_element } from "svelte/internal";
  let code;
  let codeVerifier;
  const redirectUri = "http://localhost:5173/auth/code/";
  const client_id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  if (browser) {
    const urlParams = new URLSearchParams(window.location.search);
    code = urlParams.get("code");
    codeVerifier = localStorage.getItem("code_verifier");
    const body = new URLSearchParams({
      grant_type: "authorization_code",
      code: code,
      redirect_uri: redirectUri,
      client_id: client_id,
      code_verifier: codeVerifier,
    });
    fetchAccessToken(body);
  }
  async function fetchAccessToken(body) {
    const response = fetch("https://accounts.spotify.com/api/token", {
      method: "POST",
      headers: {
        "Content-Type": "application/x-www-form-urlencoded",
      },
      body: body,
    })
      .then((response) => {
        if (!response.ok) {
          throw new Error("HTTP status " + response.status);
        }
        return response.json();
      })
      .then((data) => {
        localStorage.setItem("access_token", data.access_token);
      })
      .catch((error) => {
        console.error("Error:", error);
      });
  }
</script>

<h1>{code}</h1>