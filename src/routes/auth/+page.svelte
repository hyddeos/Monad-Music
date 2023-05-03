<script>
  import { browser } from "$app/environment";
  import { goto } from "$app/navigation";
  const client_id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const client_secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
  const client_refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;
  const redirectUri = "http://localhost:5173/auth/code/";
  function generateRandomString(length) {
    let text = "";
    let possible =
      "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    for (let i = 0; i < length; i++) {
      text += possible.charAt(Math.floor(Math.random() * possible.length));
    }
    return text;
  }

  async function sha256(data) {
    const encoder = new TextEncoder();
    const buffer = encoder.encode(data);
    const digest = await crypto.subtle.digest("SHA-256", buffer);
    const hashArray = Array.from(new Uint8Array(digest));
    const hashHex = hashArray
      .map((b) => b.toString(16).padStart(2, "0"))
      .join("");
    return hashHex;
  }
  async function generateCodeChallenge(codeVerifier) {
    function base64encode(string) {
      return btoa(String.fromCharCode.apply(null, new Uint8Array(string)))
        .replace(/\+/g, "-")
        .replace(/\//g, "_")
        .replace(/=+$/, "");
    }
    const encoder = new TextEncoder();
    const data = encoder.encode(codeVerifier);
    const digest = await crypto.subtle.digest("SHA-256", data);
    return base64encode(digest);
  }
  let codeVerifier = generateRandomString(128);
  generateCodeChallenge(codeVerifier).then(async (codeChallenge) => {
    let state = generateRandomString(16);
    let scope = "user-read-private user-read-email";
    let args = new URLSearchParams({
      response_type: "code",
      client_id: client_id,
      scope: scope,
      redirect_uri: redirectUri,
      state: state,
      code_challenge_method: "S256",
      code_challenge: codeChallenge,
    });
    /*
    // set cookie with code_verifier
    setCookie("code_verifier", codeVerifier, { path: "/auth" });
    */
    console.log("ARGS:", args);
    if (browser) {
      localStorage.setItem("code_verifier", codeVerifier);
      goto("https://accounts.spotify.com/authorize?" + args);
    }
    // window.location = "https://accounts.spotify.com/authorize?" + args;
  });
</script>

<p>client_id {client_id}</p>
<p>Random string:</p>