<script>
  import { browser } from "$app/environment";
  import { goto } from "$app/navigation";

  const client_id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
  const redirectUri = "http://localhost:5173/";

  let codeVerifier = "";

if (codeVerifier) {
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
        console.log("NEW TOKEN", data.access_token)
      })
      .catch((error) => {
        console.error("Error:", error);
      });
  }
}
else {
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

  codeVerifier = generateRandomString(128);

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

    console.log("ARGS:", args);
    if (browser) {
      localStorage.setItem("code_verifier", codeVerifier);

      goto("https://accounts.spotify.com/authorize?" + args);
    }
  }
};
</script>
