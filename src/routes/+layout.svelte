<script>
  import "../app.postcss";
  import Footer from "../components/Footer.svelte";
  import Header from "../components/Header.svelte";
</script>

<div class="h-screen text-light-200">
  <Header />
  <slot />
  <Footer />
</div>
