<script>
    import { browser } from "$app/environment";
    import NotAuthed from "../../components/NotAuthed.svelte";
    import UltimateGetPlaylists from "../../components/Ultimate_get_playlists.svelte";
 
    const Client_Id = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
    const Client_Secret = import.meta.env.VITE_SPOTIFY_CLIENT_SECRET;
    const Client_Refresh_token = import.meta.env.VITE_SPOTIFY_REFRESH_TOKEN;
    
    let bgImage = "/bg.jpg";

    let playlists = [];

  </script>
  
  <section id="main_content" class="flex">
    <img id="bg" src={bgImage} alt="consert background" />
    <div class="m-auto pt-[15%]">
        <UltimateGetPlaylists />
    </div>
    
    
  </section>
  
  <style>
    #bg {
      z-index: -2;
      opacity: 0.3;
      min-height: 100%;
      min-width: 1024px;
      width: 100%;
      height: auto;
      position: fixed;
      top: 0;
      left: 0;
    }
  </style>
  