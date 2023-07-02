<script lang="ts">
  import Converter from './lib/Converter.svelte'
</script>
<link rel="stylesheet" href="node_modules/svelte-material-ui/bare.css" />
<link
        rel="stylesheet"
        href="node_modules/svelte-material-ui/themes/svelte.css"
        media="(prefers-color-scheme: light)"
/>
<link
        rel="stylesheet"
        href="node_modules/svelte-material-ui/themes/svelte-dark.css"
        media="screen and (prefers-color-scheme: dark)"
/>
<!-- Hint where we get fonts from. -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />

<!-- Material Icons, Roboto, and Roboto Mono fonts -->
<link
        href="https://fonts.googleapis.com/css2?family=Material+Icons&Roboto+Mono:ital@0;1&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap"
        rel="stylesheet"
/>

<main>
  <h1>One Piece Trading Card Game Deck List Converter</h1>
  <div class="instructions">Get a deck list you want for the sim from <a href="https://onepiecetopdecks.com/deck-list/">here</a> by copying the deck list at the bottom of the page of the deck you want converted</div>
  <h3>
    Convert Deck list into OP-TCG-SIM format
  </h3>
  <Converter />
</main>

<style>
  .instructions {
    padding-left: 1rem;
    padding-right: 1rem;
  }
</style>