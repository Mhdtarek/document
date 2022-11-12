<script lang="ts">
  import Document from "./routes/Document.svelte";
  import Auth from "./routes/Auth.svelte";
  import Home from "./routes/Home.svelte";
  import NotFound from "./routes/NotFound.svelte";
  import { isAuthenticated } from "./routes/Auth.svelte";
  import Router from "svelte-spa-router";
  const routes = {
    // Exact path
    "/": Home,
    "/doc/:userEmail/:docId": Document,
    "*": NotFound,
  };
</script>

{#if !$isAuthenticated}
  <Auth />
{/if}
{#if $isAuthenticated}
  <Router {routes} />
{/if}
