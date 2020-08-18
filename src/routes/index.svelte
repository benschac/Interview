<script>
  import { fade, fly } from "svelte/transition";
  import { onDestroy } from "svelte";
  import { favorites } from "../favorites";

  let value = "";
  let toast = false;

  let gifPromise = [];
  let giphs = { data: [] };

  const apiKey = "VEuPUJdLiO1Qu7xdqC0VkNZ9ZUNDdXOj";
  const giphyURL = "https://api.giphy.com/v1/gifs/search";

  const unsubscribe = favorites.subscribe((favorite) => {
    return favorite;
  });

  onDestroy(unsubscribe);

  async function getGiph() {
    let res;
    let json;
    res = await fetch(
      `${giphyURL}?api_key=${apiKey}&q=${value}&limit=25&offset=0&rating=g&lang=en`
    );
    json = await res.json();
    if (res.ok) {
      return json.data;
    } else {
      throw Error("Please try again");
    }
  }

  function handleMouseOver(e) {
    e.target.play();
  }

  function addToFavorites(gif) {
    toast = true;
    favorites.update((favorites) => [...favorites, gif]);
    setTimeout(() => {
      toast = false;
    }, 1000);
  }

  function handleSearch() {
    gifPromise = getGiph();
  }
</script>

<style>
  .gifs {
    display: flex;
    flex-wrap: wrap;
    width: max(500px, 40em);
    margin: 0 auto;
    list-style: none;
  }

  .gif {
    margin: 10px;
  }

  .gif video {
    width: 300px;
    height: 300px;
    object-fit: cover;
  }

  .input-wrapper {
    display: flex;
    justify-content: center;
  }

  .input-wrapper input {
    margin-right: 4px;
    padding: 4px;
  }

  .toast {
    position: fixed;
    top: 0;
    left: 0;
  }

  h3 {
    text-align: center;
  }
</style>

<div class="input-wrapper">
  <input placeholder="Search Giphy" bind:value />
  <button on:click={handleSearch}>submit</button>
</div>

<div class="toast">
  {#if toast}
    <h3 transition:fly>
      Added {$favorites[$favorites.length - 1].title} to favorites
    </h3>
  {/if}
</div>

{#await gifPromise}
  <h3>Loading...</h3>
{:then gifs}
  <ul out:fly in:fade={{ duration: 850 }} class="gifs">
    {#each gifs as gif}
      <li on:click={() => addToFavorites(gif)} class="gif">
        <video
          poster={gif.images.original_still.url}
          src={gif.images.looping.mp4}
          on:mouseover={handleMouseOver}>
          <track
            default
            kind="captions"
            srclang="en"
            src={gif.images.looping.mp4} />
          Sorry, your browser doesn't support video.
        </video>
      </li>
    {/each}
  </ul>
{/await}
