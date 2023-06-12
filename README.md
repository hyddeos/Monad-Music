# SCREEN SHOTS
You can visit the site at [`Monad Music`](https://monad.eshtropy.se/) or see some screens below

<img src="https://github.com/hyddeos/Monad-Music/assets/39992041/75d4053c-1978-4c29-b2e2-2c927918b9de" width="400"/>
<img src="https://github.com/hyddeos/Monad-Music/assets/39992041/688358ea-8ad3-4c24-a062-3efdcdde5d6f" width="400"/>
<img src="https://github.com/hyddeos/Monad-Music/assets/39992041/372b070f-fe00-4580-8bab-f35bc56895da" width="400"/>
<img src="https://github.com/hyddeos/Monad-Music/assets/39992041/24239370-218e-4d6f-979c-d14dba04b45c" width="400"/>

# GET STARTED 

# Spotify Developer

First you will need to create a new app over at [`Spotify Developer`](https://developer.spotify.com/).
For more information about the hows, visist the [`Spotify Developer Documentation`](https://developer.spotify.com/documentation/web-api).

# create-svelte

Everything you need to build a Svelte project, powered by [`create-svelte`](https://github.com/sveltejs/kit/tree/master/packages/create-svelte).

## Creating a project

If you're seeing this, you've probably already done this step. Congrats!

```bash
# create a new project in the current directory
npm create svelte@latest

# create a new project in my-app
npm create svelte@latest my-app
```

## Environment varibles (.env)

Place your .env file a root of your project
```
VITE_SPOTIFY_CLIENT_ID=
VITE_REDIRECT_URL=
```
The client_id you will get from the Spotify app as well as deside your own redirect_url 

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://kit.svelte.dev/docs/adapters) for your target environment.
